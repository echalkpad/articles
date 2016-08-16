# Parallel I/O and Columnar Storage

[Original URL](https://eventql.io/blog/parallel-io-and-columnar-storage/)

> Welcome to our new blog! Today's article is the first in a series of posts discussing the architecture and implementation of massively parallel databases such as Vertica [0], BigQuery [1] and EventQL...

_Welcome to our new blog! Today's article is the first in a series of posts discussing the architecture and implementation of massively parallel databases such as Vertica [[0]](https://eventql.io/blog/parallel-io-and-columnar-storage/#foot0), BigQuery [[1]](https://eventql.io/blog/parallel-io-and-columnar-storage/#foot1) and EventQL [[2]](https://eventql.io/blog/parallel-io-and-columnar-storage/#foot2)_

_We begin with a high level overview of the system while follow up posts will discuss specific components in more detail. The target audience are software and systems engineers with an interest in databases and distributed systems._

## The Challenge

Let's start off with a challenge: We're given a table with 100TB of web tracking data collected over a period of a few days. Here is a small sample of rows from the table:

![](https://eventql.io/blog/parallel-io-and-columnar-storage/tracking_data.png)

Our goal is to answer queries like _"How many people visited the page '/account/signup' in the last 10 days?"_. The are only two rules: The query is not known beforehand and we must answer it in less than a second. Here is the same example query in SQL:

```
SELECT count(1)
FROM tracking_data
WHERE url = '/account/signup' AND time > time_at("-10d");
```

If that doesn't sound like an interesting challenge yet, consider this quick back-of-the-envelope calculation: Assuming the average hard disk can read roughly 200MB per second (sequentially), loading 100TB from disk will take 500k disk-seconds or about 138 disk hours.

Oops. 138 hours is almost 6 days. A long way from "less than a second". We're seven orders of magnitude off and that's before we even started to process any of the data -- just to read it from disk.

How can we still solve the challenge? We can't make a single disk go any faster but we can break up the data set into smaller pieces, put each piece on its own disk and read all pieces from all disks in parallel. If we distributed the data over 500k individual disks we could read our whole data set in one second.

![](https://eventql.io/blog/parallel-io-and-columnar-storage/parallel_query.png)

There is only one small problem with that scheme -- half a million disks cost _a lot_ of money.

So even if we use a lot of machines, reading the full data set from disk in less than a second is utterly out of reach.

Can we still solve the challenge? Yes, but I'm afraid we'll have to cheat -- maybe we can answer the query without actually reading all the data from disk.

If we could come up with an algorithm which computes the query result after reading only .01% of the data (or 10GB) from disk, then we could return an answer in one second using just fifty disks. Fifty disks could be hosted in a dozen servers. Finally, that sounds reasonable!

But how do we compute the answer for the full dataset while reading only .01% of the data from disk? One approach would be to use sampling and probabalistic algorithms. However, sampling would give us _approximately_ correct results. Approximate results are great for some use cases and not so great to unworkable for others.

There is another trick we can use to minimze the amount of data to be read from disk while always giving correct results. The technique is called "column-oriented" or "columnar" storage.

## Columnar Storage

To really understand the benefit of columnar storage for data anlytics we first have to look at how regular _row-oriented_ databases store tables on disk:

It turns out they do it pretty much like you'd expect them to. They basically keep a file somwhere which contains all the rows in the table. One row after another. Hence the name "row-oriented".

![](https://eventql.io/blog/parallel-io-and-columnar-storage/row_oriented.png)

A row-oriented file conceptually looks something like this

What's problematic about row-oriented databases is that to execute a query, they always have to read the full rows from disk. Even if just a small part of each row is required to answer the query, the database still has to read every row in full. [[3]](https://eventql.io/blog/parallel-io-and-columnar-storage/#foot3)

The not completely intuitive reason for this is that hard disks are only fast if you read a file sequentially, i.e. only if you read one byte after another. Jumping around within a file performs very poorly. So poorly in fact, that reading whole rows is practically always _faster_ than reading partial rows. [[4]](https://eventql.io/blog/parallel-io-and-columnar-storage/#foot4)

Consider the following example query which calculates the number of page views per minute:

```
SELECT time, count(1)
FROM tracking_data
GROUP BY date_trunc("1min", time);
```

To compute the answer, we only need to know the value of the `time` column of each row. We're not interested in the `session_id`, `url` or any of the potentially hundreds of other columns of the table.

Ideally, we would only load the `time` column of each row from disk when executing the query. But we just saw that row-oriented storage can't do that efficiently. We always have to read the full rows no matter what.

Depending on the table and query, we could be spending 99% of the time reading data from disk which we're not going to need to answer the query.

This is the problem column-oriented storage tries to solve. The basic idea is that instead of storing one row after another, we can break up the row into the individual columns and then store one column after another.

If, for example, our table contained one thousand rows with each three columns `time`, `session_id` and `url`, we would first store an array of a thousand `time` values, then another array of a thousand `session_id` values and finally an array of a thousand `url` values.

You might have come across the concept before under a different name: What we're doing is basically vectorization [[5]](https://eventql.io/blog/parallel-io-and-columnar-storage/#foot5).

![](https://eventql.io/blog/parallel-io-and-columnar-storage/columnar_storage.png)

Storing each column seperately has two desirable properties. The first and most obvious one is that it allows us to also read each column separately -- we don't have to load all the extraneous columns from disk anymore.

The second and less obvious upside of columnar storage is that we can compress the data very efficiently. The compression further reduces the number of bytes we actually have to fetch from disk to read a row.

To see why compression in columnar files can be very significant, imagine our table had a fourth `is_customer` column. Sadly, we don't have any customers yet so the field is always `false`.

In a row-oriented database, storing the `is_customer` field for one million rows would take at least 1MB (one byte per boolean). In a columnar database we can store all one million values in a single byte -- a 1000000x improvement. [[6]](https://eventql.io/blog/parallel-io-and-columnar-storage/#foot6)

Lastly, it should be noted that columnar storage also has a big downside: It's less efficient to perform updates on columnar files than on row-oriented files. So you probably won't see the classical OLTP databases like MySQL switching to columnar any time soon. Still, for analytical queries on large data sets columnar is clearly the way to go.

## Are we there yet?

Looks like we have finally put together a scheme which will allow us to excute a SQL query on 100TB of data in less than a second, even though just reading the data from disk would have taken many days.

Let's recapitulate our approach: We're going to split up the data into many small pieces, then distribute the pieces among a dozen or so machines. On each machine, we will cheat by storing the rows in columnar format and only actually reading a small subset of the compressed data to answer the query.

Of course, we have only scratched the surface of the problem so far. In the [next post](https://eventql.io/blog/dividing-infinity-distributed-partitioning-schemes/) we will discuss how exactly we're going to split up the data into pieces.

You subscribe to email updates for upcoming posts or the rss feed in the sidebar.

### Next up in the series:

Tagged with massively parallel databases, internals, eventql, bigquery, vertica, sql, columnar storage

### Want to learn more about EventQL?

[Learn more](https://eventql.io/)
