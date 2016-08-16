# Golang: channels implementation

[Original URL](http://dmitryvorobev.blogspot.com/2016/08/golang-channels-implementation.html)

> Introduction Go is getting more and more popular nowadays, one of the reasons for that is great support of concurrency. Channels and goroutines simplify programming of concurrent applications. There...

<span>Introduction</span>

<span>Go is getting more and more popular nowadays, one of the reasons for that is great support of concurrency. Channels and goroutines simplify programming of concurrent applications. There are several articles that give an insight into how Go data structures are implemented, for example: </span>

[<span>slices</span>](https://blog.golang.org/go-slices-usage-and-internals)

<span>, </span>

[<span>maps</span>](https://www.goinggo.net/2013/12/macro-view-of-map-internals-in-go.html)

<span>, </span>

[<span>interfaces</span>](http://research.swtch.com/interfaces)

<span>, however there is little information about the implementation of Go channels. In this article you will explore how channels work and how they are implemented. (If you have never used channels in Go I’d recommend to read </span>

[<span>this</span>](https://golang.org/doc/effective_go.html#channels)

<span>.)</span>

## <span>Channel structure</span>

<span>Let’s start with the definition of channel structure:</span>

<span>
  <img src="https://lh6.googleusercontent.com/bdpItqCYPIZYi5Kz_lxzjDNjyldj6nWhjJA-Y23jTYW6ShKXY5BVdnEsmW7r4yyoYAnY7fpZLAH4RRq5cOnXz-gzzXf_yLQ4pxdpb7SoUvXYz2caqPz--o2tA11JB47Br5hK-AQO" alt="1.png" width="114">
</span>

****<br>

<span>- qcount - number of elements in the buffer</span>

<span>- dataqsiz - buffer capacity</span>

<span>- buf - pointer to a buffer for channel elements</span>

<span>- closed - flag that shows whether the channel closed or not</span>

<span>- recvq - pointer to a linked list of goroutines waiting to receive elements from the channel</span>

<span>- sendq - pointer to a linked list of goroutines waiting to send elements to the channel</span>

<span>- lock - mutex for concurrent access to the channel</span>

****<br>

<span>In general goroutine acquires the lock when it performs an action on the channel, except in cases where it does lock free checks in non-blocking function call (I’ll explain more about that later). Closed - is a flag that is set to 1 when channel is closed and 0 when it’s not. These fields will be omitted from the pictures for clarity.</span>

****<br>

<span>A channel can be synchronous (unbuffered) or asynchronous (buffered). Let’s first see how synchronous channels work.</span>

## <span>Synchronous channels</span>

<span>Given the following sample of code:</span>

****<br>

<span>package</span>

<span> main</span>

****<br>

<span>func</span>

<span> main</span>

<span>() {</span>

<span>ch</span>

<span>:=</span>

<span> make</span>

<span>(</span>

<span>chan </span>

<span>bool)</span>

<span>go</span>

<span> func</span>

<span>() {</span>

<span>
</span>

<span>ch</span>

<span>&lt;- </span>

<span>true</span>

<span>  }()</span>

<span>  &lt;-ch</span>

****<br>

<span>New channel is created, and it looks like this:</span>

<span>
  <img src="https://lh3.googleusercontent.com/wGVK7lyXg7N8f7i5K5kBlQz5-HfCsovXaB5LvAF9lJi0xEGoep6P4dhXG9_zK-endSl4bX5MJCbU5cEDYiTKEGwR3WSVVE4O6fNXm4-JeATkcsNGqX1O658eRIioXWHzaldsjazE" alt="4.png" width="114">
</span>

****<br>

<span>Go doesn’t allocate a buffer for synchronous channels, so the pointer to buffer is nil and `dataqsiz` is zero. In the code above Go doesn’t guarantee what happens first - receive from the channel or send to the channel. Let’s assume that the first action would be receiving from the channel (Reverse order of operations is covered in the paragraph about buffered channels). First of all, the running goroutine does some checks, such as: checking if channel is closed, whether it’s buffered or not, if it contains goroutines in the send queue. In this situation the channel has neither a buffer nor queued senders, so the goroutine adds itself to the `recvq` and blocks. After this step the channel looks like this:</span>

****<br>

<span>
  <img src="https://lh4.googleusercontent.com/IjyXfz04fty1QOdqSY-1_sa1ebkNrH5DpYxBjcyKKepSd1ZtVj53zfjolQTr8Kt-6lOMbcTfFloRd-KjXo_ZhI1FthmkOS6sYrUeHIChVP42K7Hgh_O3oCvHGFcO2ajPFDmjN4lC" alt="2.png" width="387">
</span>

****<br>

<span>Now there is only one goroutine that is ready to run and that goroutine tries to send data to the channel. All the checks are repeated again and when goroutine checks `recvq`, it finds there the waiting goroutine, removes it from the receive queue and writes the data to the waiting goroutine’s stack and awakes it. This is the only place in Go runtime where one goroutine writes to the stack of another goroutine. After this step the channel looks like it was after initialization. Both goroutines return and the program terminates.</span>

****<br>

<span>That is all about synchronous channels. Now let’s have a look at buffered channels. </span>

## <span>Buffered channels</span>

<span>Consider the following example: </span>

****<br>

<span>package</span>

<span> main</span>

****<br>

<span>func</span>

<span> main</span>

<span>() {</span>

<span>ch</span>

<span>:=</span>

<span> make</span>

<span>(</span>

<span>chan </span>

<span>bool</span>

<span>,</span>

<span>1)</span>

<span>ch</span>

<span>&lt;- </span>

<span>true</span>

<span>go</span>

<span> func</span>

<span>() {</span>

<span>   &lt;-ch</span>

<span>
</span>

<span>}()</span>

<span>ch</span>

<span>&lt;- </span>

<span>true</span>

****<br>

<span>Again the order of goroutine execution is unknown, the example with receiving from the channel as the first operation was covered in the previous paragraph, so here let’s assume that two elements are sent to the channel and after that one of the elements is received. The first step is to create the channel, it looks like this:</span>

****<br>

<span>
  <img src="https://lh4.googleusercontent.com/7geuBbT10YTh6dZYLrv4-8yjsWLSDC9sUjahHmgg_wBMOY567g-9IId6slX4R6vz_qGoPuy6O16lZIk9Vb2f2ZaZV0m_ToQ_kS5Ru7raCdmgF0wy4WJzB07QbhdLMR93fEcIG1xU" alt="6.png" width="352">
</span>

****<br>

<span>The difference in comparison with synchronous channels is that Go allocates a buffer and sets the `dataqsiz` field to one. </span>

<span>Next step is to send the first value to the channel. To perform this action, the goroutine does several checks like: checking whether `recvq` is empty, if the buffer is empty, whether there is free space in the buffer.</span>

<span>In this case there is free space in the buffer and there are no goroutines receiving from the channel, so it just writes the element to the buffer, increments `qcount` and continues execution. The channel looks like this:</span>

<span>
  <img src="https://lh4.googleusercontent.com/OOIM8lY7wdbv80kNchfI--etCSzrtvZ1xPejcdu6GTKcHSAOEZG8zyTgTItB7Yl-EDQIQdzIycQyM7byuqb4zh5l0TuNyoWMjLKffMylpzTqHaBMMaHnZbLkbxMeSlMWr0cju5Nv" alt="5.png" width="352">
</span>

****<br>

<span>In the next step, the main goroutine sends the second value to the channel. When the buffer is full, a buffered channel behaves like a synchronous (unbuffered) channel, meaning that the goroutine adds itself to the waiting list and blocks, as a result the channel structure looks like this:</span>

****<br>

<span>
  <img src="https://lh4.googleusercontent.com/SD8IU46bWb5SSvdLGs8HNPFwovZXepQBCX58p9mhRuOVA3B1x1O4kJcrYfJy7KOcpAb-VVMXSff-DevqR4VwCk0R2m5Rk6Pw3bnoT7YhG-7Sf8EPxu-gKKS4iPZuljNdKC-hqEGh" alt="3.png" width="377">
</span>

****<br>

<span>Now the main goroutine is blocked and Go runs the anonymous goroutine that receives a value from the channel. Here comes the tricky part. Go guarantees that a channel works as FIFO queue (</span>

[<span>specification</span>](https://golang.org/ref/spec#Channel_types)

<span>), but the goroutine cannot just get the value from the buffer and continue execution. In that case the main goroutine would block forever. To handle this, the running goroutine reads a value from the buffer, then appends the value from the first waiting goroutine to the buffer and unlocks the waiting goroutine and removes it from the queue. (In the case of no queued senders it will simply read the first entry from the buffer). </span>

## <span>Select</span>

<span>But wait, Go supports the select statement with a default case, and if a channel blocks how would it execute the default case? </span>

<span>Good question. Let’s have a brief look at private channels’ API. When you run the following piece of code:</span>

****<br>

<span>select:</span>

<span>case</span>

<span>&lt;-</span>

<span>chan:</span>

<span>foo</span>

<span>()</span>

<span>default:</span>

<span>    bar</span>

<span>()</span>

****<br>

<span>Go executes a function with the following signature:</span>

****<br>

<span>func chanrecv</span>

<span>(</span>

<span>t </span>

<span>*</span>

<span>chantype</span>

<span>,</span>

<span> c </span>

<span>*</span>

<span>hchan</span>

<span>,</span>

<span> ep </span>

<span>unsafe</span>

<span>.</span>

<span>Pointer</span>

<span>,</span>

<span> block </span>

<span>bool</span>

<span>)</span>

****<br>

<span>`chantype` is the type of the channel (e.g. bool for make(chan bool)), `hchan` is a pointer to the channel structure, `ep` is a pointer to a segment of memory where data from the channel should be written, and the last but most interesting for us parameter is the `block` flag. If it’s set to `false` the function is running in non-blocking mode. In non-blocking mode a goroutine checks the buffer, the waiting queue and returns `true` and writes the data to `ep` or returns `false` if there is no data in the buffer or senders in the queue. Buffer and queue checks are done with atomic operations instead of locking the mutex.</span>

<span>There is also a function to send data to a queue with the similar signature.</span>

****<br>

<span>Receive and send operations have been covered, now let’s have a look at what happens when a channel is closed.</span>

## <span>Closing a channel</span>

<span>Closing a channel is a simple operation, Go iterates through all the senders and receivers in the queues and then unlock them. All receivers get default values, for the data type, from the channel and all the senders panic.</span>

## <span>Summary</span>

<span>In this article you have explored how channels are implemented and work. I tried to describe this as simple as possible, so omitted some details. The goal of the article is to provide a basic understanding of Go channels and to encourage you to read the source code if you want to get a more complete understanding. Just check channels’ </span>

[<span>source code</span>](https://golang.org/src/runtime/chan.go)

<span>. I have found it simple, well-documented and quite short, just around 700 lines of code.</span>

## <span>Resources:</span>

<span>
  <a href="https://golang.org/ref/spec#Channel_types">Channels in Go specification</a>
</span>
