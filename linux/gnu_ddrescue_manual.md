# GNU ddrescue Manual

[Original URL](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html)

> Next: Introduction, Up: (dir) This manual is for GNU ddrescue (version 1.20, 10 September 2015). Copyright © 2004-2015 Antonio Diaz Diaz. This manual is free documentation: you have...

[]() Next: [Introduction](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Introduction), Up: [(dir)](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#dir)

This manual is for GNU ddrescue (version 1.20, 10 September 2015).

Copyright © 2004-2015 Antonio Diaz Diaz. This manual is free documentation: you have unlimited permission to copy, distribute and modify it.

[]() Next: [Basic concepts](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Basic-concepts), Previous: [Top](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Top), Up: [Top](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Top)

## 1 Introduction

[]() GNU ddrescue is a data recovery tool. It copies data from one file or block device (hard disc, cdrom, etc) to another, trying to rescue the good parts first in case of read errors.

The basic operation of ddrescue is fully automatic. That is, you don't have to wait for an error, stop the program, restart it from a new position, etc.

If you use the mapfile feature of ddrescue, the data is rescued very efficiently, (only the needed blocks are read). Also you can interrupt the rescue at any time and resume it later at the same point. The mapfile is an essential part of ddrescue's effectiveness. Use it unless you know what you are doing.

Ddrescue does not write zeros to the output when it finds bad sectors in the input, and does not truncate the output file if not asked to. So, every time you run it on the same output file, it tries to fill in the gaps without wiping out the data already rescued.

Automatic merging of backups: If you have two or more damaged copies of a file, cdrom, etc, and run ddrescue on all of them, one at a time, with the same output file, you will probably obtain a complete and error-free file. This is so because the probability of having the same area damaged in all copies is low (if the errors are randomly located). Using the mapfile, only the needed blocks are read from the second and successive copies.

Ddrescue recommends lzip for compression of backups because the lzip format is designed for long-term archiving and provides data recovery capabilities which nicely complement those of ddrescue. (Ddrescue fills unreadable sectors with data from other copies, while lziprecover corrects corrupt sectors with data from other copies). If the cause of file corruption is damaged media, the combination ddrescue + lziprecover is the best option for recovering data from multiple damaged copies. See [lziprecover-example](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#lziprecover_002dexample), for an example.

Because ddrescue needs to read and write at random places, it only works on seekable (random access) input and output files.

If your system supports it, ddrescue can use direct disc access to read the input file, bypassing the kernel cache.

Ddrescue also features a "fill mode" able to selectively overwrite parts of the output file, which has a number of interesting uses like wiping data, marking bad areas or even, in some cases, "repair" damaged sectors.

One of the great strengths of ddrescue is that it is interface-agnostic, and so can be used for any kind of device supported by your kernel (ATA, SATA, SCSI, old MFM drives, floppy discs, or even flash media cards like SD).

[]() Next: [Important advice](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Important-advice), Previous: [Introduction](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Introduction), Up: [Top](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Top)

## 2 Basic concepts

Block<br>
Any amount of data. A block is described by its starting position and its size. The starting position (or beginning position) is the lowest position in the block. The end of the block is its starting position plus its size.

Cluster<br>
Group of consecutive sectors read or written in one go.

Device<br>
Piece of hardware containing data. Hard disc drives, cdrom drives, USB pendrives, are devices. /dev/hda, /dev/sdb, are device names.

File<br>
Files are named units of data which are stored by the operating system for you to retrieve later by name. Devices and partitions are accessed by means of their associated file names.

Partition<br>
Every part in which a device is divided. A partition normally contains a file system. /dev/hda1, /dev/sdb3, are partition names.

Recoverable formats<br>
As ddrescue uses standard library functions to read data from the device being rescued, only mountable device formats can be rescued with ddrescue. CD-ROMs and DVDs can be mounted and they can be rescued, "compact disc digital audio" CDs can't, "video CDs"[1] maybe.<br>
[1] <http://en.wikipedia.org/wiki/Video\_CD>

Rescue domain<br>
Block or set of blocks to be acted upon (rescued, listed, etc). You can define it with the options '

–input-position

', '

–size

' and '

–domain-mapfile

'. The rescue domain defaults to the whole input file or mapfile. If ddrescue can't determine the size of the input file, the rescue domain defaults to the maximum size of a block (at least 2^63 - 1 bytes, or 8 EiB minus 1 byte).

Ddrescue will never try to read any data outside of the rescue domain except when unaligned direct disc access is requested (see [Direct disc access](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Direct-disc-access)). If it does, please, report it as a bug.

The amount of data rescued, number of errors, etc, shown by ddrescue may vary or even become zero if you limit the rescue domain. Don't worry, they have not disappeared; they are simply out of the specified rescue domain.

Sector<br>
Hardware block. Smallest accessible amount of data on a device.

[]() Next: [Algorithm](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Algorithm), Previous: [Basic concepts](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Basic-concepts), Up: [Top](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Top)

## 3 Using ddrescue safely

[]() Ddrescue is like any other power tool. You need to understand what it does, and you need to understand some things about the machines it does those things to, in order to use it safely.

Never try to rescue a r/w mounted partition. The resulting copy may be useless. It is best that the device or partition to be rescued is not mounted at all, not even read-only.

Never try to repair a file system on a drive with I/O errors; you will probably lose even more data.

If you use a device or a partition as destination, any data stored there will be overwritten.

Some systems may change device names on reboot (eg. udev enabled systems). If you reboot, check the device names before restarting ddrescue.

If you interrupt the rescue and then reboot, any partially copied partitions should be hidden before allowing them to be touched by any operating system that tries to mount and "fix" the partitions it sees.

[]() Next: [Invoking ddrescue](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Invoking-ddrescue), Previous: [Important advice](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Important-advice), Up: [Top](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Top)

## 4 Algorithm

[]() GNU ddrescue is not a derivative of dd, nor is related to dd in any way except in that both can be used for copying data from one device to another. The key difference is that ddrescue uses a sophisticated algorithm to copy data from failing drives causing them as little additional damage as possible.

Ddrescue manages efficiently the status of the rescue in progress and tries to rescue the good parts first, scheduling reads inside bad (or slow) areas for later. This maximizes the amount of data that can be finally recovered from a failing drive.

The standard dd utility can be used to save data from a failing drive, but it reads the data sequentially, which may wear out the drive without rescuing anything if the errors are at the beginning of the drive.

Other programs read the data sequentially but switch to small size reads when they find errors. This is a bad idea because it means spending more time at error areas, damaging the surface, the heads and the drive mechanics, instead of getting out of them as fast as possible. This behavior reduces the chances of rescuing the remaining good data.

The algorithm of ddrescue is as follows (the user may interrupt the process at any point, but be aware that a bad drive can block ddrescue for a long time until the kernel gives up):

1) Optionally read a mapfile describing the status of a multi-part or previously interrupted rescue. If no mapfile is specified or is empty or does not exist, mark all the rescue domain as non-tried.

2) (First phase; Copying) Read the non-tried parts of the input file, marking the failed blocks as non-trimmed and skipping beyond them. Skip also beyond slow areas. The skipped areas are tried later in two additional passes (before trimming), reversing the direction after each pass until all the rescue domain is tried. The third pass is a sweeping pass, with skipping disabled. (The purpose is to delimit large errors fast, keep the mapfile small, and produce good starting points for trimming). Only non-tried areas are read in large blocks. Trimming, scraping and retrying are done sector by sector. Each sector is tried at most two times; the first in this step as part of a large block read, the second in one of the steps below as a single sector read.

3) (Second phase; Trimming) Trimming is done in one pass. For each non-trimmed block, read forwards one sector at a time from the leading edge of the block until a bad sector is found. Then read backwards one sector at a time from the trailing edge of the block until a bad sector is found. Then mark the bad sectors found (if any) as bad-sector, and mark the rest of the block as non-scraped without trying to read it.

4) (Third phase; Scraping) Scrape together the data not recovered by the copying or trimming phases. Scraping is done in one pass. Each non-scraped block is read forwards, one sector at a time. Any bad sectors found are marked as bad-sector.

5) (Fourth phase; Retrying) Optionally try to read again the bad sectors until the specified number of retry passes is reached. The direction is reversed after each pass. Every bad sector is tried only once in each pass. Ddrescue can't know if a bad sector is unrecoverable or if it will be eventually read after some retries.

6) Optionally write a mapfile for later use.

The total error size (errsize) is the sum of the sizes of all the bad-sector blocks. It increases during the trimming and scraping phases, and may decrease during the retrying phase. Non-trimmed and non-scraped blocks are not considered errors. Note that as ddrescue retries the failed blocks, the good data found may divide them into smaller blocks, decreasing the total error size but increasing the number of errors. The '

<span class="samp">remaining time</span>

' is calculated using the average rate of the last 60 seconds and does not take into account that some parts may be excluded from the rescue (for example with '

<span class="samp">–no-trim</span>

'), or that some areas may be unrecoverable. Therefore it may be very imprecise, may vary widely during the rescue, and may show a non-zero value at the end of the rescue. In particular it may go down to a few seconds at the end of the first pass, just to grow to hours or days in the following passes. Such is the nature of ddrescue; the good parts are usually recovered fast, while the rest may take a long time.

The mapfile is periodically saved to disc, as well as when ddrescue finishes or is interrupted. So in case of a crash you can resume the rescue with little recopying. The interval between saves varies from 30 seconds to 5 minutes depending on mapfile size (larger mapfiles are saved at longer intervals).

Also, the same mapfile can be used for multiple commands that copy different areas of the input file, and for multiple recovery attempts over different subsets. See this example:

Rescue the most important part of the disc first.

```
 ddrescue -i0 -s50MiB /dev/hdc hdimage mapfile
 ddrescue -i0 -s1MiB -d -r3 /dev/hdc hdimage mapfile
```

Then rescue some key disc areas.

```
 ddrescue -i30GiB -s10GiB /dev/hdc hdimage mapfile
 ddrescue -i230GiB -s5GiB /dev/hdc hdimage mapfile
```

Now rescue the rest (does not recopy what is already done).

```
 ddrescue /dev/hdc hdimage mapfile
 ddrescue -d -r3 /dev/hdc hdimage mapfile
```

[]() Next: [Mapfile structure](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Mapfile-structure), Previous: [Algorithm](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Algorithm), Up: [Top](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Top)

## 5 Invoking ddrescue

[]()[]()[]()[]() The format for running ddrescue is:

```
 ddrescue [options] infile outfile [mapfile]
```

infile and outfile may be files, devices or partitions. mapfile is a regular file and must be placed in an existing directory. If mapfile does not exist, ddrescue will create it.

Always use a mapfile unless you know you won't need it. Without a mapfile, ddrescue can't resume a rescue, only reinitiate it.

ddrescue supports the following options:

`-h`<br>
`--help`<br>
Print an informative help message describing the options and exit.

`-V`<br>
`--version`<br>
Print the version number of ddrescue on the standard output and exit.

`-a`bytes<br>
`--min-read-rate=`bytes<br>
Minimum read rate of good non-tried areas, in bytes per second. If the read rate falls below this value, ddrescue will skip ahead a variable amount depending on rate and error histories. The skipped blocks are tried in additional passes (before trimming) where the minimum read rate is divided by ten before each pass, until there are no more non-tried blocks left.

If bytes is 0 (auto), the minimum read rate is recalculated for each block as (average_rate / 10). Values above device capabilities are ignored.

`-A`<br>
`--try-again`<br>
Mark all non-trimmed and non-scraped blocks inside the rescue domain as non-tried before beginning the rescue. Try this if the drive stops responding and ddrescue immediately starts scraping failed blocks when restarted. If '

–retrim

' is also specified, mark all failed blocks inside the rescue domain as non-tried.

`-b`bytes<br>
`--sector-size=`bytes<br>
Sector (hardware block) size of input device in bytes (usually 512 for hard discs and 3.5" floppies, 1024 for 5.25" floppies, and 2048 for cdroms). Defaults to 512\.

`-B`<br>
`--binary-prefixes`<br>
Show units with binary prefixes (powers of 1024).<br>
SI prefixes (powers of 1000) are used by default. (See table below).

`-c`sectors<br>
`--cluster-size=`sectors<br>
Number of sectors to copy at a time. Defaults to 64 KiB / sector_size. Try smaller values for slow drives. The number of sectors per track (18 or 9) is a good value for floppies.

`-C`<br>
`--complete-only`<br>
Limit rescue domain to the blocks listed in the mapfile. Do not read new data beyond mapfile limits. This is useful when reading from devices of undefined size (like raw devices), when the drive returns an incorrect size, or when reading from a partial copy. It can only be used after a first rescue attempt, possibly limited with the '

–size

' option, has produced a complete mapfile.

`-d`<br>
`--idirect`<br>
Use direct disc access (see [Direct disc access](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Direct-disc-access)) to read from infile, bypassing the kernel cache. (Opens the file with the O_DIRECT flag). Sector size must be correctly set for this to work. Not all systems support this.

If your system does not support direct disc access, ddrescue will warn you. If the sector size is not correctly set, all reads will result in errors and no data will be rescued.

`-D`<br>
`--odirect`<br>
Use direct disc access to write to outfile, bypassing the kernel cache. (Opens the file with the O_DIRECT flag). Sector size must be correctly set for this to work. Not all systems support this.

If your system does not support direct disc access, ddrescue will warn you. If the sector size is not correctly set, a write error will result and no data will be rescued. Some OSs have a bug that prevents them from detecting write errors properly (or at all) on some devices if direct disc access is not used for outfile.

`-e [+]`n<br>
`--max-errors=[+]`n<br>
Maximum number of error areas allowed before giving up. Defaults to infinity. If n is preceded by '' the number refers to new error areas found in this run, not counting those already present in the mapfile.

`-E`bytes<br>
`--max-error-rate=`bytes<br>
Maximum rate of errors allowed before giving up, in bytes per second. Defaults to infinity. The rate being measured is that of actually failed reads, so the rescue may finish because of this rate being exceeded even if the total error size (errsize) does not change because the areas being tried are being marked as non-trimmed or non-scraped, or are already marked as errors.

`-f`<br>
`--force`<br>
Force overwrite of outfile. Needed when outfile is not a regular file, but a device or partition. This option is just a safeguard to prevent the inadvertent destruction of partitions, and is ignored for regular files.

`-F`types<br>
`--fill-mode=`types<br>
Fill the blocks in outfile specified as any of types in mapfile, with data read from infile. types contains one or more of the status characters defined in the chapter Mapfile structure (see [Mapfile structure](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Mapfile-structure)) and an optional '' for sector location data. See the chapter Fill mode (see [Fill mode](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Fill-mode)) for a complete description of the fill mode.

`-G`<br>
`--generate-mode`<br>
Generate an approximate mapfile from the infile and outfile of the original rescue run. Note that you must keep the original offset between '

–input-position

' and '

–output-position

' of the original rescue run. See the chapter Generate mode (see [Generate mode](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Generate-mode)) for a complete description of the generate mode.

`-H`file<br>
`--test-mode=`file<br>
Builds a map of good/bad blocks using the mapfile file and uses it to simulate read errors in infile. The blocks marked as finished in file will be read normally. All other block types will be considered read errors without even trying to read them from infile. This mode is an aid in improving the algorithm of ddrescue and is also useful to verify that ddrescue produces accurate results in presence of read errors. Use '' as file to read from standard input.

`-i`bytes<br>
`--input-position=`bytes<br>
Starting position of the rescue domain in infile, in bytes. Defaults to 0\. This is not the point from which ddrescue starts copying. (For example, if you pass the '

–reverse

' option to ddrescue, it starts copying from the end of the rescue domain). In fill mode it refers to a position in the infile of the original rescue run. See the chapter Fill mode (see [Fill mode](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Fill-mode)) for details.

`-I`<br>
`--verify-input-size`<br>
Compare the size of infile with the size calculated from the list of blocks contained in the mapfile, and exit with status 1 if they differ. This is not enabled by default because the size of some devices can't be known in advance and because the size derived from the mapfile may be incomplete, for example after doing a partial rescue.

`-J`<br>
`--verify-on-error`<br>
After every read error, read again the last good sector found and verify that it returns the same data. Exit with status 2 if the read fails or returns inconsistent data. This option performs one extra read after each error, wearing the drive faster. Use it only on drives that stop responding or return garbage data after finding errors. You may need to power cycle the drive before restarting ddrescue.

`-K [`initial`][,`max`]`<br>
`--skip-size=[`initial`][,`max`]`<br>
Set limits to skip size during the copying phase. At least one of initial or max must be specified. initial is the size to skip on the first read error or slow read, in bytes. max is the maximum size to skip. The values given will be rounded to the next multiple of sector size. The skip size will be doubled for each read error or slow read until it reaches max or, if max is omitted, 1% of the size of infile or 1 GiB (whichever is smaller), and will be reset to initial when good data is found. Valid values range from 64 KiB to 1 GiB. initial defaults to 64 KiB. An initial value of 0 disables skipping entirely.

If ddrescue is having difficulties skipping away from a large area with scattered errors, or if the device has large error areas at regular intervals, you can increase the initial skip size with this option. Inversely, if ddrescue is skipping too much, leaving large non-tried areas behind each error (which will be read later in the usually slower backwards direction), you can reduce the maximum skip size, or disable skipping.

'

<span class="samp">–skip-size</span>

' is independent from '

<span class="samp">–cluster-size</span>

'. The size to skip is calculated from the end of the block that just failed.

`-L`<br>
`--loose-domain`<br>
Accept an incomplete synthetic (user fabricated) domain mapfile or test-mode mapfile and fill the gaps in the list of data blocks with non-tried blocks. The blocks in the mapfile must be strictly ascending and non-overlapping, but they do not need to be contiguous. This option allows making quick edits to a mapfile without all the size calculations involved in making all data blocks contiguous again.

`-m`file<br>
`--domain-mapfile=`file<br>
Restrict the rescue domain to the blocks marked as finished in the mapfile file. This is useful for merging partially recovered images of backups, or if the destination drive fails during the rescue. Use '' as file to read from standard input.

`-M`<br>
`--retrim`<br>
Mark all failed blocks inside the rescue domain as non-trimmed before beginning the rescue. The effect is similar to '

–retry-passes=1

', but the bad sectors are tried in a different order, making perhaps possible to rescue some of them.

`-n`<br>
`--no-scrape`<br>
Skip the scraping phase. Avoids spending a lot of time trying to rescue the most difficult parts of the file.

`-N`<br>
`--no-trim`<br>
Skip the trimming phase. Specially useful in the first parts of a multi-part rescue.

`-o`bytes<br>
`--output-position=`bytes<br>
Starting position of the image of the rescue domain in outfile, in bytes. Defaults to '

–input-position

'. The bytes below bytes aren't touched if they exist and truncation is not requested. Else they are set to 0\.

`-O`<br>
`--reopen-on-error`<br>
Close infile and then reopen it after every read error and, if '

–min-read-rate

' is set, after every slow read encountered both during the copying phase. Use this option if you notice a permanent drop in transfer rate after finding read errors or slow areas. But be warned that most probably the slowing-down is intentionally caused by the kernel in an attempt to increase the probability of reading data from the device.

`-p`<br>
`--preallocate`<br>
Preallocate space on disc for outfile. Only space for regular files can be preallocated. If preallocation succeeds, rescue will not fail due to lack of free space on disc. If ddrescue can't determine the size to preallocate, you may need to specify it with some combination of the '

–input-position

', '

–output-position

', '

–size

', and '

–domain-mapfile

' options.

`-P[`lines`]`<br>
`--data-preview[=`lines`]`<br>
Show lines lines of the latest data read in '

16-byte hex + ASCII

' format. Valid values for lines range from 1 to 32\. If lines is omitted, a default value of 3 is used.

`-q`<br>
`--quiet`<br>
Quiet operation. Suppress all messages.

`-r`n<br>
`--retry-passes=`n<br>
Exit after given number of retry passes. Defaults to 0\. -1 means infinity. Every bad sector is tried only once in each pass. To retry bad sectors detected on a previous run, you must specify a non-zero number of retry passes.

`-R`<br>
`--reverse`<br>
Reverse the direction of all passes (copying, trimming, scraping and retrying). Every pass that is normally run forwards will now be run backwards, and vice versa. '

–reverse

' does not modify the size of the blocks copied during each phase, just the order in which they are tried.

`-s`bytes<br>
`--size=`bytes<br>
Maximum size of the rescue domain, in bytes. It limits the amount of input data to be copied. If ddrescue can't determine the size of the input file, you may need to specify it with this option. Note that this option does not specify the size of the resulting outfile. For example, the following command creates an outfile 300 bytes long, but only writes data on the last 200 bytes:

```
 ddrescue -i 100 -s 200 infile outfile mapfile
```

`-S`<br>
`--sparse`<br>
Use sparse writes for outfile. (The blocks of zeros are not actually allocated on disc). May save a lot of disc space in some cases. Not all systems support this. Only regular files can be sparse.

`-t`<br>
`--truncate`<br>
Truncate outfile to zero size before writing to it. Only works for regular files, not for drives or partitions.

`-T`interval<br>
`--timeout=`interval<br>
Maximum time since last successful read allowed before giving up. Defaults to infinity. interval is a rational number (like 1.5 or 1/2) optionally followed by one of '', '', '' or '', meaning seconds, minutes, hours and days respectively. If no unit is specified, it defaults to seconds.

`-u`<br>
`--unidirectional`<br>
Run all passes in the same direction. Forwards by default, or backwards if the option '

–reverse

' is also given.

`-v`<br>
`--verbose`<br>
Verbose mode. Further -v's (up to 4) increase the verbosity level.

`-w`<br>
`--ignore-write-errors`<br>
Make fill mode ignore write errors. This is useful to avoid ddrescue exiting because of new errors developing while wiping the good sectors of a failing drive. Fill mode normally writes to outfile one cluster at a time. With this option, after the first write error is found in an area, the rest of that area is filled sector by sector.

`-x`bytes<br>
`--extend-outfile=`bytes<br>
Extend the size of outfile to make it at least bytes long. If the size of outfile is already equal or longer than bytes then this option does nothing. Use this option to guarantee a minimum size for outfile. Only regular files can be extended.

`-X`<br>
`--exit-on-error`<br>
Exit after the first read error is encountered during the copying phase. This is similar but different to '

–timeout=0

', which waits until the screen status is refreshed (at least 1 second). If there is at least one successful read per second, '

–timeout=0

' does not make ddrescue to exit.

This is also similar but different to '

<span class="samp">–max-errors=+0</span>

', which exits when a new error area is found. If the read errors are adjacent to existing error areas, no new error areas are produced (just enlarged), and '

<span class="samp">–max-errors=+0</span>

' does not make ddrescue to exit.

`-y`<br>
`--synchronous`<br>
Use synchronous writes for outfile. (Issue a fsync call after every write). May be useful when forcing the drive to remap its bad sectors.

`-1`file<br>
`--log-rates=`file<br>
Log rates and error sizes every second in file. If file already exists, it will be overwritten. Every time the screen is updated with new details, some of those details (time, input position, current and average rates, number of errors and error size) are written to file in a format usable by plotting utilities like gnuplot. This allows a posterior analysis of the drive to see if it has any weak zones (areas where the transfer rate drops well below the sustained average).

`-2`file<br>
`--log-reads=`file<br>
Log all read operations in file. If file already exists, it will be overwritten. Every read attempt and its result (position, size, copied size and error size) is written to file. (The position written is always the beginning of the block tried, even if reading backwards). A line is also written at the beginning of each phase (copying, trimming, scraping and retrying). Finally, a line with a time mark is written every second (unless the read takes more time). Use this option with caution because file may become very large very quickly. Use lzip to compress file if you need to store or transmit it.

`--ask`<br>
Ask for user confirmation before starting the copy. If the first letter of the answer is '', ddrescue starts copying. Else it exits with status 1.<br>
If they can be obtained, ddrescue shows the model and serial number of the input and output devices. Else it shows the size in bytes of the corresponding file or device.

`--cpass=`n`[,`n`]`<br>
Select what pass(es) to run during the copying phase. Valid values for n range from 0 to 3\. '

–cpass=0

' skips the copying phase entirely. To run only the given pass(es), specify also '

–no-trim

' and '

–no-scrape

'.

`--max-read-rate=`bytes<br>
Maximum read rate, in bytes per second. If bytes is too small, the actual read rate is rounded up to the equivalent of a whole number of cluster reads per second. Use this option to limit the bandwidth used by ddrescue, for example when recovering over a network.

`--pause=`interval<br>
Time to wait between passes. Defaults to 0\. interval is formatted as in the option '

–timeout

' above.

Numbers given as arguments to options (positions, sizes, rates, etc) may be expressed as decimal, hexadecimal or octal values (using the same syntax as integer constants in C++), and may be followed by a multiplier and an optional '

<span class="samp">B</span>

' for "byte".

Table of SI and binary prefixes (unit multipliers):

Prefix | Value                  | | | Prefix | Value<br>
------ | ---------------------- | - | ------ | --------------------------
       |                        | | | s      | sectors<br>
k      | kilobyte (10^3 = 1000) | | | Ki     | kibibyte (2^10 = 1024)<br>
M      | megabyte (10^6)        | | | Mi     | mebibyte (2^20)<br>
G      | gigabyte (10^9)        | | | Gi     | gibibyte (2^30)<br>
T      | terabyte (10^12)       | | | Ti     | tebibyte (2^40)<br>
P      | petabyte (10^15)       | | | Pi     | pebibyte (2^50)<br>
E      | exabyte (10^18)        | | | Ei     | exbibyte (2^60)<br>
Z      | zettabyte (10^21)      | | | Zi     | zebibyte (2^70)<br>
Y      | yottabyte (10^24)      | | | Yi     | yobibyte (2^80)<br>

Exit status: 0 for a normal exit, 1 for environmental problems (file not found, invalid flags, I/O errors, etc), 2 to indicate a corrupt or invalid input file, 3 for an internal consistency error (eg, bug) which caused ddrescue to panic. If ddrescue is interrupted by a signal, it updates mapfile and then terminates by raising the signal received.

[]() Next: [Emergency save](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Emergency-save), Previous: [Invoking ddrescue](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Invoking-ddrescue), Up: [Top](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Top)

## 6 Mapfile structure

[]() NOTE: In versions of ddrescue prior to 1.20 the mapfile was called '

<span class="samp">logfile</span>

'. The format is the same; only the name has changed.

The mapfile is a text file easy to read and edit. It is formed by three parts, the heading comments, the status line, and the list of data blocks. Any line beginning with '

<span class="samp">#</span>

' is a comment line.

The heading comments contain the version of ddrescue or ddrescuelog that created the mapfile, the command line used, and the time when the program started. If the mapfile was created by ddrescue it will also contain the current time when the mapfile was saved and a copy of the status message from the screen describing the operation being performed (copying, trimming, finished, etc). They are intended as information for the user.

The first non-comment line is the status line. It contains a non-negative integer and a status character. The integer is the position being tried in the input file. (The beginning of the block being tried in a forward pass or the end of the block in a backward pass). The status character is one of these:

Character | Meaning<br>
--------- | ----------------------------------
'?'       | copying non-tried blocks<br>
'*'       | trimming non-trimmed blocks<br>
'/'       | scraping non-scraped blocks<br>
'-'       | retrying bad sectors<br>
'F'       | filling specified blocks<br>
'G'       | generating approximate mapfile<br>
'+'       | finished<br>

The blocks in the list of data blocks must be contiguous and non-overlapping.

Every line in the list of data blocks describes a block of data. It contains 2 non-negative integers and a status character. The first integer is the starting position of the block in the input file, the second integer is the size (in bytes) of the block. The status character is one of these:

Character | Meaning<br>
--------- | ------------------------------
'?'       | non-tried block<br>
'*'       | failed block non-trimmed<br>
'/'       | failed block non-scraped<br>
'-'       | failed block bad-sector(s)<br>
'+'       | finished block<br>

And here is an example mapfile:

```
 # Mapfile. Created by GNU ddrescue version 1.20
 # Command line: ddrescue -d -c18 /dev/fd0 fdimage mapfile
 # Start time: 2015-07-21 09:37:44
 # Current time: 2015-07-21 09:38:19
 # Copying non-tried blocks... Pass 1 (forwards)
 # current_pos current_status
 0x00120000 ?
 # pos size status
 0x00000000 0x00117000 +
 0x00117000 0x00000200 -
 0x00117200 0x00001000 /
 0x00118200 0x00007E00 data.json package.json 0x00120000 0x00048000 ?
```

If you edit the file, you may use decimal, hexadecimal or octal values, using the same syntax as integer constants in C++.

[]() Next: [Optical media](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Optical-media), Previous: [Mapfile structure](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Mapfile-structure), Up: [Top](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Top)

## 7 Saving the mapfile in case of trouble

[]() The mapfile is an essential part of ddrescue's effectiveness. Without a mapfile, ddrescue can't resume a rescue, only reinitiate it. Given that a difficult rescue may take days to complete, it would be a serious drawback if the mapfile were lost because of a solvable problem like a lack of space on the device the mapfile is written to.

In case of trouble writing the mapfile, ddrescue will print a message like this:

```
 Error writing mapfile 'mapfile': No space left on device
 Fix the problem and press ENTER to retry,
 or E+ENTER for an emergency save and exit,
 or Q+ENTER to abort.
```

You may try to fix the problem, for example deleting some files to make room for the mapfile, and press <Return> to retry.

If the problem can't be fixed, you may press <e> followed by <Return> to try an emergency save and exit. Ddrescue will try to write the mapfile to the file 

<span class="file">ddrescue.map</span>

 in the current directory or, if this fails, to 

<span class="file">$HOME/ddrescue.map</span>

. If the mapfile is written succesfully, ddrescue will exit with status 1\. Else it will print the above message again.

Or you may press <q> followed by <Return> to quit and exit with status 1\. In this case the contents of the mapfile will be lost.

[]() Next: [Examples](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Examples), Previous: [Emergency save](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Emergency-save), Up: [Top](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Top)

## 8 Copying CD-ROMs and DVDs

[]() Ddrescue may be better than dd for copying recordable CD-ROMs because the two lead out sectors at the end of some of them may cause a read error that prevents the whole last record from being copied by dd, potentially losing data. Also dd may create an image larger than the original if the '

<span class="samp">sync</span>

' conversion and a block size larger than the sector size are specified.

Recordable CD and DVD media keep their data only for a finite time (typically for some years). After that time, data loss develops slowly with read errors growing from the outer media region towards the inside. Just make two (or more) copies of every important CD-ROM/DVD you burn so that you can later recover them with ddrescue.

If you have only one copy of a CD-ROM or DVD that fails when being copied, and if you have access to multiple optical media drives, you have a better chance of recovering the bad sectors since one drive may fail to read a particular sector, but another drive might be able to squeeze the data out of it, depending on the laser frequency and the sensitivity of the laser-sensor that reads the reflected laser light.

Example 1: Rescue a CD-ROM in /dev/cdrom. ddrescue -n -b2048 /dev/cdrom cdimage mapfile ddrescue -d -r1 -b2048 /dev/cdrom cdimage mapfile (if errsize is zero, cdimage now contains a complete image of the CD-ROM and you can write it to a blank CD-ROM)

Example 2: Rescue a CD-ROM in /dev/cdrom from two copies. ddrescue -n -b2048 /dev/cdrom cdimage mapfile ddrescue -d -b2048 /dev/cdrom cdimage mapfile (insert second copy in the CD drive) ddrescue -d -r1 -b2048 /dev/cdrom cdimage mapfile (if errsize is zero, cdimage now contains a complete image of the CD-ROM and you can write it to a blank CD-ROM)

Example 3: Rescue a CD-ROM in /dev/cdrom using two CD drives from two different computers, writing the image into an USB drive nounted on /mnt/mem. ddrescue -n -b2048 /dev/cdrom /mnt/mem/cdimage /mnt/mem/mapfile ddrescue -d -r1 -b2048 /dev/cdrom /mnt/mem/cdimage /mnt/mem/mapfile (umount the USB drive and move both USB drive and CD-ROM to second computer) ddrescue -d -r1 -b2048 /dev/cdrom /mnt/mem/cdimage /mnt/mem/mapfile (if errsize is zero, /mnt/mem/cdimage now contains a complete image of the CD-ROM and you can write it to a blank CD-ROM)

Example 4: Merge the partially recovered images of 3 identical DVDs using their mapfiles as domain mapfiles. ddrescue -m mapfile1 dvdimage1 dvdimage mapfile ddrescue -m mapfile2 dvdimage2 dvdimage mapfile ddrescue -m mapfile3 dvdimage3 dvdimage mapfile (if errsize is zero, dvdimage now contains a complete image of the DVD and you can write it to a blank DVD)

[]()Example 5: Rescue a lzip compressed backup from two copies on CD-ROM with error-checked merging of copies. (See the [lziprecover manual](http://www.nongnu.org/lzip/manual/lziprecover_manual.html) for details about lziprecover). ddrescue -d -r1 -b2048 /dev/cdrom cdimage1 mapfile1 mount -t iso9660 -o loop,ro cdimage1 /mnt/cdimage cp /mnt/cdimage/backup.tar.lz rescued1.tar.lz umount /mnt/cdimage (insert second copy in the CD drive) ddrescue -d -r1 -b2048 /dev/cdrom cdimage2 mapfile2 mount -t iso9660 -o loop,ro cdimage2 /mnt/cdimage cp /mnt/cdimage/backup.tar.lz rescued2.tar.lz umount /mnt/cdimage lziprecover -m -v -o backup.tar.lz rescued1.tar.lz rescued2.tar.lz Input files merged successfully. lziprecover -tv backup.tar.lz backup.tar.lz: ok

[]() Next: [Direct disc access](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Direct-disc-access), Previous: [Optical media](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Optical-media), Up: [Top](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Top)

## 9 A small tutorial with examples

[]() This tutorial is for those already able to use the dd command. If you don't know what dd is, better search the net for some introductory material about dd and GNU ddrescue first.

A failing drive tends to develop more and more errors as time passes. Because of this, you should rescue the data from a drive as soon as you notice the first error. Be diligent because every time a physically damaged drive powers up and is able to output some data, it may be the very last time that it ever will.

You should make a copy of the failing drive with ddrescue, and then try to repair the copy. If your data is really important, use the first copy as a master for a second copy, and try to repair the second copy. If something goes wrong, you have the master intact to try again.

If you are trying to rescue a whole partition, first repair the copy with e2fsck or some other tool appropriate for the type of partition you are trying to rescue, then mount the repaired copy somewhere and try to recover the files in it.

If the drive is so damaged that the file system in the rescued partition can't be repaired or mounted, you will have to browse the rescued data with an hex editor and extract the desired parts by hand or use a file recovery tool like photorec.

If the partition table is damaged, you may try to rescue the whole disc, then try to repair the partition table and the partitions on the copy.

If the damaged drive is not listed in /dev, then you cannot rescue it. At least not with ddrescue.

See [Optical media](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Optical-media), for rescue examples of CD-ROMs and DVDs.

Example 1: Rescue a whole disc with two ext2 partitions in /dev/hda to /dev/hdb.<br>
Note: you do not need to partition /dev/hdb beforehand, but if the partition table on /dev/hda is damaged, you'll need to recreate it somehow on /dev/hdb. ddrescue -f -n /dev/hda /dev/hdb mapfile ddrescue -d -f -r3 /dev/hda /dev/hdb mapfile fdisk /dev/hdb e2fsck -v -f /dev/hdb1 e2fsck -v -f /dev/hdb2

Example 2: Rescue an ext2 partition in /dev/hda2 to /dev/hdb2.<br>
Note: you need to create the hdb2 partition with fdisk first. hdb2 should be of appropriate type and size. ddrescue -f -n /dev/hda2 /dev/hdb2 mapfile ddrescue -d -f -r3 /dev/hda2 /dev/hdb2 mapfile e2fsck -v -f /dev/hdb2 mount -t ext2 -o ro /dev/hdb2 /mnt (read rescued files from /mnt)

Example 3: While rescuing the whole drive /dev/hda to /dev/hdb, /dev/hda freezes up at position 12345678. ddrescue -f /dev/hda /dev/hdb mapfile <-- /dev/hda freezes here (restart /dev/hda or reboot computer) (restart copy at a safe distance from the troubled sector) ddrescue -f -i 12350000 /dev/hda /dev/hdb mapfile (then copy backwards down to the troubled sector) ddrescue -f -R /dev/hda /dev/hdb mapfile

Example 4: While rescuing the whole drive /dev/hda to /dev/hdb, /dev/hdb fails and you have to rescue data to a third drive, /dev/hdc. ddrescue -f -n /dev/hda /dev/hdb mapfile1 <-- /dev/hdb fails here ddrescue -f -m mapfile1 /dev/hdb /dev/hdc mapfile2 ddrescue -f -n /dev/hda /dev/hdc mapfile2 ddrescue -d -f -r3 /dev/hda /dev/hdc mapfile2

Example 5: While rescuing the whole drive /dev/hda to /dev/hdb, /dev/hda stops responding and disappears from /dev. ddrescue -f -n /dev/hda /dev/hdb mapfile <-- /dev/hda fails here (restart /dev/hda or reboot computer as many times as needed) ddrescue -f -n -A /dev/hda /dev/hdb mapfile ddrescue -d -f -r3 /dev/hda /dev/hdb mapfile

[]() Next: [Fill mode](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Fill-mode), Previous: [Examples](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Examples), Up: [Top](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Top)

## 10 Direct disc access

[]()[]() If you notice that the positions and sizes in mapfile are always multiples of the sector size, maybe your kernel is caching the disc accesses and grouping them. In this case you may want to use direct disc access for infile, or read from a raw device, to bypass the kernel cache and rescue more of your data.

NOTE! Sector size must be correctly set with the '

<span class="samp">–sector-size</span>

' option for direct disc access to work.

NOTE: Direct disc access can copy arbitrary domains by reading whole sectors and then writing only the requested part. This is the only case where ddrescue will try to read data outside of the rescue domain.

Try the '

<span class="samp">–idirect</span>

' option first. If direct disc access is not available in your system, try raw devices. Read your system documentation to find how to bind a raw device to a regular block device. Some OSs provide raw access through special device names, like /dev/rdisk.

Ddrescue aligns its I/O buffer to the sector size so that it can be used for direct disc access or to read from raw devices. For efficiency reasons, also aligns it to the memory page size if page size is a multiple of sector size. On some systems, ddrescue can't determine the size of a raw device, so an explicit '

<span class="samp">–size</span>

' or '

<span class="samp">–complete-only</span>

' option may be needed.

Using direct disc access, or reading from a raw device, may be slower or faster than normal cached reading depending on your OS and hardware. In case it is slower you may want to make a first pass using normal cached reads and use direct disc access, or a raw device, only to recover the good sectors inside the failed blocks.

Example 1: using direct disc access. ddrescue -f -n /dev/hdb1 /dev/hdc1 mapfile ddrescue -d -f -r3 /dev/hdb1 /dev/hdc1 mapfile e2fsck -v -f /dev/hdc1 mount -t ext2 -o ro /dev/hdc1 /mnt

Example 2: using a raw device. raw /dev/raw/raw1 /dev/hdb1 ddrescue -f -n /dev/hdb1 /dev/hdc1 mapfile ddrescue -C -f -r3 /dev/raw/raw1 /dev/hdc1 mapfile raw /dev/raw/raw1 0 0 e2fsck -v -f /dev/hdc1 mount -t ext2 -o ro /dev/hdc1 /mnt

[]() Next: [Generate mode](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Generate-mode), Previous: [Direct disc access](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Direct-disc-access), Up: [Top](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Top)

## 11 Fill mode

[]() When ddrescue is invoked with the '

<span class="samp">–fill-mode</span>

' option it operates in "fill mode", which is different from the default "rescue mode". That is, if you use the '

<span class="samp">–fill-mode</span>

' option, ddrescue does not rescue anything. It only fills with data read from infile the blocks of outfile whose status character from mapfile coincides with one of the type characters specified as argument to the '

<span class="samp">–fill-mode</span>

' option.

If the argument of the '

<span class="samp">–fill-mode</span>

' option contains an '

<span class="samp">l</span>

', ddrescue will write location data (position, sector number and status) into each sector filled. With bad sectors filled in this way, it should be possible to retry the recovery of important files, as location of the error is known by looking into the unfinished copy of the file.

In fill mode infile may have any size. If it is too small, the data will be duplicated as many times as necessary to fill the input buffer. If it is too big, only the data needed to fill the input buffer will be read. Then the same data will be written to every cluster or sector to be filled.

Note that in fill mode infile is always read from position 0\. If you specify a '

<span class="samp">–input-position</span>

', it refers to the original infile from which mapfile was built, and is only used to calculate the offset between input and output positions.

Note also that when filling the infile of the original rescue run you should not set '

<span class="samp">–output-position</span>

', whereas when filling the outfile of the original rescue run you should keep the original offset between '

<span class="samp">–input-position</span>

' and '

<span class="samp">–output-position</span>

'.

The '

<span class="samp">–fill-mode</span>

' option implies the '

<span class="samp">–complete-only</span>

' option.

In fill mode mapfile is updated to allow resumability when interrupted or in case of a crash, but as nothing is being rescued mapfile is not destroyed. The status line is the only part of mapfile that is modified.

The fill mode has a number of uses. See the following examples: Example 1: Mark parts of the rescued copy to allow finding them when examined in an hex editor. For example, the following command line fills all blocks marked as '

<span class="samp">-</span>

' (bad-sector) with copies of the string '

<span class="samp">BAD SECTOR </span>

':

```
 printf "BAD SECTOR " > tmpfile
 ddrescue --fill-mode=- tmpfile outfile mapfile
```

Example 2: Wipe only the good sectors, leaving the bad sectors alone. This way, the drive will still test bad (i.e., with unreadable sectors). This is the fastest way of wiping a failing drive, and is specially useful when sending the drive back to the manufacturer for warranty replacement.

```
 ddrescue --fill-mode=+ --force /dev/zero bad_drive mapfile
```

Example 3: Force the drive to remap the bad sectors, making it usable again. If the drive has only a few bad sectors, and they are not caused by drive age, you can probably just rewrite those sectors, and the drive will reallocate them automatically to new "spare" sectors that it keeps for just this purpose. WARNING! This may not work on your drive.

```
 ddrescue --fill-mode=- -f --synchronous /dev/zero bad_drive mapfile
```

Fill mode can also help you to figure out, independently of the file system used, what files are partially or entirely in the bad areas of the disc. Just follow these steps: 1) Copy the damaged drive with ddrescue until finished. Do not use sparse writes. This yields a mapfile with only finished ('

<span class="samp">+</span>

') and bad-sector ('

<span class="samp">-</span>

') blocks.

2) Fill the bad-sector blocks of the copied drive or image file with a string not present in any file, for example "DEADBEEF". Use '

<span class="samp">–fill-mode=l-</span>

' if you want location data.

3) Mount the copied drive (or the image file, via loopback device) read-only.

4) Grep for the fill string in all the files. Those files containing the string reside (at least partially) in damaged disc areas. Note that if all the damaged areas are in unused space, grep will not find the string in any file, which means that no files are damaged.

5) Take note of the location data of any important files that you want to retry.

6) Unmount the copied drive or image file.

7) Retry the sectors belonging to the important files until they are rescued or until it is clear that they can't be rescued.

8) Optionally fill the bad-sector blocks of the copied drive or image file with zeros to restore the disc image.

Example 4: Figure out what files are in the bad areas of the disc.

```
 ddrescue -b2048 /dev/cdrom cdimage mapfile
 printf "DEADBEEF" > tmpfile
 ddrescue --fill-mode=l- tmpfile cdimage mapfile
 rm tmpfile
 mount -t iso9660 -o loop,ro cdimage /mnt/cdimage
 find /mnt/cdimage -type f -exec grep -l "DEADBEEF" '{}' ';'
 (note that my_thesis.txt has a bad sector at pos 0x12345000)
 umount /mnt/cdimage
 ddrescue -b2048 -i0x12345000 -s2048 -dr9 /dev/cdrom cdimage mapfile
 ddrescue --fill-mode=- /dev/zero cdimage mapfile
 mount -t iso9660 -o loop,ro cdimage /mnt/cdimage
 cp -a /mnt/cdimage/my_thesis.txt /safe/place/my_thesis.txt
```

[]() Next: [Ddrescuelog](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Ddrescuelog), Previous: [Fill mode](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Fill-mode), Up: [Top](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Top)

## 12 Generate mode

[]() When ddrescue is invoked with the '

<span class="samp">–generate-mode</span>

' option it operates in "generate mode", which is different from the default "rescue mode". That is, if you use the '

<span class="samp">–generate-mode</span>

' option, ddrescue does not rescue anything. It only tries to generate a mapfile for later use.

So you didn't read the manual and started ddrescue without a mapfile. Now, two days later, your computer crashed and you can't know how much data ddrescue managed to save. And even worse, you can't resume the rescue; you have to restart it from the very beginning.

Or maybe you started copying a drive with `dd conv=noerror,sync` and are now in the same situation described above. In this case, note that you can't use a copy made by dd unless it was invoked with the '

<span class="samp">sync</span>

' conversion argument.

Don't despair (yet). Ddrescue can in some cases generate an approximate mapfile, from infile and the (partial) copy in outfile, that is almost as good as an exact mapfile. It makes this by simply assuming that sectors containing all zeros were not rescued.

However, if the destination of the copy was a drive or a partition, (or an existing regular file and truncation was not requested), most probably you will need to restart ddrescue from the very beginning. (This time with a mapfile, of course). The reason is that old data may be present in the drive that have not been overwritten yet, and may be thus non-tried but non-zero.

For example, if you first tried one of these commands:

```
 ddrescue infile outfile
 or
 dd if=infile of=outfile conv=noerror,sync
```

then you can generate an approximate mapfile with this command:

```
 ddrescue --generate-mode infile outfile mapfile
```

Note that you must keep the original offset between '

<span class="samp">–input-position</span>

' and '

<span class="samp">–output-position</span>

' of the original rescue run.

[]() Next: [Invoking ddrescuelog](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Invoking-ddrescuelog), Previous: [Generate mode](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Generate-mode), Up: [Top](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Top)

## 13 Ddrescuelog

[]() Ddrescuelog is a tool that manipulates ddrescue mapfiles, shows mapfile contents, converts mapfiles to/from other formats, compares mapfiles, tests rescue status, and can delete a mapfile if the rescue is done. Ddrescuelog operations can be restricted to one or several parts of the mapfile if the domain setting options are used.

When performing logic operations (AND, OR, XOR) on mapfiles of different extension, only the blocks present in both files are processed.

Here are some examples of how to use ddrescuelog, alone or in combination with other tools.

Example 1: Delete the mapfile if the rescue is finished (all data have been recovered without errors left). ddrescue -f /dev/hda /dev/hdb mapfile ddrescuelog -d mapfile

Example 2: Rescue two ext2 partitions in /dev/hda to /dev/hdb and repair the file systems using badblock lists generated with ddrescuelog. File system block size is 4096.<br>
Note: you do need to partition /dev/hdb beforehand. fdisk /dev/hdb <-- partition /deb/hdb ddrescue -f /dev/hda1 /dev/hdb1 mapfile1 ddrescue -f /dev/hda2 /dev/hdb2 mapfile2 ddrescuelog -l- -b4096 mapfile1 > badblocks1 ddrescuelog -l- -b4096 mapfile2 > badblocks2 e2fsck -v -f -L badblocks1 /dev/hdb1 e2fsck -v -f -L badblocks2 /dev/hdb2

Example 3: Rescue a whole disc with two ext2 partitions in /dev/hda to /dev/hdb and repair the file systems using badblock lists generated with ddrescuelog. Disc sector size is 512, file system block size is 4096\. Arguments to options '' and '' are the starting positions and sizes of the partitions being rescued.<br>
Note: you do not need to partition /dev/hdb beforehand, but if the partition table on /dev/hda is damaged, you'll need to recreate it somehow on /dev/hdb. ddrescue -f /dev/hda /dev/hdb mapfile fdisk /dev/hdb <-- get partition sizes ddrescuelog -l- -b512 -i63s -o0 -s767457s -b4096 mapfile > badblocks1 ddrescuelog -l- -b512 -i767520s -o0 -s96520s -b4096 mapfile > badblocks2 e2fsck -v -f -L badblocks1 /dev/hdb1 e2fsck -v -f -L badblocks2 /dev/hdb2

[]() Next: [Problems](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Problems), Previous: [Ddrescuelog](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Ddrescuelog), Up: [Top](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Top)

## 14 Invoking ddrescuelog

[]() The format for running ddrescuelog is:

```
 ddrescuelog [options] mapfile
```

Use '

<span class="samp">-</span>

' as mapfile to read the mapfile from standard input or to write the mapfile created by '

<span class="samp">–create-mapfile</span>

' to standard output.

Ddrescuelog supports the following options:

`-h`<br>
`--help`<br>
Print an informative help message describing the options and exit.

`-V`<br>
`--version`<br>
Print the version number of ddrescuelog on the standard output and exit.

`-a`old_types`,`new_types<br>
`--change-types=`old_types`,`new_types<br>
Change the status of every block in the rescue domain from one type in old_types to the corresponding type in new_types, much like the '' command does, and write the resulting mapfile to standard output. old_types and new_types are strings of block status characters as defined in the chapter Mapfile structure (see [Mapfile structure](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Mapfile-structure)). Blocks whose status is not in old_types are left unchanged. If new_types is shorter than old_types the last type of new_types is repeated as many times as necessary.

`-b`bytes<br>
`--block-size=`bytes<br>
Block size used by ddrescuelog. Depending on the requested operation it may be the sector size of the input device, the block size of the rescued file system, etc. Defaults to 512\.

`-B`<br>
`--binary-prefixes`<br>
Show units with binary prefixes (powers of 1024).<br>
SI prefixes (powers of 1000) are used by default. (See table above, [Invoking ddrescue](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Invoking-ddrescue)).

`-c[`type1type2`]`<br>
`--create-mapfile[=`type1type2`]`<br>
Create a mapfile from a list of block numbers read from standard input. Only blocks included in the rescue domain will be added to mapfile.

type1 and type2 are block status characters as defined in the chapter Mapfile structure (see [Mapfile structure](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Mapfile-structure)). type1 sets the type for blocks included in the list, while type2 sets the type for the rest of mapfile. If not specified, type1 defaults to '

<span class="samp">+</span>

' and type2 defaults to '

<span class="samp">-</span>

'.

`-C[`type`]`<br>
`--complete-mapfile[=`type`]`<br>
Complete a synthetic (user fabricated) mapfile by filling the gaps with blocks of type type, and write the completed mapfile to standard output. type is one of the block status characters defined in the chapter Mapfile structure (see [Mapfile structure](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Mapfile-structure)). If type is not specified, the gaps are filled with non-tried blocks. All gaps in mapfile are filled. Domain options are ignored.

`-d`<br>
`--delete-if-done`<br>
Delete the given mapfile if all the blocks in the rescue domain have been successfully recovered. The exit status is 0 if mapfile could be deleted, 1 otherwise.

`-D`<br>
`--done-status`<br>
Test if all the blocks in the rescue domain have been successfully recovered. The exit status is 0 if all tested blocks are finished, 1 otherwise.

`-f`<br>
`--force`<br>
Force overwrite of mapfile.

`-i`bytes<br>
`--input-position=`bytes<br>
Starting position of the rescue domain, in bytes. Defaults to 0\. It refers to a position in the original infile.

`-l`types<br>
`--list-blocks=`types<br>
Print on standard output the block numbers of the blocks specified as any of types in mapfile and included in the rescue domain. types contains one or more of the block status characters defined in the chapter Mapfile structure (see [Mapfile structure](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Mapfile-structure)).

The list format is one block number per line in decimal, like the output of the badblocks program, so that it can be used as input for e2fsck or other similar filesystem repairing tool.

`-L`<br>
`--loose-domain`<br>
Accept an incomplete synthetic (user fabricated) domain mapfile or compare-as-domain mapfile and fill the gaps in the list of data blocks with non-tried blocks. The blocks in the mapfile must be strictly ascending and non-overlapping, but they do not need to be contiguous. This option allows making quick edits to a mapfile without all the size calculations involved in making all data blocks contiguous again.

`-m`file<br>
`--domain-mapfile=`file<br>
Restrict the rescue domain to the blocks marked as finished in the mapfile file. Use '' as file to read from standard input.

`-n`<br>
`--invert-mapfile`<br>
Invert the types of the blocks in mapfile which are included in the rescue domain, and write the resulting mapfile to standard output. Finished blocks ('') are changed to bad-sector (''), all other types are changed to finished. '

–invert-mapfile

' is equivalent to '

–change-types=?*/-+,++++-

'

`-o`bytes<br>
`--output-position=`bytes<br>
Starting position of the image of the rescue domain in the original outfile, in bytes. Is used by the '

–list-blocks

' option. Defaults to '

–input-position

'.

`-p`file<br>
`--compare-mapfile=`file<br>
Compare the types of the blocks included in the rescue domain. The exit status is 0 if all the blocks tested are the same in both file and mapfile, 1 otherwise.

`-P`file<br>
`--compare-as-domain=`file<br>
Compare only the blocks marked as finished in the rescue domain. The exit status is 0 if all the blocks tested are the same in both file and mapfile, 1 otherwise. Two files comparing equal with this option are equivalent when used as domain mapfiles.

`-q`<br>
`--quiet`<br>
Quiet operation. Suppress all messages.

`-s`bytes<br>
`--size=`bytes<br>
Maximum size of the rescue domain, in bytes. It refers to a s
ize in the original infile.

`-t`<br>
`--show-status`<br>
Print a summary of the contents of each mapfile to the standard output. This option allows more than one mapfile. If the domain setting options are used, the summary can be restricted to one or several parts of mapfile.

`-v`<br>
`--verbose`<br>
Verbose mode. Further -v's (up to 4) increase the verbosity level.

`-x`file<br>
`--xor-mapfile=`file<br>
Perform a logical XOR (exclusive OR) operation between the finished blocks in file and those in mapfile, and write the resulting mapfile to standard output. In other words, in the resulting mapfile a block is only shown as finished if it was finished in either of the two input mapfiles but not in both.

`-y`file<br>
`--and-mapfile=`file<br>
Perform a logical AND operation between the finished blocks in file and those in mapfile, and write the resulting mapfile to standard output. In other words, in the resulting mapfile a block is only shown as finished if it was finished in both input mapfiles.

`-z`file<br>
`--or-mapfile=`file<br>
Perform a logical OR operation between the finished blocks in file and those in mapfile, and write the resulting mapfile to standard output. In other words, in the resulting mapfile a block is shown as finished if it was finished in either of the two input mapfiles.

Exit status: 0 for a normal exit, 1 for environmental problems (file not found, invalid flags, I/O errors, etc), 2 to indicate a corrupt or invalid input file, 3 for an internal consistency error (eg, bug) which caused ddrescuelog to panic.

[]() Next: [Concept index](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Concept-index), Previous: [Invoking ddrescuelog](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Invoking-ddrescuelog), Up: [Top](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Top)

# 15 Reporting bugs

[]()[]() There are probably bugs in ddrescue. There are certainly errors and omissions in this manual. If you report them, they will get fixed. If you don't, no one will ever know about them and they will remain unfixed for all eternity, if not longer.

If you find a bug in GNU ddrescue, please send electronic mail to [bug-ddrescue@gnu.org](mailto:bug-ddrescue@gnu.org). Include the version number, which you can find by running `ddrescue --version`.

[]() Previous: [Problems](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Problems), Up: [Top](http://www.gnu.org/software/ddrescue/manual/ddrescue_manual.html#Top)

# Concept index
