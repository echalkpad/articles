# A Linear-Time Sorting Algorithm for FPGAs

[Original URL](http://hackaday.com/2016/01/20/a-linear-time-sorting-algorithm-for-fpgas/)

> Sorting. It's a classic problem that's been studied for decades, and it's a great first step towards "thinking algorithmically." Over the years, a handful of...

Sorting. It's a classic problem that's been studied for decades, and it's a great first step towards "thinking algorithmically." Over the years, a handful of sorting algorithms have emerged, each characterizable by it's _[asymptotic order](https://en.wikipedia.org/wiki/Big_O_notation)_, a measure of how much longer an algorithm takes as the problem size gets bigger. While all sorting algorithms take longer to complete the more elements that must be sorted, some are slower than others.

For a sorter like _bubble sort_, the time grows quadradically longer for a linear increase in the number of inputs; it's of order `O(N²)`.With a faster sorter like _merge-sort_, which is `O(N*log(N))`, the time required grows far less quickly as the problem size gets bigger. Since sorting is a bit old-hat among many folks here, and since `O(N*log(N))` seems to be the generally-accepted baseline for top speed with a single core, I thought I'd pop the question: can we go faster?

In short -- yes, we can! In fact, I'll claim that we can sort in linear time, i.e a running time of `O(N)`. There's a catch, though: to achieve linear time, we'll need to build some custom hardware to help us out. In this post, I'll unfold the problem of sorting in parallel, and then I"ll take us through a linear-time solution that we can synthesize at home on an FPGA.

Need to cut to the chase? Check out the full solution implemented in SystemVerilog on [GitHub](https://github.com/Poofjunior/fpga_fast_serial_sort). I've wrapped it inside an SPI communication layer so that we can play with it using an everyday microcontroller.

To understand how it works, join us as we embark on an adventure in designing algorithms for hardware. If you're used to thinking of programming in a stepwise fashion for a CPU, it's time to get out your thinking cap!

## A Parallel Approach

Our goal is to deliver a module of digital logic that receives its data serially, or, once per clock cycle, and sorts that data. To achieve a linear-time benchmark, I've opted for sorting the data _while it's being clocked in_. In that sense, once we've finished clocking in our data, our array should be sorted immediately afterwards, ready to be clocked out again.

If we can construct such a block, then our hardware solution is linear, or `O(N)`. The problem might seem complicated, but the solution is actually rather simple -- provided that we're doing our sorting in parallel. To unpack how this algorithm works, let's look at a simple case: sorting a three-element array.

### Example Time: the Three-Sorter

Let's start with a quick example of sorting a three-element array into a container of size three.

![3-sorter](https://hackadaycom.files.wordpress.com/2016/01/3-sorter.png?w=400&h=240)

On the left we've got our unsorted list ready to be clocked in serially; on the right, our "Three-Sorter" sorting unit with a cell array of size three. (An _e_ indicates that the cell is empty.) Inside the Three-Sorter, I'll declare that elements in this cell array from top-to-bottom are stored in increasing size. Our goal is to put each new element in the right spot based on what's currently inside the cell array. At this point, we don't actually know any details about the signals that will make this sorting happen, but we'll walk through the flow of data and build up that logic afterwards.

#### Sorting Step 1:

![insert_step_1_simple](https://hackadaycom.files.wordpress.com/2016/01/insert_step_1_simple.png?w=400&h=149)

In Step 1, all cells are empty. We start by inserting a new element, but we need a place to put it. Since all cells are empty, this new element is the smallest we've seen so far, so let's insert it at the top. Even though there's just one element, I'll make a bold claim that our cell array is sorted.

#### Sorting Step 2:

![insert_step_2_simple](https://hackadaycom.files.wordpress.com/2016/01/insert_step_2_simple.png?w=400&h=149)

In Step 2, we try to insert the second element. Our first question might be: where does this element fit in the current container? Looking at all the cells, we can see that this second element _4_ is greater than the elements in all other occupied cells. _4_ fits into any of the empty slots after _3_, but we'll put it in the first empty slot _after_ the last occupied slot. Hence, our new array is still sorted.

#### Sorting Step 3:

![insert_step_3_simple](https://hackadaycom.files.wordpress.com/2016/01/insert_step_3_simple.png?w=400&h=149)

In Step 3, we'll insert the third element, _3_. Looking at our container of cells, we know that _3_ fits nicely between _2_ and _4_, but there's simply no empty space. To make room, we'll kick _4_ downwards into the next cell and put _3_ in _4_'s old spot. (Sorry, _4_, but it's for science!) Now that we've inserted _3_ -- guess what -- our internal cells are still sorted!

Taking a step back, every time we insert a new element, we look at each cell and ask ourselves: "Does my new data fit here?" Because we've agreed beforehand that the contents of our cell array will always be sorted, it turns out that every cell can answer this question independently without knowing the contents of the rest of the cells. All it needs to do is ask for a few bits of information from the cell above it.

Alas, if only these cells were "intelligent" enough to act on their own and ask questions about their data and their neighbor's data. If they could, then I claim that all cells could independently agree on who gets to hold the new element as each element gets clocked in.

### Signals for Parallel Sorting

Now that we've seen cell-sorting in action, let's revisit this example while we take a deeper look at how these cells communicate. Without knowing the contents of the rest of the cells, all cells need to independently agree where the new element goes so that our new element gets stored once and only once. Let's see this "cell-talk" in action.

#### Detailed Sorting Step 1:

![insert_step_1_detailled](https://hackadaycom.files.wordpress.com/2016/01/insert_step_1_detailled.png?w=400&h=200)

In Step 1, we start by inserting _2_. Here, all cells are empty, so any spot would be fair game. Since _2_ is the smallest element we've seen so far, it needs to go in the top cell. The top cell also needs a way to tell all other cells that it just got dibs on the incoming _2_. We don't want to have a "cell-claimed" signal that propagates all the way down the chain; that's just too slow. Rather, we'll make a deal across all cells. If a cell is empty, it will _only_ claim the incoming element if its above neighboring cell is occupied. Now, when new data arrives, each empty cell just needs to look above at the cell on top of it to see if that cell is occupied. If it is, that empty cell claims the new data. If it isn't, then we'll assume that some empty cell above it already has dibs. To bootstrap the first cell into always claiming the first element of an incoming array, we'll hard wire it's "above-cell-is-occupied" signal to True. (That's the fake grey cell in the diagram above.)

This "deal" we just made needs to be so strict that we'll call it "Rule Number 1."

- **_Rule No. 1:_** _if a cell is empty, it will only claim the incoming element if the above cell is not empty._

#### Detailed Sorting Step 2:

![insert_step_2_detailed](https://hackadaycom.files.wordpress.com/2016/01/insert_step_2_detailed.png?w=400&h=200)

In Step 2, a second element, _4_, arrives to be sorted. Here, we know that _4_ should go after _2_, but how do our cells know? If a cell is occupied, it needs to ask the question: "is this incoming new number less than my current number?" If it is, then that cell will kick out its current number and replace it with the new number. That current number can get kicked down to the next cell below it. Hmm, if every occupied cell asks that same question, then there could be multiple cells that would be more than happy to replace their current number with the new number. If that were true, then we risk storing multiple copies of an input element. Well, we can't have that, so we'll make another rule.

- **_Rule No. 2:_** _If a cell is occupied, it will claim the incoming element if both the incoming element is less than the stored element AND the occupied cell above this cell is not kicking out its element._

Phew! Now only one copy of the incoming element will be stored in the entire cell array.

To make sure that we aren't losing data when cells kick out their data, we'll make another rule.

- **_Rule No. 3:_** _If the cell above the current cell kicks out it's stored element, then the current cell MUST claim the above cell's element, regardless of whether or not the current cell is empty or occupied._

If a cell is empty -- phew -- it'll just follow "Rule Number 1" and "Rule Number 3."

#### Detailed Sorting Step 3:

![insert_step_3_detailed](https://hackadaycom.files.wordpress.com/2016/01/insert_step_3_detailed.png?w=400&h=200)

Now, let's try to insert a _3_ as in the last example. _3_ should fit nicely in _4_'s spot, but how do our cells know? The top cell is holding a _2_, and since two is less than three, the _2_ stays put. The second cell, however, is holding a _4_, and since three is less than four, _4_ gets the boot and needs to go down a cell. The first empty cell could take the new data since it's empty, but since _4_ just got kicked out from the cell above, this empty cell gets the _4_ instead. All other empty cells below follow "Rule Number 1". Each of these cells is empty and each cell above these cells was also empty at the time, so they stay empty.

We'll make one last rule for cells that claim new data:

- **_Rule No. 4:_** _If a cell is occupied and accepts a new element (either from the above cell or from the incoming data), it must kick out its current element._

With all our other rules, we can guarantee that any element that gets kicked out will be caught by the cell below.

If all of our cells follow these rules, then we can guarantee that, together, they can sort arrays without needing to know about the cell contents of all other cells, because it is implicit to the cell array structure. And with that said, each sorting step is fast, bubbling down to _N_ identical and independent questions asked in parallel at every clock cycle.

### Behold–a Sorting Cell

Now that we've ironed out the rules, let's look at the anatomy of a single sorting cell, each of which makes up a unit in our cell array.

![sorting_cell](https://hackadaycom.files.wordpress.com/2016/01/sorting_cell1.png?w=449&h=209)

To follow the rules we just generated, each cell needs to know information about it's upstream cell and pass along information about it's current state and data. In a nutshell, these signals just become inputs and outputs to a collection of internal digital logic. To "follow the rules" of our architecture, we'll need to resort to a small snippet of sequential and combinational logic.

First, each cell needs to keep track of its own state, _empty_ or _occupied_. In SystemVerilog, we can implement this feature as a two-state state machine. To make determine whether or not we'll claim the new data, previous data, or hold on to our current data, we'll need to follow the rules, which I've wrapped up as one block of combinational logic.

First, we'll need some intermediate signals:

```
assign new_data_fits = (new_data < cell_data) || (cell_state == EMPTY);
assign cell_data_is_pushed = new_data_fits & (cell_state == OCCUPIED);
```

`new_data_fits` is simply our criteria for whether or not a cell can accommodate new data.

`cell_data_is_pushed` tells us whether or not the current cell data in an occupied cell is getting kicked out to the lower level.

Now, we can put together the true nuts and bolts of our sorting cell: the logic for determining what data gets stored.

```
if (reset)
 begin
 cell_data <= 'b0; // actual reset value is irrelevant since cell is empty
 end
 else if (enable)
 begin
//{shift_up, new_data_fits, prev_cell_data_pushed, cell_state, prev_cell_state}
 casez (priority_vector)
 'b0?1??: cell_data <= prev_cell_data;
 'b0101?: cell_data <= new_data;
 'b0?001: cell_data <= new_data;
 'b1????: cell_data <= next_cell_data;
 default: cell_data <= cell_data;
 endcase
 end
 else
 begin
 cell_data <= cell_data;
 end
```

The block above is a Verilog case statement with "don't cares" making some signals take priority over others. The first case is just "Rule Number 3" where a cell always takes the previous cell's contents if that number is kicked out. The second case is for a cell that is currently occupied, but satisfies the "strictly-less-than" criteria. The third case is for storing the biggest element yet in the first empty cell, or "Rule Number 1" with "Rule Number 3." The final case isn't part of the algorithm, but it's for shifting our data back up the chain when we want to retrieve our array of sorted elements.

Perfect! Now that our sorting cells are encoded to follow the rules, we simply create an array of these with a Verilog `generate` statement, and our linear-time sorter is complete! Now, give it a go by [uploading the source code](https://github.com/Poofjunior/fpga_fast_serial_sort) to real FPGA hardware.

## Getting Edgy with Edge Cases

Before celebrating, it's worth bringing up some edge cases. What happens if our unsorted array has repeats? Can we sort fewer elements than the maximum capacity of our hardware sorter? Without transforming this post into a novel, I'll reassure you that this solution does indeed handle repeats and also sorts arrays from size 0 up to _N_, where _N_ is the total number of sorting cells that we've synthesized onto our FPGA. It can't however, swap back and forth between sorting arrays of signed or unsigned data types without tweaking a few parameters in the source code first. All that said, I encourage you: _be skeptical!_ Don't believe me until you worked these edge cases out for yourself on paper or in simulation.

## Trading Time for Space

We did it! We've built our very own sorting peripheral in hardware that chews through unsorted arrays and sorts them in linear time! But, as with all too-good-to-be-true scenarios, we've paid a hefty price in FPGA resources. While we may have an ultra-fast sorter built in hardware, I can only instantiate sorting hardware to sort 8-bit arrays up to ~1000 elements long before I've exhausted my FPGA's resources. We've traded time for FPGA macrocells. Luckily, the amount of hardware grows linearly as we increase the number of sorting cells to sort larger arrays.

## Putting on your "Algorithms-in-Hardware" Hat

![parameterized_length](https://hackadaycom.files.wordpress.com/2016/01/parameterized_length.png?w=684&h=181)

I snagged the image above from Atera's RTL Viewer after synthesizing a Ten-Sorter, and something is strange. As I grow the size of the sorting hardware or change the data type from 8-bit to 16-bit to 32-bit integer by tweaking some top-level parameters, I lose the ability to see all of the connections and signals passing back and forth in my head. It's just too complicated. There are too many wires.

Nevertheless, the design itself bubbles down to a very simple unit, the _sorting cell_, that repeats itself many times over with each repeat performing the exact same task. Because we know the behavior of each of these sorting cells, we can predict the behavior of an entire system made up from these blocks -- even if we can't visualize all of the connections in our heads! This type of procedurally generated complexity strikes me as very beautiful, the same kind of beauty that we'd find in fractals or recursive algorithms. If you've made it this far, thanks for joining me, and keep us all posted on your adventures in the world of "algorithms in hardware."
