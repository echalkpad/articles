# A walk through the SA-IS algorithm - Screwtape's Notepad

[Original URL](http://zork.net/~st/jottings/sais.html)

> Screwtape's Notepad A walk through the SA-IS Suffix Array Construction Algorithm Some time ago, while looking for solutions to some string-searching problem I was having, I stumbled across the Suffix...

## Screwtape's Notepad

## A walk through the SA-IS Suffix Array Construction Algorithm

Some time ago, while looking for solutions to some string-searching problem I was having, I stumbled across the [Suffix Array](http://en.wikipedia.org/wiki/Suffix_array) data-structure. It seemed promising, so I looked up the algorithm Wikipedia recommended (the "SA-IS" algorithm from the paper "Linear Suffix Array Construction by Almost Pure Induced-Sorting" by G. Nong, S. Zhang and W. H. Chan). I downloaded the paper from [the authors' site](https://code.google.com/p/ge-nong/) and I read through it.

Unfortunately, the paper was aimed at an audience of Computer Science researchers well-versed in the literature of the field, rather than an industry programmer with some spare-time and curiosity, so it made almost no sense to me. I decided I would research this research, figure out how the SA-IS algorithm worked, and write up the most accessible explanation I could muster.

Before we begin, though, some caveats:

- I will explain the ideas behind the SA-IS algorithm, but I won't go into detail about _why_ they work. For fully-worked proofs, see the original paper.
- At any point where I have to choose between an efficiently-implemented example and a simple-to-explain example, I'll pick simplicity. You can probably come up with practical optimisations yourself.
- Although SA-IS can be generally applied to strings with an alphabet of any size, the code shown here is specialised to strings of 8-bit bytes, because in practice that's what most people will want to do.
- The SA-IS algorithm itself is simple enough, but requires storage for intermediate values. To keep things simple I'll ignore storage costs, but G. Nong's follow-up paper, "Practical Linear-Time O(1)-Workspace Suffix Sorting for Constant Alphabets" discusses how to build a suffix-array in linear-time and constant (working) space (obviously you need O(n) space to store the resulting sorted array). It's basically the same as the SA-IS algorithm but it's smarter about storing intermediate values.
- All the code examples are written to work with Python 3, and specifically tested against Python 3.4.

## What's a suffix array?

A suffix array is an index over a string that helps you find all the times some smaller string occurs inside it, if at all. A suffix array for a string with N characters will generally be an array of N integers, where each item of the array stores an offset into the string, representing the suffix of the string starting at that offset and continuing (as suffixes do) to the end.

The important thing about a suffix-array is that it represents a _sorted_ sequence of suffixes, so you can find some particular suffix--or all the suffixes beginning with some particular string--by using an ordinary binary search.

Here's a simple Python function that generates a suffix array:

```
>>> def naivelyMakeSuffixArray(source):
... """
... A naive, slow suffix-array construction algorithm.
... """
...
... # Construct a list of suffixes of the source string.
... suffixes = []
... for offset in range(len(source) + 1):
... suffixes.append(source[offset:])
...
... # Sort the suffixes
... suffixes.sort()
...
... # Calculate the start offset of each suffix, storing them in
... # sorted order into the suffix array.
... suffixArray = []
... for suffix in suffixes:
... offset = len(source) - len(suffix)
... suffixArray.append(offset)
...
... return suffixArray
```

If we feed a string like `cabbage` into this algorithm, we get the correct suffix array in return:

```
>>> naivelyMakeSuffixArray(b'cabbage')
[7, 1, 4, 3, 2, 0, 6, 5]
```

The string contains two instances of the character `a`, at indexes 1 and 4, and therefore two suffixes starting with `a`. Sure enough, the indexes 1 and 4 are grouped together in the suffix array. Index 1 comes first, because that suffix begins with `ab`, which is alphabetically before index 4's `ag`.

Note that although the indexes of the characters run 0 to 6, for a total of seven characters, the suffix array has _eight_ offsets. The suffix beginning just after the last character and continuing to the end of the string is the empty suffix, containing no characters. Because it's the shortest possible suffix, it always winds up sorted to the beginning of the suffix array. Most people who want to use suffix arrays don't care about the empty suffix, but it turns out to be important for the SA-IS algorithm.

The naive algorithm above just re-uses a standard O(n log n) sorting algorithm, plus it adds the overhead of building a list of suffixes and building a new array of offsets at the end. SA-IS manages to do the whole thing in O(n) time, faster than just the standard sorting on its own, a massive improvement. To achieve this speed, SA-IS takes advantage of certain features of suffix arrays that are not necessarily true of any old array of strings.

## Concepts in SA-IS

Before we get into the details of the SA-IS algorithm, we need to cover the concepts that SA-IS is built on.

## S-type and L-type suffixes

When sorting an array of suffixes of some string, SA-IS divides them into two groups: "S-type" suffixes and "L-type" suffixes. S-type suffixes are _smaller_ (in the sorting sense) than the suffix to their right (and so must appear closer to the start of the finished suffix array) and L-type suffixes are _larger_ than the suffix to their right (and so appear closer to the end).

Let's take the string `cabbage` again. The suffix starting at offset 1 is `abbage`; the suffix starting to the right of it is `bbage`, and `abbage` comes before `bbage` alphabetically, so `abbage` is an S-type suffix. Meanwhile, the suffix starting at offset 5 is `ge`; the suffix starting to the right is `e`, and `ge` comes after `e`, so `ge` is an L-type suffix.

We can figure out whether the suffix starting at some particular place is S-type or L-type by comparing the character at that place with the character to the right, but what if both characters are the same? For example, the suffix at offset 2 is `bbage`, and the suffix to the right is `bage`. You could just use a normal string comparison, but imagine if one suffix was "a million `b`s followed by `age`" and the other suffix was "a million and one `b`s followed by `age`"--that comparison would get expensive quickly! However, because both are suffixes of the same string, it turns out that when a character is the same as the character to its right, then its type is the same as the other character too. `bage` turns out to be L-type, and so `bbage` is also L-type.

Therefore, if we know whether the suffix at some offset is L-type or S-type, we can easily calculate the type of every suffix to the left of that offset by starting at that point and walking left, comparing characters and storing types. But what about the right-most suffix? It doesn't have a right-hand suffix to compare to, so we can't figure out whether it's larger or smaller! Well, in SA-IS the right-most suffix is always the empty suffix, and it's defined to always be S-type.

Now we have a complete set of rules for determining the type of each suffix of a string:

1. The empty suffix at the end of the string is S-type by definition, and therefore the penultimate suffix (containing just the last character) is always L-type (since it's alphabetically after the empty suffix).
2. If the character at offset X is alphabetically larger than the character at offset X+1, the suffix starting at offset X is L-type.
3. If the character at offset X is the same as the character at offset X+1, and the suffix starting at offset X+1 is L-type, then the suffix starting at offset X is also L-type.
4. Otherwise, the suffix starting at offset X is S-type.

If we define some values to represent "S-type" and "L-type":

```
>>> S_TYPE = ord("S")
>>> L_TYPE = ord("L")
```

...we can make a function that takes a string and maps out whether each character of the source string as S-type or L-type, following the above rules.

```
>>> def buildTypeMap(data):
... """
... Builds a map marking each suffix of the data as S_TYPE or L_TYPE.
... """
... # The map should contain one more entry than there are characters
... # in the string, because we also need to store the type of the
... # empty suffix between the last character and the end of the
... # string.
... res = bytearray(len(data) + 1)
...
... # The empty suffix after the last character is S_TYPE
... res[-1] = S_TYPE
...
... # If this is an empty string...
... if not len(data):
... # ...there are no more characters, so we're done.
... return res
...
... # The suffix containing only the last character must necessarily
... # be larger than the empty suffix.
... res[-2] = L_TYPE
...
... # Step through the rest of the string from right to left.
... for i in range(len(data)-2, -1, -1):
... if data[i] > data[i+1]:
... res[i] = L_TYPE
... elif data[i] == data[i+1] and res[i+1] == L_TYPE:
... res[i] = L_TYPE
... else:
... res[i] = S_TYPE
...
... return res
```

The `buildTypeMap()` function stores its map as a `bytearray`, so if we want to print out a type-map we'll need to decode it into text first:

```
>>> def showTypeMap(data):
... print(data.decode('ascii'))
... print(buildTypeMap(data).decode('ascii'))
```

If we look at the type map of `cabbage`, we can see the S-type and L-type suffixes are just as we expected:

```
>>> showTypeMap(b'cabbage')
cabbage
LSLLSLLS
```

Note again that the type-map is longer than the input text, because it's storing an entry for the empty suffix.

## LMS characters

Let's say that some particular character in our input string is an "S character" if an S-type suffix starts at that location (and likewise for an "L character"). A "left-most S character" (or "LMS character" for short), is just an S character that has an L character to its immediate left. The first character in the string can never be an LMS character (because there's no character to the immediate left, let alone an L character), but apart from that the definition is straightforward. Therefore, we can very easily write a function to implement this test:

```
>>> def isLMSChar(offset, typemap):
... """
... Returns true if the character at offset is a left-most S-type.
... """
... if offset == 0:
... return False
... if typemap[offset] == S_TYPE and typemap[offset - 1] == L_TYPE:
... return True
... 
... return False
```

...and we can also extend our `showTypeMap()` function to point out which characters in the string are LMS characters:

```
>>> def showTypeMap(data):
... typemap = buildTypeMap(data)
... 
... print(data.decode('ascii'))
... print(typemap.decode('ascii'))
... 
... print("".join(
... "^" if isLMSChar(i, typemap) else " "
... for i in range(len(typemap))
... ))
```

Now we can see where the LMS characters are in `cabbage`:

```
>>> showTypeMap(b'cabbage')
cabbage
LSLLSLLS
 ^ ^ ^
```

Note that as a side-effect of our definition, the empty suffix is regarded as an LMS character, even though it's not a character as such.

If we try a word that has consecutive S characters, we can see that only the left-most S character is marked as an LMS character:

```
>>> showTypeMap(b'caabage')
caabage
LSSLSLLS
 ^ ^ ^
```

## LMS substrings

Looking at the examples above, you can see that LMS characters are somewhat sparse throughout the input string. An "LMS substring" is a portion of the input string starting at one LMS character and continuing up to (but not including) the next LMS character. In the string `cabbage` examined above, there are two LMS substrings: `abb` and `age`. The SA-IS algorithm does its magic by sorting LMS substrings, but we can't use an ordinary string comparison function because we don't necessarily know how long each LMS string is. We'd need to walk along the string to find the beginning of the next LMS character, and if we're walking the string anyway, we might as well do the comparison at the same time.

The rules for comparing LMS substrings are straightforward: they must have the same length and the same characters in the same order. Here's a function that, given an input string, its typemap and two offsets into that string, determines whether the LMS substrings starting at those offsets are equal:

```
>>> def lmsSubstringsAreEqual(string, typemap, offsetA, offsetB):
... """
... Return True if LMS substrings at offsetA and offsetB are equal.
... """
... # No other substring is equal to the empty suffix.
... if offsetA == len(string) or offsetB == len(string):
... return False
...
... i = 0
... while True:
... aIsLMS = isLMSChar(i + offsetA, typemap)
... bIsLMS = isLMSChar(i + offsetB, typemap)
...
... # If we've found the start of the next LMS substrings...
... if (i > 0 and aIsLMS and bIsLMS):
... # ...then we made it all the way through our original LMS
... # substrings without finding a difference, so we can go
... # home now.
... return True
...
... if aIsLMS != bIsLMS:
... # We found the end of one LMS substring before we reached
... # the end of the other.
... return False
...
... if string[i + offsetA] != string[i + offsetB]:
... # We found a character difference, we're done.
... return False
...
... i += 1
```

This function doesn't explicitly test for reaching the end of the string, but remember that the empty suffix at the end of the string is always regarded as an LMS character--if `i + offsetA` reaches the end of the string, then `aIsLMS` must be true, and the code will always take one of the first two `if` statements in the body of the `while` loop.

Let's take a string with some repeated content:

```
>>> showTypeMap(b'rikki-tikki-tikka')
rikki-tikki-tikka
LSLLLSLSLLLSLSLLLS
 ^ ^ ^ ^ ^ ^
```

You can see the LMS substrings at offset 1 and offset 7 both have the same content (`ikki`) and the same types (`SLLL`). Therefore, those two LMS substrings should be equal:

```
>>> s = b'rikki-tikki-tikka'
>>> tm = buildTypeMap(s)
>>> lmsSubstringsAreEqual(s, tm, 1, 7)
True
```

On the other hand, the LMS substring at offset 13 has the same length and the same types, but different content (`ikka`), so it should be reported as unequal:

```
>>> lmsSubstringsAreEqual(s, tm, 1, 13)
False
```

## Bucket sorting

Bucket sorting is a common operation even outside the world of suffix arrays, but since it's important to SA-IS here's a brief description. Since we're making a sorted suffix array, we know that all the suffixes beginning with the same character will wind up grouped together. The string `cabbage` has two `a`s, two `b`s, one `c`, and no `d`s, so we can already predict that the first two indexes in the suffix array will point at suffixes beginning with `a`, the `b`s will take the next two slots, then one for `c` and none for `d`.

Given a string (and the size of its alphabet), the following function will figure out how many suffixes of the string begin with each character of the alphabet; colloquially, how many suffixes are in the "bucket" for each character:

```
>>> def findBucketSizes(string, alphabetSize=256):
... res = [0] * alphabetSize
...
... for char in string:
... res[char] += 1
...
... return res
```

We could find the bucket sizes for `cabbage` using the traditional ASCII encoding for each letter, but ASCII is a 128-character alphabet, and that would make for an awkwardly-long list. Instead, let's make up our own encoding where `a` = 0, `b` = 1, `c` = 2, etc.:

```
>>> encoded_cabbage = [2, 0, 1, 1, 0, 6, 4]
```

Because `g` is the alphabetically largest letter in our string, we only need 7 characters in our alphabet to spell it. And so, our bucket sizes are:

```
>>> findBucketSizes(encoded_cabbage, 7)
[2, 2, 1, 0, 1, 0, 1]
```

As predicted, the `a` bucket has two suffixes, `b` has two, `c` has one, `d` has none, and so on.

Now that we know how big each bucket is, it's simple to derive an array where each character's index points at the beginning of the corresponding bucket in the suffix array:

```
>>> def findBucketHeads(bucketSizes):
... offset = 1
... res = []
... for size in bucketSizes:
... res.append(offset)
... offset += size
...
... return res
>>> cabbage_buckets = findBucketSizes(encoded_cabbage, 7)
>>> findBucketHeads(cabbage_buckets)
[1, 3, 5, 6, 6, 7, 7]
```

The empty suffix always winds up at the beginning of the suffix array in index 0, so the `a` bucket begins at index 1\. There's two `a`s so the `b` bucket begins at 3, and so on. Note that the buckets for `d` and `e` both begin at index 6, but there's no `d` suffixes so that won't cause a problem.

We can easily find the indexes of the end of each bucket in a similar fashion:

```
>>> def findBucketTails(bucketSizes):
... offset = 1
... res = []
... for size in bucketSizes:
... offset += size
... res.append(offset - 1)
...
... return res
>>> findBucketTails(cabbage_buckets)
[2, 4, 5, 5, 6, 6, 7]
```

And now, having covered all the background material, let's talk about the algorithm itself.

## The SA-IS Algorithm

The SA-IS algorithm's name comes from the fact that it produces a Suffix Array by Induced Sorting. What is "Induced Sorting"? Well, the tricky part of building a suffix array turns out to be the LMS suffixes. If you imagine a simple string like "AAAAA", it's very easy to create a sorted suffix list because all the suffixes are L-type, so you can just list them out. LMS suffixes are the knots in the string that make the problem hard, so if you have them properly sorted it's easy to slot all the L-type and other S-type suffixes into place.

At a very high level, the SA-IS algorithm looks like this (don't worry about the functions we haven't defined yet, we'll get to them in the following sections):

```
>>> def makeSuffixArrayByInducedSorting(string, alphabetSize):
... """
... Compute the suffix array of 'string' with the SA-IS algorithm.
... """
...
... # Classify each character of the string as S_TYPE or L_TYPE
... typemap = buildTypeMap(string)
...
... # We'll be slotting suffixes into buckets according to what
... # character they start with, so let's precompute that info now.
... bucketSizes = findBucketSizes(string, alphabetSize)
...
... # Use a simple bucket-sort to insert all the LMS suffixes into
... # approximately the right place the suffix array.
... guessedSuffixArray = guessLMSSort(string, bucketSizes, typemap)
...
... # Slot all the other suffixes into guessedSuffixArray, by using
... # induced sorting. This may move the LMS suffixes around.
... induceSortL(string, guessedSuffixArray, bucketSizes, typemap)
... induceSortS(string, guessedSuffixArray, bucketSizes, typemap)
...
... # Create a new string that summarises the relative order of LMS
... # suffixes in the guessed suffix array.
... summaryString, summaryAlphabetSize, summarySuffixOffsets = \
... summariseSuffixArray(string, guessedSuffixArray, typemap)
...
... # Make a sorted suffix array of the summary string.
... summarySuffixArray = makeSummarySuffixArray(
... summaryString,
... summaryAlphabetSize,
... )
...
... # Using the suffix array of the summary string, determine exactly
... # where the LMS suffixes should go in our final array.
... result = accurateLMSSort(string, bucketSizes, typemap,
... summarySuffixArray, summarySuffixOffsets)
...
... # ...and once again, slot all the other suffixes into place with
... # induced sorting.
... induceSortL(string, result, bucketSizes, typemap)
... induceSortS(string, result, bucketSizes, typemap)
...
... return result
```

As an aid to illustrating the algorithm in action, we'll use the following function to show the state of a suffix array in progress. We'll store -1 in suffix array elements that are uninitialised, and since we'll be demonstrating this algorithm on short strings, we can get away with assuming each offset will be one or two digits long (the same length as "-1") and render intermediate states of a suffix array like this:

```
>>> def showSuffixArray(arr, pos=None):
... print(" ".join("%02d" % each for each in arr))
...
... if pos is not None:
... print(" ".join(
... "^^" if each == pos else " "
... for each in range(len(arr))
... ))
```

This shows all the offsets in a suffix array:

```
>>> showSuffixArray([2, -1, 4])
02 -1 04
```

...and it also allows an individual cell in the array to be highlighted, to show the progress of whatever part of the algorithm we're looking at:

```
>>> showSuffixArray([2, -1, 4], 2)
02 -1 04
 ^^
```

## The first guess

We don't yet know exactly where our LMS suffixes should go in our sufffix array, so we start by putting them in approximately the right place with a bucket sort. All else being equal, a longer suffix (one occurring earlier in the string) sorts after a shorter suffix (one occurring later), so we'll go through the string from left-to-right and stack each LMS suffix we find at the tail-end of its bucket.

```
>>> def guessLMSSort(string, bucketSizes, typemap):
... """
... Make a suffix array with LMS-substrings approximately right.
... """
... # Create a suffix array with room for a pointer to every suffix of
... # the string, including the empty suffix at the end.
... guessedSuffixArray = [-1] * (len(string) + 1)
...
... bucketTails = findBucketTails(bucketSizes)
...
... # Bucket-sort all the LMS suffixes into their appropriate bucket.
... for i in range(len(string)):
... if not isLMSChar(i, typemap):
... # Not the start of an LMS suffix
... continue
...
... # Which bucket does this suffix go into?
... bucketIndex = string[i]
... # Add the start position at the tail of the bucket...
... guessedSuffixArray[bucketTails[bucketIndex]] = i
... # ...and move the tail pointer down.
... bucketTails[bucketIndex] -= 1
...
... # Show the current state of the array
... showSuffixArray(guessedSuffixArray)
...
... # The empty suffix is defined to be an LMS-substring, and we know
... # it goes at the front.
... guessedSuffixArray[0] = len(string)
...
... showSuffixArray(guessedSuffixArray)
...
... return guessedSuffixArray
```

So now we can guess the positions of the LMS substrings in our string, leaving all the other positions set to -1:

```
>>> cabbage_buckets = findBucketSizes(b'cabbage')
>>> cabbage_types = buildTypeMap(b'cabbage')
>>> cabbage_guess = guessLMSSort(b'cabbage', cabbage_buckets, cabbage_types)
-1 -1 01 -1 -1 -1 -1 -1
-1 04 01 -1 -1 -1 -1 -1
07 04 01 -1 -1 -1 -1 -1
```

Because `guessLMSSort()` calls `showSuffixArray()` at various points of the process, we can trace its operation:

- the first LMS suffix it finds is `abbage` at index 1 in the string, so it puts that at the end of the `a` bucket
- the next LMS suffix it finds is `age` at index 4, so it puts that immediately before `abbage`
- we hit the end of the source string, so we add the empty suffix at position 0 in the suffix array

To fill in the rest of the positions, we're going to use "induced sorting"; that is, determining where to put the other suffixes based on what's already in the array.

## Induced sorting: L-type suffixes

Now that we have the LMS suffixes in our suffix array, we can extrapolate where all the other suffixes go. We start by scanning through our temporary suffix array and for each listed suffix, we check the suffix to the _left_ of it in the original string - if that's L-type, we'll bucket-sort that one too.

```
>>> def induceSortL(string, guessedSuffixArray, bucketSizes, typemap):
... """
... Slot L-type suffixes into place.
... """
... bucketHeads = findBucketHeads(bucketSizes)
...
... # For each cell in the suffix array....
... for i in range(len(guessedSuffixArray)):
... if guessedSuffixArray[i] == -1:
... # No offset is recorded here.
... continue
...
... # We're interested in the suffix that begins to the left of
... # the suffix this entry points at.
... j = guessedSuffixArray[i] - 1
... if j < 0:
... # There is no useful information in the typemap at this
... # location; skip it.
... continue
... if typemap[j] != L_TYPE:
... # We're only interested in L-type suffixes right now.
... continue
...
... # Which bucket does this suffix go into?
... bucketIndex = string[j]
... # Add the start position at the head of the bucket...
... guessedSuffixArray[bucketHeads[bucketIndex]] = j
... # ...and move the head pointer up.
... bucketHeads[bucketIndex] += 1
...
... showSuffixArray(guessedSuffixArray, i)
```

If we feed this function our guess from before, we can watch it propagate L-type suffixes into the array:

```
>>> induceSortL(b'cabbage', cabbage_guess, cabbage_buckets, cabbage_types)
07 04 01 -1 -1 -1 06 -1
^^ 
07 04 01 03 -1 -1 06 -1
 ^^ 
07 04 01 03 -1 00 06 -1
 ^^ 
07 04 01 03 02 00 06 -1
 ^^ 
07 04 01 03 02 00 06 05
 ^^
```

Tracing through the output above, we can see what it's done:

- we start at the first cell in the suffix array, which represents the empty suffix at the end of the string
- the character before the empty suffix is `e` at offset 6, so we slot 6 into the `e` bucket
- the next cell has the suffix `age` at offset 4, and the suffix to the left of it is the L-type suffix `bage` at offset 3, so we slot 3 into the `b` bucket
- next is `abbage` at offset 1, offset 0 has the L-type suffix `cabbage`, so we slot 0 into the `c` bucket
- we find `bage` at offset 3, which we stored two steps ago - but the suffix at offset 2 (`bbage`) is still L-type, so we slot it into the `b` bucket
- we find `bbage` at offset 2, but the suffix to its left is not L-type so we move on
- we find `cabbage` at offset 0, but there's nothing to its left, so we move on
- we find `e` at offset 6, and the suffix at offset 5 (`ge`) is L-type, so we slot it into the `g` bucket
- we find `ge` at offest 5, but the suffix to its left is not L-type, so we're done

If we compare the above output to the result of the naive algorithm...

```
>>> showSuffixArray(naivelyMakeSuffixArray(b'cabbage'))
07 01 04 03 02 00 06 05
```

...we can see that we already have something fairly close to the correct answer. That's partially because of the power of induced sorting, but also because `cabbage` is a pretty easy string to begin with: it only has two S-type suffixes, and they're both LMS suffixes. Let's investigate a more complex string:

```
>>> baa = b'baabaabac'
>>> showTypeMap(baa)
baabaabac
LSSLSSLSLS
 ^ ^ ^ ^
```

This string has six S-type suffixes, only four of which are LMS-suffixes. If we duplicate what we did with `cabbage` on this new string...

```
>>> baa_buckets = findBucketSizes(baa)
>>> baa_types = buildTypeMap(baa)
>>> baa_guess = guessLMSSort(baa, baa_buckets, baa_types)
-1 -1 -1 -1 -1 01 -1 -1 -1 -1
-1 -1 -1 -1 04 01 -1 -1 -1 -1
-1 -1 -1 07 04 01 -1 -1 -1 -1
09 -1 -1 07 04 01 -1 -1 -1 -1
>>> induceSortL(baa, baa_guess, baa_buckets, baa_types)
09 -1 -1 07 04 01 -1 -1 -1 08
^^ 
09 -1 -1 07 04 01 06 -1 -1 08
 ^^ 
09 -1 -1 07 04 01 06 03 -1 08
 ^^ 
09 -1 -1 07 04 01 06 03 00 08
 ^^
```

Here, we can see that at this point, there's still some uninitialised cells in the suffix array. That's not suprising, we haven't touched the S-type suffixes yet.

## Induced sorting: S-type suffixes

Having scanned through the suffix array from left-to-right slotting in L-type suffixes, we now scan from right-to-left slotting in S-type suffixes. This is basically a mirror-image of the previous function:

```
>>> def induceSortS(string, guessedSuffixArray, bucketSizes, typemap):
... """
... Slot S-type suffixes into place.
... """
... bucketTails = findBucketTails(bucketSizes)
...
... for i in range(len(guessedSuffixArray)-1, -1, -1):
... j = guessedSuffixArray[i] - 1
... if j < 0:
... # There is no useful information in the typemap at this
... # location; skip it.
... continue
... if typemap[j] != S_TYPE:
... # We're only interested in S-type suffixes right now.
... continue
...
... # Which bucket does this suffix go into?
... bucketIndex = string[j]
... # Add the start position at the tail of the bucket...
... guessedSuffixArray[bucketTails[bucketIndex]] = j
... # ...and move the tail pointer down.
... bucketTails[bucketIndex] -= 1
...
... showSuffixArray(guessedSuffixArray, i)
```

When we apply this next step to our old friend `cabbage`:

```
>>> induceSortS(b'cabbage', cabbage_guess, cabbage_buckets, cabbage_types)
07 04 04 03 02 00 06 05
 ^^
07 01 04 03 02 00 06 05
 ^^
```

`cabbage` only has two S-type suffixes, so we only get two rows of output. As the function sweeps through the suffix array, it winds up swapping the order of the LMS suffixes from "04 01" to "01 04", and this is enough to transform our guessed suffix array into the correct suffix array. That's just a happy coincidence, though. If we process our other example:

```
>>> induceSortS(baa, baa_guess, baa_buckets, baa_types)
09 -1 -1 07 04 07 06 03 00 08
 ^^
09 -1 -1 07 02 07 06 03 00 08
 ^^ 
09 -1 -1 05 02 07 06 03 00 08
 ^^ 
09 -1 01 05 02 07 06 03 00 08
 ^^ 
09 04 01 05 02 07 06 03 00 08
 ^^
```

...it's shuffled around the LMS suffixes, but compare the result to the output of the naive algorithm:

```
>>> showSuffixArray(naivelyMakeSuffixArray(baa))
09 01 04 02 05 07 00 03 06 08
```

There's still a few cells that need to be swapped around.

## Summarise the guessed suffix array

We started with a string of characters in some alphabet, we made an approximate suffix array for that string, and now we will summarise that approximate suffix array with a _new_ string, in a _new_ alphabet, that represents just the most important bits of information: where the LMS suffixes ended up, after `induceSortL()` and `induceSortS()` did their thing.

The summarising works like this: Each LMS suffix of the original string gets a name, based on the order in which those suffixes appear in the guessed suffix array. Or rather, the LMS substring at the _beginning_ of each LMS suffix gets a name: if two LMS suffixes begin with the same LMS substring, they get the same name. These names are combined in the same order as the corresponding suffixes in the original string to form the summary string.

Here's the corresponding implementation:

```
>>> def summariseSuffixArray(string, guessedSuffixArray, typemap):
... """
... Construct a 'summary string' of the positions of LMS-substrings.
... """
... # We will use this array to store the names of LMS substrings in
... # the positions they appear in the original string.
... lmsNames = [-1] * (len(string) + 1)
...
... # Keep track of what names we've allocated.
... currentName = 0
...
... # Where in the original string was the last LMS suffix we checked?
... lastLMSSuffixOffset = None
...
... # We know that the first LMS-substring we'll see will always be
... # the one representing the empty suffix, and it will always be at
... # position 0 of suffixOffset.
... lmsNames[guessedSuffixArray[0]] = currentName
... lastLMSSuffixOffset = guessedSuffixArray[0]
...
... showSuffixArray(lmsNames)
...
... # For each suffix in the suffix array...
... for i in range(1, len(guessedSuffixArray)):
... # ...where does this suffix appear in the original string?
... suffixOffset = guessedSuffixArray[i]
...
... # We only care about LMS suffixes.
... if not isLMSChar(suffixOffset, typemap):
... continue
...
... # If this LMS suffix starts with a different LMS substring
... # from the last suffix we looked at....
... if not lmsSubstringsAreEqual(string, typemap,
... lastLMSSuffixOffset, suffixOffset):
... # ...then it gets a new name
... currentName += 1
...
... # Record the last LMS suffix we looked at.
... lastLMSSuffixOffset = suffixOffset
...
... # Store the name of this LMS suffix in lmsNames, in the same
... # place this suffix occurs in the original string.
... lmsNames[suffixOffset] = currentName
... showSuffixArray(lmsNames)
...
... # Now lmsNames contains all the characters of the suffix string in
... # the correct order, but it also contains a lot of unused indexes
... # we don't care about and which we want to remove. We also take
... # this opportunity to build summarySuffixOffsets, which tells
... # us which LMS-suffix each item in the summary string represents.
... # This will be important later.
... summarySuffixOffsets = []
... summaryString = []
... for index, name in enumerate(lmsNames):
... if name == -1:
... continue
... summarySuffixOffsets.append(index)
... summaryString.append(name)
...
... # The alphabetically smallest character in the summary string
... # is numbered zero, so the total number of characters in our
... # alphabet is one larger than the largest numbered character.
... summaryAlphabetSize = currentName + 1
...
... return summaryString, summaryAlphabetSize, summarySuffixOffsets
```

That's a lot of code, and it's a little bit intricate, but let's look at some examples.

```
>>> (
... cabbage_summary,
... cabbage_summary_alpha_size,
... cabbage_summary_suffix_offsets,
... ) = summariseSuffixArray(b'cabbage', cabbage_guess, cabbage_types)
-1 -1 -1 -1 -1 -1 -1 00
-1 01 -1 -1 -1 -1 -1 00
-1 01 -1 -1 02 -1 -1 00
```

As we saw above, the guessed suffix array of `cabbage` looks like this:

```
>>> showSuffixArray(cabbage_guess)
07 01 04 03 02 00 06 05
```

Of those suffixes, we know the first three are the LMS suffixes; in order, the empty suffix, `abbage` and `age`. Because all three LMS suffixes begin with different LMS substrings, they get three distinct names (0, 1 and 2). Those names are stored in `lmsNames` at the positions where the corresponding suffix occurs in the original string: at offsets 7, 1 and 4 respectively.

After `lmsNames` is calculated, we throw away the unused indices of `lmsNames` to produce the summary string:

```
>>> cabbage_summary
[1, 2, 0]
```

But that's not the only output from our function. We also know that the summary string is written in an alphabet with three distinct characters:

```
>>> cabbage_summary_alpha_size
3
```

...and we have a list that stores the suffix offset associated with each item in the summary string.

```
>>> cabbage_summary_suffix_offsets
[1, 4, 7]
```

But as noted previously, `cabbage` is a pretty simple string. Let's try our more complex example, `baabaabac`:

```
>>> (
... baa_summary,
... baa_summary_alpha_size,
... baa_summary_suffix_offsets,
... ) = summariseSuffixArray(baa, baa_guess, baa_types)
-1 -1 -1 -1 -1 -1 -1 -1 -1 00
-1 -1 -1 -1 01 -1 -1 -1 -1 00
-1 01 -1 -1 01 -1 -1 -1 -1 00
-1 01 -1 -1 01 -1 -1 02 -1 00
```

For this string, two of the LMS suffixes start with the same LMS substring: `aabaabac` and `aabac` both start with `aab` and the types of those characters are `SSL` in both cases. Consequently, both suffixes get the same name (1) and our summary string contains two identical characters:

```
>>> baa_summary
[1, 1, 2, 0]
```

Although this summary string contains four characters, it still only contains three distinct characters, so the alphabet size is still 3:

```
>>> baa_summary_alpha_size
3
```

And we get the suffix offset map for the summary string again:

```
>>> baa_summary_suffix_offsets
[1, 4, 7, 9]
```

## Make a suffix array of the summary

We started with one string and wanted to build a suffix array, and in the process we've build a second string and now we have to make a suffix array of that one too? Isn't that circular reasoning?

Well, almost: it's _recursive_ reasoning.

If we're going to do recursion, there needs to be a base case that's not recursive. If we look at our `cabbage` example above, we got a pretty simple summary string:

```
>>> cabbage_summary
[1, 2, 0]
```

Yes, that particular example happens to be small enough that you could build the suffix array in your head, but there's another interesting feature: we've said it's a string in an alphabet of three characters, and every character in the alphabet appears exactly once, somewhere in that string. Which means we can create a suffix array with our trusty old bucket sort.

If we look at our `baabaabac` example on the other hand, the outlook isn't nearly as rosy:

```
>>> baa_summary
[1, 1, 2, 0]
```

This string is still in an alphabet of three characters, but it's more than three characters long, which means at least one character must be repeated and we can't be sure a bucket sort will do the right thing - so in that situation we'll have to recurse.

It's worth pointing out that while you could in theory have a three-character string in an alphabet of three characters that included repetition (say, `[1, 1, 0]`), our `summariseSuffixArray()` function will never generate such a thing, since it always uses consecutive characters in the alphabet, and it always sets the alphabet size to the number of distinct characters it's used.

```
>>> def makeSummarySuffixArray(summaryString, summaryAlphabetSize):
... """
... Construct a sorted suffix array of the summary string.
... """
... if summaryAlphabetSize == len(summaryString):
... # Every character of this summary string appears once and only
... # once, so we can make the suffix array with a bucket sort.
... summarySuffixArray = [-1] * (len(summaryString) + 1)
...
... # Always include the empty suffix at the beginning.
... summarySuffixArray[0] = len(summaryString)
...
... for x in range(len(summaryString)):
... y = summaryString[x]
... summarySuffixArray[y+1] = x
...
... else:
... # This summary string is a little more complex, so we'll have
... # to use recursion.
... summarySuffixArray = makeSuffixArrayByInducedSorting(
... summaryString,
... summaryAlphabetSize,
... )
...
... return summarySuffixArray
```

We can't test this function on the `baabaabac` summary yet because we haven't finished the recursive implementation, but we can test it on the `cabbage` summary:

```
>>> cabbage_summary_suffix_array = makeSummarySuffixArray(
... cabbage_summary, cabbage_summary_alpha_size,
... )
>>> showSuffixArray(cabbage_summary_suffix_array)
03 02 00 01
```

...and compare that with our naive implementation:

```
>>> showSuffixArray(naivelyMakeSuffixArray(cabbage_summary))
03 02 00 01
```

## Build the real suffix array

At last we begin building the real, final suffix array, based on the suffix array of the summary string. As before, we start by placing the LMS suffixes into the suffix array, and then we can fill in all the others by induced sorting. Unlike before, we don't just bucket-sort them into place in whatever order we find them, we insert them in an order determined by the summary string's suffix array.

Remember, when we built the summary string (where each character was a generated name for an LMS substring), we also built a corresponding `summarySuffixOffsets` array that maps characters from the summary string back to LMS substrings (and hence, LMS suffixes) from the original string, so that's what we're going to use.

The process goes something like this: Each entry in the suffix array of the summary string points at a position in `summary`. We look at the same position in `summarySuffixOffsets` to find the offset of the corresponding LMS suffix of our original string. Then we put that LMS suffix into the correct bucket, trusting in the summary suffix array to put them in the correct order within the bucket.

```
>>> def accurateLMSSort(string, bucketSizes, typemap,
... summarySuffixArray, summarySuffixOffsets):
... """
... Make a suffix array with LMS suffixes exactly right.
... """
... # A suffix for every character, plus the empty suffix.
... suffixOffsets = [-1] * (len(string) + 1)
...
... # As before, we'll be adding suffixes to the ends of their
... # respective buckets, so to keep them in the right order we'll
... # have to iterate through summarySuffixArray in reverse order.
... bucketTails = findBucketTails(bucketSizes)
... for i in range(len(summarySuffixArray)-1, 1, -1):
... stringIndex = summarySuffixOffsets[summarySuffixArray[i]]
...
... # Which bucket does this suffix go into?
... bucketIndex = string[stringIndex]
... # Add the suffix at the tail of the bucket...
... suffixOffsets[bucketTails[bucketIndex]] = stringIndex
... # ...and move the tail pointer down.
... bucketTails[bucketIndex] -= 1
...
... showSuffixArray(suffixOffsets)
...
... # Always include the empty suffix at the beginning.
... suffixOffsets[0] = len(string)
...
... showSuffixArray(suffixOffsets)
...
... return suffixOffsets
```

Note: to make sure our LMS suffixes survive the induced sorting later, we need to insert them at the _end_ of their buckets, so we need to walk backward through `summarySuffixArray` as we insert the suffixes backwards, to maintain their relative order.

Also, we don't process _every_ entry in `summarySuffixArray`: the first entry corresponds to the empty suffix of the summary string, which does not correspond to any suffix of the original string. The second entry corresponds to the empty suffix of the original string, which we can't bucket-sort into place because it's empty. We already know it winds up at the beginning, though, so it's not a problem.

Let's walk through the algorithm as it applies to our old friend `cabbage`. As a reminder, here's the inputs to `accurateLMSSort()`:

```
>>> showSuffixArray(cabbage_summary_suffix_array)
03 02 00 01
>>> showSuffixArray(cabbage_summary)
01 02 00
>>> showSuffixArray(cabbage_summary_suffix_offsets)
01 04 07
```

And now, we can walk through the algorithm.

- The first entry we find in the summary's suffix array (the last entry, since we're walking backwards) is `01`. If we look at offset 1 of `summarySuffixOffsets`, we see this corresponds to the LMS suffix at position 4 of the original string (`age`) so we can slot in that suffix at the end of the `a` bucket.
- The next entry in the summary's suffix array is `00`, offset 0 of `summarySuffixOffsets` corresponds to the LMS suffix at position 1 of the original string (`abbage`), so we slot that suffix into the `a` bucket just before `age`.
- The second-last entry is skipped because it points at the empty suffix of the original string and we can't bucket-sort that into place.
- The last entry is skipped because it represents the empty suffix at the end of the summary string, it doesn't have a corresponding LMS suffix in the original string.
- Finally, we know that the empty suffix (at position 7) will wind up at the beginning of our final suffix array.

When we run our function, we can see the same steps occurring:

```
>>> cabbage_real = accurateLMSSort(b'cabbage', cabbage_buckets,
... cabbage_types, cabbage_summary_suffix_array,
... cabbage_summary_suffix_offsets)
-1 -1 04 -1 -1 -1 -1 -1
-1 01 04 -1 -1 -1 -1 -1
07 01 04 -1 -1 -1 -1 -1
```

`04` is slotted in at the at the end of the `a` bucket, then `01` immediately before that, then finally the empty suffix gets its special place at the beginning.

## Putting it all together

After putting the LMS suffixes into their correct places, all the other suffixes fall into place with a second round of induced sorting, just as before. We've now described all the functions that `makeSuffixArrayByInducedSorting()` calls, so we can invoke it to efficiently build a suffix array of any string we like... along with a detailed log of its inner calculations (omitted here for brevity):

```
>>> showSuffixArray(makeSuffixArrayByInducedSorting(b'cabbage', 256))
-1 -1 01 -1 -1 -1 -1 -1
...
07 01 04 03 02 00 06 05
>>> showSuffixArray(makeSuffixArrayByInducedSorting(baa, 256))
-1 -1 -1 -1 -1 01 -1 -1 -1 -1
...
09 01 04 02 05 07 00 03 06 08
```

But since most people who want to build a sorted suffix array will have a string of bytes, rather than strings of an arbitrary-sized alphabet, let's make a wrapper function with a sensible default:

```
>>> def makeSuffixArray(bytestring):
... return makeSuffixArrayByInducedSorting(bytestring, 256)
```

And now we're done.

```
>>> naivelyMakeSuffixArray(b'cabbage') == makeSuffixArray(b'cabbage')
True
>>> naivelyMakeSuffixArray(baa) == makeSuffixArray(baa)
True
```
