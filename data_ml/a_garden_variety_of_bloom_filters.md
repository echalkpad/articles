# A Garden Variety of Bloom Filters

[Original URL](http://matthias.vallentin.net/blog/2011/06/a-garden-variety-of-bloom-filters/)

> In this article, I explain how Bloom filters work and introduce several variants that evolved as a result of extensive academic treatment of this topic. Moreover, I present libbf, an implementation...

In this article, I explain how **Bloom filters** work and introduce several variations that evolved as a result of extensive academic treatment of this topic. Moreover, I introduce my own implementation of these Bloom filters, [libBf](http://matthias.vallentin.net/libbf/), a header-only [C++11](http://en.wikipedia.org/wiki/C%2B%2B0x) library that comes with a BSD-style licence.

> Whenever you have a set or list, and space is an issue, a Bloom filter may be a useful alternative.<br>
> –Mitzenmacher

## Introduction

A [Bloom filter](http://en.wikipedia.org/wiki/Bloom_filter) is a randomized [synopsis data structure](http://www.pittsburgh.intel-research.net/people/gibbons/talks-surveys/Synopsis-Data-Structures-Gibbons-Matias.pdf) that allows for set membership queries. Its space-efficient representation comes at the cost of _false positives_, i.e., elements can erroneously be reported as members of the set. In practice, the huge space savings often outweigh the false positives if kept at a sufficiently low rate.

Bloom filters have received a great deal of attention not only by the research community but also in practice. For example, [Google Chrome](http://src.chromium.org/viewvc/chrome/trunk/src/chrome/browser/safe_browsing/bloom_filter.h?view=log) uses a Bloom filter to represent a blacklist of dangerous URLs. Each time a user is about to navigate to new page, the corresponding URL is mangled, hashed, and then compared to a local Bloom filter that represents the set of all malicious URLs. If the Bloom filter reports that the URL is in the set, the browser sends the hash of the URL to the Safebrowsing server to confirm that the URL is indeed malicious to compensate a potential false positive. That is, all checks are performed locally, but when the user surfs to a malicious URL, an extra round trip to the Safebrowsing server occurs.

Another example is the squid web proxy which uses Bloom filters to represent [cache digests](http://wiki.squid-cache.org/SquidFaq/CacheDigests), which caching servers use to periodically exchange the objects they contain. There are many more examples of Bloom filter applications, for instance in peer-to-peer applications, routing protocols, [IP traceback](http://en.wikipedia.org/wiki/IP_traceback), resource location, etc. Broder and Mitzenmacher give a [good survey of network applications](http://www.eecs.harvard.edu/~michaelm/postscripts/im2005b.pdf).

## Bloom Filters

Before we delve into the discussion, let us agree on some common notation.

## Terminology

- Universe
- distinct items
- independent hash functions
- Vector of cells, i.e.,
- Set:
- Multiset / Stream:

  - where and
  - counters of
  - multiplicity (frequency) of

- Bloom filter estimate denoted by hat:
- Probability of a false positive (FP):
- Probability of a false negative (FN):
- Capacity , i.e., is the maximum number of items a Bloom filter can hold until a given can no longer be guaranteed
- A Bloom filter is _full_ when then number of added items exceeds

## Basic

![The basic Bloom filter devised by Burton Bloom. To insert an item x, we set the corresponding positions in the bit vector to 1](http://matthias.vallentin.net/blog/2011/06/bf-basic.png) Burton Bloom introduced the original Bloom filter in 1970, which we refer to as the **basic Bloom filter**. It uses a bit vector with and independent hash functions that map items in to the range . (Unlike in the implementation, we start at index 1 for the formal treatment.) All bits in are initialized to 0\. To insert an item , we set the bits at positions in to 1\. To test whether an item is a member of , we examine the bits at positions in . If any of these bits is 0 we report . Otherwise we report that , although there remains some probability that . This type of error is a _false positive_ (FP) and also known as _Bloom error_ . It occurs because other elements in also map to the same positions.

To compute the probability of a Bloom error, we start off with an empty bit vector and insert an item. This is the same as independently (and uniformly) choosing bits and setting them to 1\. Thereafter, the probability that a certain bit in is still 0 is

Afer insertions, the probability that a certain bit is 1 is

Testing for membership involves hashing an item times. Thus the probability of a Bloom error is

For fixed parameters and , the optimal value that minimizes this probability is

For , we have hence . Moreover, for a desired FP probability we can compute the number of required bits by substituting the optimal value of :

## Multisets

A basic Bloom filter can only represent a set, but neither allows for querying the multiplicities of an item, nor does it support deleting entries. We use the term _counting Bloom filter_ to refer to variants of Bloom filters that represent multisets rather than sets. In libBf, we call a counting Bloom filter a basic Bloom filter with width parameter .

### Counting

![Each cell in the counting Bloom filter has a fixed bit width w. To insert an item x, we increment the counters C_x. To remove an item y, we decrement its counters C_y](http://matthias.vallentin.net/blog/2011/06/bf-counting.png) In a [counting Bloom filter](http://pages.cs.wisc.edu/~cao/papers/summarycache.html), inserting an item corresponds to incrementing a counter. Some variants also feature a decrement operation to remove item from a set. As soon we allow for deletions we introduce false negative (FN) errors. These occur when removing an item that itself was a FP. The probability of a FN is bounded by .

When we get the count of an item , we compute its set of counters and return the minimum value as frequency estimate . This query algorithm is also known as _minimum selction_ (MS).

There are two main issues with counting Bloom filters:

1. Counter overflows
2. Choosing

The first problem exists when we the counter value reaches and cannot be incremented any more. The most natural thing to do is simply not continuing to count rather than overflowing and restarting at 0\. However, this introduces _undercounts_, which we also refer to as FNs.

The second problem concerns the choice of the width parameter . If we choose very large, the space gains of a Bloom filter diminish. There will also be a lot of unused space, manifesting as unused zeros. If we choose too small, we will reach the maximum counter value to fast. Choosing the right value is a difficult trade-off that also depends on the data. It has been shown that for uniform distributions a value of works well.

### Bitwise

![The bitwise Bloom filter consists of l counting Bloom filters, each of which represent w_i orders of magnitude of the entire counter. This Figure illustrates a bitwise Bloom filter with w_i = 1.](http://matthias.vallentin.net/blog/2011/06/bf-bitwise.png) The [bitwise Bloom filter](ftp://ftp.cs.rochester.edu/pub/papers/theory/07.tr927.Bitwise_bloom_filter.pdf) is a combination of counting Bloom filters with bit vectors , each of which have cells, hash functions, and width where . It aims at solving both of the overflow and space problem of the counting Bloom filter.

To add an item , we first look at the counters in the first level . If there is enough room (i.e., width) available, we simply perform the increment. If the counter overflows, we insert into and remove it from . In this fashion, the counter value is unbounded as we can always add more levels. However, the item has to be hashed times with a total of hash functions.

In order to read the counter of an item, we combine the binary representation of all the levels. Let be the counter value at level . Then we compute the counter value as

### Spectral

The [spectral Bloom filter](http://theory.stanford.edu/~matias/papers/sbf_tech_report.pdf) is an optimized version of the counting Bloom filter. It consists of two extra algorithms in addition to MS and introduces a more space-efficient data structure to represent counters.

1. Let us review the MS algorithm. When querying an item , MS uses the minimum counter value as frequency estimate, i.e., . Cohen and Matias claim that and for all .

2. The second spectral algorithm is an optimization for the add operation. When adding an item to the Bloom filter, the _minimum increase_ (MI) algorithm only increments the minimum counter value(s) . The rationale behind this is that is always the most accurate count, thus MI results in the fewest possible increment operations.

  Because not all counters are incremented on inserts, the effect of deletes is significantly worse and the number of FNs becomes unbounded. Thus, the MI algorithm should not be used when removing of items from a Bloom filter. Cohen and Matias claim that and that if is drawn uniformly from , then .

3. The third algorithm is _recurring minimum_ (RM) and involves two Bloom filters, and . The key insight behind RM is that items that experience Bloom errors are less likely to have recurring minima counter values. Cohen and Matias found empirically that this applies to approximately 20% of the items. Such items with a unique minimum are maintained in the second Bloom filter to reduce the discrepancy between and .

  To query an item according to the RM algorithm, we look first into the first Bloom filter and check if has a recurring minimum. If so, we return the minimum counter value. Otherwise we look the minimum counter value from the second Bloom filter, unless it is 0\. If it is 0 (i.e., does not exist), we return the minimum counter from the first Bloom filter.

  Since all the items are inserted into the first bloom filter, the RM optimization does at least as well as the MS algorithm, yet has usually better error rates because a second filter holding fewer items is used for items which experience higher error rates.

The fancy data-structure takes space, where is the number of distinct items and . For details, please refer to [the paper](http://theory.stanford.edu/~matias/papers/sbf_tech_report.pdf).

## Aging

A problem all the above Bloom filter variants is that they eventually fill up over time when dealing with a large set or stream of data. This means that at some point the Bloom filter becomes unusable due to its high error rates. There exist various scenarios where one would like to "age out" old items that have been added a long time ago. For example, we might want to estimate only recent items or we have a very limited amount of space available.

Although counting Bloom filters have a delete operation, it is often impossible to retain old items in memory. Thus we do not know their counter positions in the bit vector anymore, otherwise we would simply decrement their count. What we want is a Bloom filter that has _sliding window_ semantics, as illustrated by the Figure below.

![In a sliding window scenario, an insert operation for a new item x_7 would ideally delete an old item x_0.](http://matthias.vallentin.net/blog/2011/06/sliding-window.png)

To support a sliding window, we would like to have Bloom filter that acts like a FIFO. In the following, we discuss two different Bloom filter flavors that aim at providing this property.

### Stable

The [stable Bloom filter](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.85.1569) is essentially a basic Bloom filter with an underlying bit vector with a fixed cell width . However, counters do not represent the multiplicities of the items but rather their age. Thus the interface supports only set membership queries.

To insert an item, we decrement cells chosen uniformly at random. Thereafter, we set the counters of all cells to their maximum value of .

Deng and Rafiei have shown that the fraction of zeros will eventually become constant. When having reached this _stable point_, the probability of a Bloom error is

### A

<sup>2</sup>

The [Bloom filter](http://portal.acm.org/citation.cfm?id=1685986), also known as **active-active buffering**, provides another type of FIFO. It uses two single-bit vectors and where . Unlike the spectral RM algorithm, one Bloom filter is not a subset of the other, so an item can be in either Bloom filter.

To query for an item, we return true if exists in either or . To insert an item , we simply return if it already exists in . Otherwise we insert it in and test whether has reached its capacity. If it is full, we flush and swap and . Thereafter we insert the item in (the old ).

One advantage of the Bloom filter is space-efficiency, since one bit vector is always full. Let the subscript denote the value of the Bloom filter. The probability of a Bloom error is

and the optimal value for and are:

## libBf

As part of a class project for the course [Combinatorial Algorithms and Data Structures](http://www.cs.berkeley.edu/~satishr/cs270/) in Spring 2011 at UC Berkeley, I decided to write a [C++11](http://en.wikipedia.org/wiki/C%2B%2B0x) **lib**rary of **B**loom **f**ilters, [libBf](http://matthias.vallentin.net/libbf), which implements the above discussed Bloom filters. [Slides](http://matthias.vallentin.net/course-work/cs270-s11.pdf) of the final presentation are also available; they go a little deeper into the algorithmic details.

I only presented a few Bloom filter types in this article, but active research in this field yielded many more variations. For example, the [dynamic](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.151.8477) and [scalable](http://gsd.di.uminho.pt/members/cbm/ps/dbloom.pdf) Bloom filter are two variants that grow dynamically as soon as more items are added. Bloom filters can also be [compressed](http://www.eecs.harvard.edu/~michaelm/NEWWORK/postscripts/cbf.pdf), e.g., when sending them over the network. [Distance-sensitive](http://www.siam.org/proceedings/alenex/2006/alx06_004akirsch.pdf) Bloom filters give more than a binary answer or count when asking for an item: they also return if an item is close to another item in the set. Finally, there exist [Bloomier](http://webee.technion.ac.il/~ayellet/Ps/nelson.pdf) filters which extend the set membership query model and counting notion to computations of arbitrary functions.

[Cuckoo hashing](http://en.wikipedia.org/wiki/Cuckoo_hashing) is a related space-efficient alternative to Bloom filters. Moreover, Adam Langley has some [interesting thoughts](http://www.imperialviolet.org/2011/04/29/filters.html) on Golomb Compressed Sets.
