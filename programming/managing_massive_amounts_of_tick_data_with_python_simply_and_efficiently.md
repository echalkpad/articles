# Managing massive amounts of tick data with Python, simply and efficiently

[Original URL](http://gregharris.info/managing-massive-amounts-of-tick-data-with-python-simply-and-efficiently/)

> Tick data is hard to come by and hard to manage. This is my experience with it over the last month. I'm interested in how stocks react to certain events, and tick data will let me study the...

Tick data is hard to come by and hard to manage. This is my experience with it over the last month. I'm interested in how stocks react to certain events, and tick data will let me study the intra-day reaction.

## Getting Tick Data

- The [NYSE Trade and Quote (TAQ)](http://www.nyxdata.com/Data-Products/Daily-TAQ) product seems to be the source of data for many tick data providers. Unfortunately, it is very expensive.
- [TickData.com](https://www.tickdata.com/historical-market-data-products/historical-equities-data/) is a source I've used before for futures data. The pricing for the full history for all stocks is no longer listed on the website, but it also used to be very high.
- [QuantQuote.com](https://quantquote.com/products_tick-data.php) seems to be favored by the [Quantitative Finance Group at Caltech](http://quant.caltech.edu/historical-stock-data.html). Their historical tick data costs $20,000.
- [QuantGo.com](https://quantgo.com/data_services/us/) appears to rent the data for $275/month, as long as you use it in their cloud.
- [Wharton Research Data Services (WRDS)](https://wrds-web.wharton.upenn.edu/wrds/) is available for academic researchers at many institutions. I discovered my school (USC) has purchased access, and I was able to create an account. WRDS has TAQ data in addition to CRSP, Computstat, and many other great datasets. At first, I thought I could only get small, specific queries executed through their web interface. Later, I found the bulk data stored as SAS files on their secure FTP server. Unfortunately, the SAS files are not compressed, so it took me several weeks to download all trades since 1998 (about 3TB). The data is arranged in daily files, with the records sorted first by ticker, then by timestamp (truncated to the second). I have no hope of downloading the quote files, because they are about 40x as large. I did get the master files describing each ticker, as well as the files with dividends and splits going back to 1993\. The WRDS dataset is updated annually.

## Timeseries Databases

Several commercial databases exist for storing tick data. The most notable is [Kdb+](http://kx.com/). I couldn't find any benchmarks, but people seem to rave about it. [Stack Exchange](http://quant.stackexchange.com/questions/3156/is-there-any-thing-out-there-as-a-substitute-for-kdb) has a good discussion about it. It seems to be wildly expensive, although the 32-bit version is now free for non-commercial use. It requires the use of the _q_ language, and people generally say it has a steep learning curve. Much of the benefit seems to be from storing data in memory for faster query response. I'm not building a live trading system or collecting streaming ticks myself, so this is of little value to me.

Stack Overflow has a [discussion](http://stackoverflow.com/questions/1623399/storing-massive-ordered-time-series-data-in-bigtable-derivatives) on the use of [Cassandra](http://cassandra.apache.org/) for storing tick data. Cassandra looks interesting, but I think including every tick as a key in the index will be slow and take too much space. I could aggregate the ticks together, compress them, and use Cassandra as a key-value store for blobs. But, then the database doesn't seem to be adding any value, except for replicating itself across a cluster. I don't need the kind of scalability that would require a cluster.

In fact, I don't really need a database at all. My data store will be updated only once a year, and will otherwise be read-only. I don't need atomic transactions, multi-threaded socket connections, or security handling. I want something fast, simple, robust, and accessible from any programming language. [TeaFiles](http://discretelogics.com/teafiles/) come close to what I want, but they don't appear to support compression. In the end, I decided to just make my own data store on the file system.

## Key System Points

1. Data should be stored in binary form, not text files. Text files require parsing and data conversion, which slows everything down.
2. Data should be [column-oriented](https://en.wikipedia.org/wiki/Column-oriented_DBMS). That means all the timestamps are stored consecutively, and all the prices are stored consecutively, etc. Typical relational databases are row-oriented, keeping the full record together. So, the timestamp for a record is followed by the price for the record, and then the volume. It means all the fields must be scanned in order to get the desired field for a record. Otherwise, you'd need a seek operation for each record, which would be prohibitively slow.
3. Data should be compressed. Reading from a hard disk takes time, and compressed tick data takes only about 10% of the space. Decompression is pretty fast on a good computer. [One person's tests](http://skipperkongen.dk/2012/02/28/uncompressed-versus-compressed-read/) indicate that reading and decompressing compressed files is about 4x as fast as reading uncompressed files from disk.
4. Data should be stored on a solid state drive, which is much easier to do if it is compressed. SSDs are [many times faster](http://www.thessdreview.com/featured/ssd-throughput-latency-iopsexplained/) than spinning disks. Also, typical desktop hard drives have an [average seek latency of 9 ms](https://en.wikipedia.org/wiki/Hard_disk_drive_performance_characteristics#Seek_time), while typical SSDs have latencies around 0.10 ms. This is important for randomly accessing the data.

## Random-Access vs. Compression

Random access doesn't work with compressed files. Well, there seem to be [people working on that](http://lh3.github.io/2014/07/05/random-access-to-zlib-compressed-files/), but the solutions all require trade-offs. One method stores 32 kB uncompressed before each access point. Another method stores an index table at the end of the file and resets the stream before each access point. Other methods involve binary search and multiple seeks to peek into the compressed file and find the right location.

I chose to implement the method of resetting the stream at certain points, which are recorded in an index table. This involves a trade-off, where fine-grained access increases the size of the index table and reduces the size of the data chunk visible to the compression algorithm. I decided that the finest granularity I need is the ability to query the trades for a given ticker on a given day.

I first tried simply storing separate gzip files for each ticker-day. That took as much space as having no compression. The reason is that there are many files (about 10,000 tickers each day), and a large fraction of them contain very few ticks. Storing tiny amounts of data in individual files came with overhead in the form of a gzip headers and filesystem headers, and also each file [takes more space](http://www.fixedbyvonnie.com/2014/08/whats-difference-file-size-size-disk) when written to disk. Anyway, I've had filesystems bog down before with less than the 33 million files this solution would create.

My current solution is to combine all the values for each field for one day into its own file, like this:

[![File organization](http://gregharris.info/wp-content/uploads/2015/11/filesystem.png)](http://gregharris.info/wp-content/uploads/2015/11/filesystem.png)

Here, we see one folder per day, each folder with three files: price, size, and time. Each file is a concatenation of compressed data chunks, with one chunk per ticker:

[![filecontents](http://gregharris.info/wp-content/uploads/2015/11/filecontents.png)](http://gregharris.info/wp-content/uploads/2015/11/filecontents.png)

For convenience, I chose to simply store the index table in MySQL:

[![indextable](http://gregharris.info/wp-content/uploads/2015/11/indextable.png)](http://gregharris.info/wp-content/uploads/2015/11/indextable.png)

Each field has an offset, which tells where the compressed chunk for that ticker begins. Each field also has the number of bytes in the compressed chunk. Notice how some fields compress better than others, which is why I can't store just one offset and bytes pair for each ticker.

It might be possible to structure the data as one file per ticker, with each file holding the entire history. That has some disadvantages, though. First, tickers get reused over time by different companies. Second, it is less robust. I mean, if something gets messed up, I would need to reprocess all SAS files to rebuild one ticker. With my system, I would only need to rebuild a single day. Third, it would make it harder to parallelize. With my system, I can process multiple SAS files (days) at the same time.

## Tick Filtering

TAQ records contain ancillary fields that I chose not to store. [This document](http://www.nyxdata.com/doc/2659) and [this document](http://www.nyxdata.com/doc/224904) explain what each field means. I didn't keep all trades, but rather implemented [this filter recommended by WRDS](http://wrds-web.wharton.upenn.edu/wrds/support/Additional%20Support/WRDS%20Presentations/_000user2007/taq_data.pdf):

[![filter](http://gregharris.info/wp-content/uploads/2015/11/filter-300x202.png)](http://gregharris.info/wp-content/uploads/2015/11/filter.png)

## Python Code

First, I use the [sas7bdat](https://pypi.python.org/pypi/sas7bdat) package to extract the records from the SAS file:

```
from sas7bdat import SAS7BDAT
f = SAS7BDAT(filename)
f.skip_header = True
for row in f:
 if the record passes the filter, append the values to each list
f.close()
```

I use lists to store the prices, sizes, and timestamps for each ticker. [Lists grow better than arrays](http://stackoverflow.com/questions/176011/python-list-vs-array-when-to-use). Once I detect that the ticker has changed, I compress and append the data from the previous ticker:

```
import zlib
from array import array
...
# initialize the compressed dataset before processing each date
compressedTimes = ''
compressedPrices = ''
compressedSizes = ''
...
# after collecting all data for a ticker, compress and append it 
# append compressed time
binaryData = array('i', times).tostring()
compressedData = zlib.compress(binaryData, zlib.Z_BEST_COMPRESSION)
timeBytes = len(compressedData) # store in MySQL with ticker and date
timeOffset = len(compressedTimes) # store in MySQL with ticker and date
compressedTimes += compressedData
# append compressed price
binaryData = array('f', prices).tostring()
compressedData = zlib.compress(binaryData, zlib.Z_BEST_COMPRESSION)
priceBytes = len(compressedData)
priceOffset = len(compressedPrices)
compressedPrices += compressedData
# append compressed size
binaryData = array('f', sizes).tostring()
compressedData = zlib.compress(binaryData, zlib.Z_BEST_COMPRESSION)
sizeBytes = len(compressedData)
sizeOffset = len(compressedSizes)
compressedSizes += compressedData
```

Finally, after reading in all records from the SAS file, I write out the compressed data:

```
# time
fid = open(dateFolder + os.sep + 'time', 'wb')
fid.write(compressedTimes)
fid.close()
# price
fid = open(dateFolder + os.sep + 'price', 'wb')
fid.write(compressedPrices)
fid.close()
# size
fid = open(dateFolder + os.sep + 'size', 'wb')
fid.write(compressedSizes)
fid.close()
```

Note that I store the price and size as 4-bytes floats, and I store the timestamp as seconds in a 4-byte integer. I don't store the date, since it is found in the folder name. The ticker is stored only in MySQL.

## Reading the Data

First use MySQL to get the offsets and byte counts for the target ticker and date. Then run code like this:

```
# time
fid = open(dateFolder + os.sep + 'time', 'rb')
fid.seek(time_offset)
compressedData = fid.read(time_bytes)
binaryData = zlib.decompress(compressedData)
timeArray = array('i')
timeArray.fromstring(binaryData)
fid.close()
# price
fid = open(dateFolder + os.sep + 'price', 'rb')
fid.seek(price_offset)
compressedData = fid.read(price_bytes)
binaryData = zlib.decompress(compressedData)
priceArray = array('f')
priceArray.fromstring(binaryData)
fid.close()
# size
fid = open(dateFolder + os.sep + 'size', 'rb')
fid.seek(size_offset)
compressedData = fid.read(size_bytes)
binaryData = zlib.decompress(compressedData)
sizeArray = array('f')
sizeArray.fromstring(binaryData)
fid.close()
```

Note: opening the files using 'rb' instead of just 'r' is only necessary on Windows.

## Conclusion

Once the compressed files are prepared, it is incredibly fast to read the data. I suspect it is as fast as possible without maintaining a partial in-memory cache like Kdb+. Please let me know if you have a suggestion that can make the system faster. Some commercial systems use memory mapping to reduce the cost of system calls for seeking. This could be done on my compressed files, as well. It would only involve a modification of the reading code. Python Central ran a test where [memory mapping was 17% faster](http://pythoncentral.io/memory-mapped-mmap-file-support-in-python/). Anyway, the file format is simple enough to easily write data readers in any language. I believe zlib is available on all the major languages. Even Matlab seems to have access to it ([through the included JRE](http://www.mathworks.com/matlabcentral/fileexchange/8899-rapid-lossless-data-compression-of-numerical-or-string-variables/content/dunzip.m)).
