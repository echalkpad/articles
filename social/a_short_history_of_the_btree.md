# A Short History of the BTree

[Original URL](https://www.perforce.com/blog/110928/short-history-btree)

> You can make a reasonable argument that the BTree is the most important data structure and algorithm that has yet been invented in computer science. It surely is one of the longest-lived and most...

You can make a reasonable argument that the BTree is the most important data structure and algorithm that has yet been invented in computer science. It surely is one of the longest-lived and most durable ideas. BTrees are used by every database system in existence, and are also used in file systems, information retrieval applications, and many other places. The Perforce server stores all of your metadata in BTrees, and you know how high-performing and scalable Perforce servers are!

Although BTree implementations date back to the 1960's, the modern history of the BTree begins with Rudolf Bayer and Ed McCreight's paper: [_Organization and maintenance of large ordered indexes_](http://www.springerlink.com/content/n9n0754115251238/). Bayer and McCreight were working at Boeing's Mathematical and Information Sciences Laboratory in the 1960's, trying to develop algorithms for storing and retrieving data from the computers of the time (here's an earlier version of their paper, published as [Mathematical and Information Sciences Report No. 20](http://www.dtic.mil/cgi-bin/GetTRDoc?AD=AD0712079&Location=U2&doc=GetTRDoc.pdf) by the Boeing Scientific Research Laboratories in July, 1970).

Although computers have come a long way in forty years, and the terminology has changed dramatically, the basic concepts of the problem are still the same, and are well stated by Bayer and McCreight:

> We assume that the index itself is so voluminous that only rather small parts of it can be kept in main store at one time. Thus the bulk of the index must be kept on some backup store. The class of backup stores considered are _pseudo random access devices_ which have a rather long access or wait time – as opposed to a true random access device like core store – and a rather high data rate once the transmission of physically sequential data has been initiated. Typical pseudo random access devices are: fixed and moving head discs, drums, and data cells.

The publication of Bayer and McCreight's paper ushered in the First Age of BTrees, and the decade of the 1970's was filled with enthusiastic work to explore, extend, and improve upon the basic ideas. Various teams worked out the details of page formats, key representations, compression and encoding techniques, and so forth. The most important consolidation during this time was the realization that the B+-tree, a variation in which all keys reside in the leaves, and the internal nodes are purely redundant search structures, had substantial benefits compared to the original version; every practical BTree that I have ever studied has been a B+-tree.

All of this work was collected and described by Doug Comer of Purdue University in the paper [_The ubiquitous B-tree_](http://citeseer.ist.psu.edu/viewdoc/summary?doi=10.1.1.96.6637) in 1979.

During the 1980's, the emphasis was on DataBase Management Systems (DBMS), and various vendors and researchers explored the issues of concurrency control and recovery in BTrees. The fundamental concurrency control problem in BTrees is that the access patterns aren't consistent:

- Search-by-key starts at the root of the tree and descends to the leaf.
- Splitting a leaf (due to insert or update) and merging two leaves (due to delete or update) proceeds upwards from the leaf toward the root.
- Retrieving entries in index order moves sideways from leaf to leaf.
- Retrieving entries in descending index order also moves sideways, but in the opposite direction.

This means that the protocols which enable multiple independent transactions to process a BTree simultaneously are complex.

Similar problems arise during recovery: should the DBMS log the low-level physical changes such as split, join, page allocation, and key re-arrangement? Or should it log only the "logical" operations such as insert, update, and delete? Various DBMS implementations solved these problems in different ways; one of the most successful and best-known methods is the [ARIES](http://en.wikipedia.org/wiki/Algorithms_for_Recovery_and_Isolation_Exploiting_Semantics) algorithm.

A very thorough and complete treatment of this Second Age of BTrees can be found in Jim Gray and Andreas Reuter's encyclopedic [_Transaction Processing: Concepts and Techniques_](http://www.amazon.com/Transaction-Processing-Concepts-Techniques-Management/dp/1558601902).

During the 1990's, the Third Age of Btrees found researchers focused on several specialized aspects of BTrees that arose as they were used more universally:

- Some applications found that BTree I/O performance created problems. Maintaining the index in real time results in a high number of random I/Os, which can dramatically impact the throughput on most storage media, such as hard disks. Techniques such as [_Log-Structured Merge-Trees_]() combined ideas from log-structed file systems into the BTree algorithms, and dramatically improved high-end performance.
- Others looked at the notion of "versioned" BTrees, which don't overwrite entries when changing them, but rather construct new indexes that can include multiple versions of the indexed data over time. These came to be known as the "copy on write" (COW) BTrees, because the basic approach is to make a copy of (part of) the index when it is updated. An early example of COW BTrees is the [Episode File System](http://citeseer.ist.psu.edu/viewdoc/summary?doi=10.1.1.37.6439) built by Transarc in the 1990s.

These powerful, high-performance BTrees found their way outside of traditional DBMS implementations and down into the filesystem; examples of filesystems built atop the BTree include: [btrfs](http://en.wikipedia.org/wiki/btrfs), [ZFS](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.184.3704), and [WAFL](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.40.3691). With their carefully-orchestrated I/O behaviors and their multi-versioned interfaces, these BTree implementations offered sophisticated new features such as: shadowing, snapshotting, and cloning; they work well with modern hardware such as RAID and SSDs.

An excellent description of the concepts in most Third Age BTrees can be found in Ohad Rodeh's [_B-trees, Shadowing, and Clones_](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.161.6863).

Progress never stops, of course, and today's Internet-scale, distributed, virtualized, cloud-aware data centers are pushing to and beyond the limits of the centralized BTree implementations. Recently, interest has shifted to the investigation of distributed BTree implementations, such as the [_Partitioned B-trees_](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.3.5791) work done by Microsoft and [_A practical scalable distributed B-tree_](http://www.hpl.hp.com/techreports/2007/HPL-2007-193.pdf), by a team at HP.

Meanwhile, a startup company named Acunu has been gathering a lot of attention this summer for their work on _Stratified B-trees_, which are designed to address the problems of redundancy and duplication in COW B-trees. In [_Versioned external-memory dictionaries with optimal query/update tradeoffs_](http://arxiv.org/pdf/1103.2566v2), Andrew Byde and Andy Twigg define the problem as follows:

> For deep version trees, many keys in the range may not have been updated since the root version, while some may have been updated many times since then. It is easy to see that some elements must be replicated many times for range queries to be asymptotically optimal – a construction that achieves this while balancing asymptotically optimal space, query and update costs is our main contribution.

A good paper for starting to study the Acunu technology is last spring's [_Stratified B-trees and versioning dictionaries_](http://arxiv.org/abs/1103.4282).

It seems that we have entered a glorious new Fourth Age of BTrees in the past few years, as researchers and practitioners continue to extend and enhance this 40-year-old basic idea, demonstrating that there is still a lot of life left in one of computing's "old classics."

Have you seen any innovative and novel new improvements or variations on the BTree? Drop me a line and let me know!
