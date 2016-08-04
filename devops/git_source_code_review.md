# Git Source Code Review

[Original URL](http://fabiensanglard.net/git_code_review/diff.php)

> April 1st, 2014 I love you Hoi-En. To find the differences between two "things" is a difficult task that Git has to perform very often: Find the differences between two binaries files in order to...

April 1st, 2014

[![](http://fabiensanglard.net/git_code_review/git_blue_print.png)](http://fabiensanglard.net/git_code_review/index.php)

I love you Hoi-En.

![](http://fabiensanglard.net/git_code_review/diff.php) To find the differences between two "things" is a difficult task that Git has to perform very often:

- Find the differences between two binaries files in order to generate a delta.
- Find the differences between two text files in order to merge properly.
- Find the differences between two text files in order to display it.
- Find the differences between two commands in order to find typos.

Here is a summary of all the algorithms used with an associated genesis.

[Part 1: Overview](http://fabiensanglard.net/git_code_review/index.php)<br>
[Part 2: Genesis](http://fabiensanglard.net/git_code_review/history.php) [Part 3: Architecture](http://fabiensanglard.net/git_code_review/architecture.php) [Part 4: Algorithms for DIFF](http://fabiensanglard.net/git_code_review/diff.php)

## Diff on text

Find the difference between text files is something Git has to do the most commonly :

- Look at the difference between index and working directory: `git diff file.txt`.
- Compare the change between two commits: `git diff commitBefore commitAfter`
- Generate a patch : `git format-patch master --stdout > mypatch.patch`.

The [first implementation](https://github.com/git/git/blob/e83c5163316f89bfbde7d9ab23ca2e25604af290/show-diff.c#L39) simply leveraged the system `diff` executable via a call to `popen` in `show-diff.c`. `diff` is a very famous and ubiquitous tool in the Linux world originally developed in the early 1970 for Unix. Its first version used [Hunt–McIlroy algorithm](http://en.wikipedia.org/wiki/Hunt%E2%80%93McIlroy_algorithm). The core algorithm was later notoriously improved thanks to the work of [Eugene W. Myers](http://en.wikipedia.org/wiki/Eugene_Myers) and [Webb Miller](http://en.wikipedia.org/wiki/Webb_Miller), work extensively document in the papers: [An O(ND) Difference Algorithm and its Variations](http://www.xmailserver.org/diff2.pdf) by Eugene W. Myers and [A File Comparison Program](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.189.70&rep=rep1&type=pdf) by Webb Miller and Myers.

The two most important metrics for diff operations are:

Effort on Speed :

System diff not only consumed a `fork`/`exec`, it also required a lot of useless access disks because of temporary data. A builtin diff module was needed and implemented in [March 2006 patch](https://github.com/git/git/commit/3443546f6ef57fe28ea5cca232df8e400bfc3883) which introduced the `xdiff` folder containing a simplified version of [libxdiff](http://www.xmailserver.org/xdiff-lib.html):

Since libxdiff used Eugene W. Myers's algorithm, the diff strategy was called `myers`. To this day it is still the default way git perform all diffs on text.

Effort on Quality :

If speed is an easy metric to understand and benchmark, quality is subjective and not intuitive. Take for example two versions of a file where a developer simply added a method `functhreehalves` in the middle. There are two way to reconstruct the change but one is of much higher quality because it clearly shows what was added where the other seems to indicate something was modified.

```
 


 $diff -y file2 file1
 void func1() { void func1() {
 x += 1 x += 1
 } }

 > void functhreehalves() {
 > x += 1.5
 > }
 > 
 void func2() { void func2() {
 x += 2 x += 2
 } }


 ``` | ```
 


 $diff -y file2 file1
 void func1() { void func1() {
 x += 1 x += 1
 } > }
 >
 > void functhreehalves() {
 > x += 1.5
  }

 void func2() { void func2() {
 x += 2 x += 2
 } }


 ```
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

The discussion started in [[PATCH 0/3] Teach Git about the patience diff algorithm](http://marc.info/?l=git&m=123082787502576&w=2) where quality between git's myers and bazar's patience were compared. Since patience algorithm seemed to provide a better quality on specific input it was shortly added in a [patch](https://github.com/git/git/commit/92b7de93fb7801570ddc3195f03f30b9c201a3bd) that modified further `xdiff` :

You can find more documentation on patience algorithm from the author himself Bram Cohen (yes, the guy who also invented Bittorrent): [Patience Diff Advantages](http://bramcohen.livejournal.com/73318.html) and [Patience Diff, a brief summary](http://alfedenzo.livejournal.com/170301.html).

Further work was done by people from [jgit project](http://www.eclipse.org/jgit/) (which is a Java/Eclipse based). Their histogram based technique was supposed to be faster than `myers` while still based on based on Cohen's four rules found in `patience` (best of both world). A [patch](https://github.com/git/git/commit/8c912eea94a2138e8bc608f7c390eb0b313effb0) was eventually submitted:

Note : So which diff strategy should be used? I personally have always used the default (`myers`). `histogram` and `patience` seem to provide better output on specific case but they are so marginal and poorly documented that it is impossible to know when they will be good withiout trial/errors. If non-obsolete test cases could be provided users could probably make educated decisions.

Note : There is a fourth diff strategy called `minimal` which is actually an extended version of myers: If no minimal diff is requested the myers algo [simply returns](https://github.com/git/git/blob/master/xdiff/xdiffi.c#L153). But if minimal is requested, the algorithm will iterate until a certain amount of CPU time has been consumed and keep the "best" diff. Best being the smallest diff. `Minimal`was always part of xlibdiff and came with the first "speed" patch mentioned earlier.

Trivia : This need for speed also triggered the creation of a [performance testing framework](http://marc.info/?l=git&m=133103975225142) for diff strategies.

Trivia : diff is such an important feature that git developer even allow user to use their own external custom engine via the `GIT_EXTERNAL_DIFF` variable ([Using Git diff to detect code movement](http://stackoverflow.com/questions/12590947/using-git-diff-to-detect-code-movement-how-to-use-diff-options)).

To finish on text diff, and example of trace trace execution that I found useful:

```
 main (git.c)
 cmd_diff (builtin/diff.c)
 builtin_diff_files (builtin/pack-objects.c) 
 run_diff_files (diff-lib.c)
 xdi_diff_outf (xdiff-interface.c)
 xdi_diff (xdiff-interface.c)
 xdl_diff (xdiff/xdiffi.c)
 xdl_do_diff (xdiff/xdiffi.c)
 xdl_do_patience_diff
 xdl_do_histogram_diff
 meyes
 meyes mininmal
```

## Diff on binaries (deltas)

The initial architecture of Git was: one text file => one sha1 => one deflated blob. The only drawback was HD storage consumption but "disk is cheap" was a fair approach and things worked well this way for a while. But big repositories suffered when being cloned across a network and all those blobs required a lot of disk access.

The topic [RFC: adding xdelta compression to git](http://www.gelato.unsw.edu.au/archives/git/0505/2821.html) raised those issues and in the end it was decided that storage was cheap but bandwidth and I/O were not. The [xdelta lib](http://xdelta.org/) was briefly considered but a customized version of xlibdiff was favored and [added to xdiff](https://github.com/git/git/commit/a310d4349467d78266f38d29e500c77b96ee5bef).

Since binary files miss the CR character used to build atomic units in text algorithm (lines), different heuristic can be used in order to cut the stream. Set length of 48 bytes is an approach but git used [adler32](http://en.wikipedia.org/wiki/Adler-32) before being replaced later with Rabin's [Fingerprinting by random polynomials](http://www.xmailserver.org/rabin.pdf) in an other [patch](https://github.com/git/git/commit/3dc5a9e4cdcc7124c665a050547d1285d86a421f):

Rabin's finger print technique was a big improvement since the sliding window allowed to detect short addition at the beginning of a zip and keep deltas tiny.

To finish with binary diff, here is the execution path of pack creation :

```
 main (git.c)
 cmd_pack_objects (builtin/pack-objects.c)
 prepare_pack (builtin/pack-objects.c)
 ll_find_deltas (builtin/pack-objects.c)
 threaded_find_deltas (builtin/pack-objects.c)
 find_deltas (builtin/pack-objects.c)
 try_delta (builtin/pack-objects.c)
 create_delta (diff-delta.c) 
```

## Diff on command-line

git sometimes is not interested by the exact difference but rather by the distance between two strings. In 2008 was [introduced](https://github.com/git/git/commit/8af84dadb142f7321ff0ce8690385e99da8ede2f) a "suggestion" feature :

That is how the dispatcher seen in the [architecture](http://fabiensanglard.net/git_code_review/architecture.php) part is able to provide suggestions when a typo is suspected :

```
 $git adt test.txt
 git: 'adt' is not a git command. See 'git --help'.

 Did you mean this?
 add
```

Word suggestion are generated by calculating an edit distance for each [known builtin command](https://github.com/git/git/blob/afc711b8e1ee89626f0dddf0ef01fb73168d47ca/git.c#L335) using [levenshtein distance](https://github.com/fabiensanglard/git/blob/master/levenshtein.c) . Found in [levenshtein.c](https://github.com/git/git/blob/afc711b8e1ee89626f0dddf0ef01fb73168d47ca/levenshtein.c) , it is a nice example of dynamic programming.

## diffcore, xdiff, xdelta ... what ?

Just to summarize (because I was confused when I started) :

## Next

To be published: Git internal algorithms for graphs and merge operation.

## Comments

## 

Fabien Sanglard @2014
