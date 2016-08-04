# Writing Blazing Fast MySQL Queries

[Original URL](http://code.tutsplus.com/tutorials/writing-blazing-fast-mysql-queries--cms-25085)

> The differences between well written SQL and not are vast, and in production on a high-demand site they cause serious repercussions in performance and reliability of service. In this guide I will...

The differences between well written SQL and not are vast, and in production on a high-demand site they cause serious repercussions in performance and reliability of service. In this guide I will discuss how to write fast queries and what factors contribute to making them run slow.

## Why MySQL?

Today there is a lot of talk about Big Data and new technologies. NoSQL and cloud-based solutions are great, but a lot of popular web software (such as WordPress, phpBB, Drupal, VBulletin Forum software, etc.) still runs on MySQL. Migration to these newer solutions may not be as simple as just optimizing the configuration you already have in production. Besides, the performance of MySQL is very good, especially the [Percona version](https://www.percona.com/).

Don't make the common mistake of throwing more and more computing power at dealing with the problem of slow queries and high server loads, rather than actually addressing the root underlying issues. Adding CPU power, SSDs or RAM is a form of optimization if you like, but it's not what I will be talking about here. Also, without an optimized site, as you grow with the hardware gains the issues will multiply exponentially. So it is not a solid long-term solution.

Being good at SQL is always a vital tool for a web developer, and with the fix being often as simple as just adding an index or slightly modifying how the table is being used, it really does help to know how to use your RDBMS well. In this case we are focusing on a popular open-source database often used in conjunction with PHP, and that is MySQL.

### Who's This Guide For?

Web developers, Database Architects / DBAs and System Administrators that are familiar with MySQL. If you are not familiar with MySQL as a newbie, then this guide is most probably not going to make much sense, but I will try to keep it as informative as possible for newcomers to MySQL.

### Back Up First

I recommend trying the steps provided on your own MySQL database (back up everything first of course!). If you don't have any database to work on, example create database schemas are provided where applicable.

Backing up MySQL is easy with the `mysqldump` command line utility:

```
$ mysqldump myTable > myTable-backup.sql
```

You can learn [more about mysqldump](https://dev.mysql.com/doc/refman/5.1/en/mysqldump.html).

### What Makes a Query Slow?

In brief and in no order of importance, the following all play significant factors in query and server performance:

- table indexes
- `Where` clause (and usage of internal MySQL functions such as `IF` and `DATE` for example)
- sorting with `Order By`
- frequency of concurrent requests
- storage engine type (InnoDB, MyISAM, Memory, Blackhole)
- not using Percona edition
- server configuration variables (tuning my.cnf / my.ini)
- large result sets (>1,000 rows)
- non-persistent connections
- sharding / cluster configuration
- poor table design

We will address all of these areas within this guide. Also, if you are not already using it, please install [Percona](https://www.percona.com/software/mysql-database/percona-server), which is a drop-in replacement for MySQL that will bring a serious performance increase. To see a benchmark of Percona vs. MySQL, look at [this comparison](https://www.percona.com/software/mysql-database/percona-server/benchmarks).

### What Are Indexes?

Indexes are used by MySQL to find rows with specific column values quickly, for example inside a `WHERE`. Without an index, MySQL must begin with the first row and then read through the entire table to find the relevant rows. The larger the table, the more this costs.

If the table has an index for the columns in question, MySQL can quickly determine the position to seek to in the middle of the data file without having to look at all the data. This is much faster than reading every row sequentially.

## Non-Persistent Connections?

When your scripting language connects with the database, if you have configured persistent connections then it will be able to re-use an existing connection without having to create a new one. This is optimal for production usage and must be enabled.

PHP users can read more in the [PHP Manual](http://php.net/manual/en/features.persistent-connections.php).

### Reducing the Frequency of Concurrent Requests

The fastest, most effective way I have found to fix this is via utilising a key-value pair store such as `Memcached` or `Redis`.

With `Memcache` you can simply cache your query contents with the following, for example:

```
<?php
$cache = new Memcache;
$cache->connect('localhost',11211);
$cacheResult = $cache->get('key-name');
if($cacheResult){
 //.. no need to query
 $result = $cacheResult;
} else {
    //.. run your query
 $mysqli = mysqli('p:localhost','username','password','table'); //prepend p: to hostname for persistancy
 $sql = 'SELECT articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii FROM posts 
        LEFT JOIN userInfo using (UID) WHERE posts.post_type = 'post' || posts.post_type = 'article' 
 ORDER BY column LIMIT 50';
 $result = $mysqli->query($sql);
 $memc->set('key-name', $result->fetch_array(), MEMCACHE_COMPRESSED,86400);
}

//Pass the $cacheResult to template
$template->assign('posts', $cacheResult);

?>
```

Now the example `LEFT JOIN` query will only be run once every 86,400 seconds (24 hours), thus taking a huge amount of load away from the MySQL server and reducing the concurrent connections.

Note: Prepend `p:` to your host argument in MySQLi for persistent connections.

### Sharding / Clustering

When your data gets big or demand for your service ramps up, panic can set in. A quick fix to ensure your service stays online can be sharding. But I do not recommend that, because sharding inherently seems to make data structures overly complicated. And as explained very eloquently in this [article from the Percona blog](https://www.percona.com/blog/2009/08/06/why-you-dont-want-to-shard/), _don't shard._

### Poor Table Design

Creating database schemas isn't too hard when you accept some golden rules, such as working with the limitations and being aware of what will be efficient. Storing images in the database as `blob` datatypes, for example, is highly discouraged; storing a filename in a `varchar` datatype column is far superior.

Ensuring that the design is correct for the required usage is paramount in creating your app. Keep specific data separated (e.g. categories and posts) and ensure many-to-one or one-to-many relationships can be easily linked with IDs. Utilising the `FOREIGN KEY` facility of MySQL is ideal for cascading data contingency between tables.

When building your table, try to remember the following:

- Use the minimum you need to get the job done; be sparse and to the point.
- Don't expect MySQL to do your business logic or be programmatic--that should be done really before insertion by your scripting language. For example, if you need to randomise a list, do the randomisation of an array in PHP, not in an `ORDER BY` in MySQL.
- Use a `UNIQUE` index type for unique datasets and utilise `ON DUPLICATE KEY UPDATE` to keep a datetime or unix timestamp updated for example of the last time the row was checked.
- Use an `INT` datatype for integer numericals. If you don't specify the length, MySQL will calculate what is required itself.

## The Fundamentals of Optimization

To effectively optimize, we must look at three fundamental data sets regarding your application:

1. Analysis (slow query logging, auditing, query and table design analysis)
2. Performance requirements (how many users, what is the demand)
3. Constraints of technology (hardware speed, asking too much of MySQL)

Analysis can be done in several ways. Firstly we will take the most direct route to looking under the bonnet of MySQL queries. The first tool in your optimization toolbox is `EXPLAIN`. Utilising this in your query prior to the `SELECT` will give you the following output:

```
mysql> EXPLAIN SELECT articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii FROM `wp_posts` WHERE `post_type`='post';
+----+-------------+----------+------+------------------+------------------+---------+-------+------+-------------+
| id | select_type | table | type | possible_keys | key | key_len | ref | rows | Extra |
+----+-------------+----------+------+------------------+------------------+---------+-------+------+-------------+
| 1 | SIMPLE | wp_posts | ref | type_status_date | type_status_date | 82 | const | 2 | Using where |
+----+-------------+----------+------+------------------+------------------+---------+-------+------+-------------+
1 row in set (0.00 sec)
```

The columns listed each hold useful information about the query being executed. The columns you need to pay close attention to are `possible_keys` and `Extra`.

`possible_keys` will display the indexes that the MySQL engine has available to use for the query. Sometimes you need to force an index to ensure the query is executed in the fastest manner.

The `Extra` column will show whether a conditional `WHERE` or `ORDER BY` was used. Most important to note is if `Using Filesort` appears. Consider the following example:

```
EXPLAIN SELECT main_text
FROM posts
WHERE user = 'myUsername' &&
status = '1' && (
 status_spam_user = 'no_spam'
 || (
 status_spam_user = 'neutral' &&
 status_spam_system = 'neutral'
 )
)
ORDER BY datum DESC
LIMIT 6430 , 10
```

This type of query can get to disk because of the conditional where, which is happening if we look at the `EXPLAIN`:

```
id select_type table type possible_keys key key_len ref rows Extra
1 SIMPLE posts ref index_user,index_status index_user 32 const 7800 Using where; Using filesort
```

So this query has the possibility to use two indexes and currently it is hitting disk because of the `Using filesort` in the `Extra`.

What `Using Filesort` is doing is defined here from the MySQL manual:

> "MySQL must do an extra pass to find out how to retrieve the rows in sorted order. The sort is done by going through all rows according to the join type and storing the sort key and pointer to the row for all rows that match the WHERE clause. The keys then are sorted and the rows are retrieved in sorted order."

This extra pass will slow your app up and must be avoided at all costs. Another crucial `Extra` result to avoid is `Using temporary`, which means MySQL had to create a temporary table for the query. Obviously this is a hideous usage of MySQL and must be avoided at all costs unless you cannot optimize any further due to the data requirements. In this case the query should be cached in Redis or Memcache and not run by the users.

To fix the problem with `Using Filesort` we must ensure MySQL uses an `INDEX`. It has several `possible_keys` to choose from, but MySQL can only use one index in the final query. Although Indexes can be composites of several columns, the inverse is not true, although you can provide hints to the MySQL optimizer as to which indexes you have created.

### Index Hints

MySQL's optimizer will use statistics based on the queries' tables to select the best index for the scope of the query. It does so based on its built-in optimizer's statistical logic, although with multiple choices this cannot always be correct without hinting. To ensure the correct key is used (or not used), utilize the `FORCE INDEX`, `USE INDEX` and `IGNORE INDEX` keywords in your query. You can read more about index hinting in the [MySQL manual](http://dev.mysql.com/doc/refman/5.1/en/index-hints.html).

To look at the table keys, use the command `SHOW INDEX`.

You can specify multiple hints for the optimizer to use, for example:

````
SELECT articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii FROM table1 USE INDEX (col1_index,col2_index)
 WHERE col1=1 AND col2=2 AND col3=3;
 ```

Running an `EXPLAIN` will show you which index was used in the final outcome. So to fix the previous example we will add the `USE INDEX` as so:

```sql
EXPLAIN SELECT main_text
FROM posts USE INDEX (index_user)
WHERE user = 'myUsername' &&
status = '1' && (
 status_spam_user = 'no_spam'
 || (
 status_spam_user = 'neutral' &&
 status_spam_system = 'neutral'
 )
)
ORDER BY datum DESC
LIMIT 6430 , 10
````

Now that MySQL has the `index_status` from the table to use, the query is fixed.

```
id select_type table type possible_keys key key_len ref rows Extra
1 SIMPLE posts ref index_user,index_status index_user 32 const 7800 Using where
```

Alongside `EXPLAIN` is the `DESCRIBE` keyword. With `DESCRIBE` you can view a table's information as follows:

```
mysql> DESCRIBE City;
+------------+----------+------+-----+---------+----------------+
| Field | Type | Null | Key | Default | Extra |
+------------+----------+------+-----+---------+----------------+
| Id | int(11) | NO | PRI | NULL | auto_increment |
| Name | char(35) | NO | | | |
| Country | char(3) | NO | UNI | | |
| District | char(20) | YES | MUL | | |
| Population | int(11) | NO | | 0 | |
+------------+----------+------+-----+---------+----------------+
```

### Adding Indexes

You create indexes in MySQL with the `CREATE INDEX` syntax. There are a few flavours of index. `FULLTEXT` is used for full-text searching purposes, and then there is the `UNIQUE` type for ensuring data is kept unique.

To add an index to your table, use the following syntax for example:

```
mysql> CREATE INDEX idx_start_of_username ON `users` (username(10));
```

This will create an index on the table `users`, which will use the first 10 letters of the username column, which is a varchar data type.

In this case, any lookups requiring a `WHERE` sort on the username with the match being in the first 10 characters would be the same as a lookup of the entire table.

#### Composite Indexes

Indexes have a huge effect on the speed it takes to return the query data. Just setting a primary key and unique index is generally not enough--composite keys are where the real tuning niche lies in MySQL, and most often this requires some A/B checking with `EXPLAIN`.

For example, if we need to reference two columns within our `WHERE` conditional, a composite key would be ideal.

```
mysql> CREATE INDEX idx_composite ON users (username, active);
```

Here this key is being created on the `username` column from the prior example and the column `active`, an `ENUM` data type that signifies whether the user account is active. So now when querying the data for `WHERE` the username is valid and account is `active = 1`, the dataset is now optimized to handle this better.

## How Fast Is Your MySQL?

Enable profiling to take a closer look at your MySQL queries. This can be done at run time via `set profiling=1`, and then executing your query and looking at the result of `show profiles`.

With PDO here's a snippet of code that does just that:

```
$db->query('set profiling=1'); 
$db->query('select headline, body, tags from posts');
$rs = $db->query('show profiles');
$db->query('set profiling=0'); // Disable profiling after the query has been run

$records = $rs->fetchAll(PDO::FETCH_ASSOC); // Get the results from profiling

$errmsg = $rs->errorInfo()[2]; //Catch any errors here
```

If you are not using PDO, the same can be done with `mysqli` as so:

```
$db = new mysqli($host,$username,$password,$dbname);

$db->query('set profiling=1');
$db->query('select headline, body, tags from posts');
if ($result = $db->query("SHOW profiles", MYSQLI_USE_RESULT)) {
 while ($row = $result->fetch_row()) {
 var_dump($row);
 }
 $result->close();
}

if ($result = $db->query("show profile for query 1", MYSQLI_USE_RESULT)) {
 while ($row = $result->fetch_row()) {
 var_dump($row);
 }
 $result->close();
}

$db->query('set profiling=0');
```

This will return to you the profiling data, which will include the execution time in the second value of the associative array:

```
array(3) { 
    [0]=> string(1) "1" 
 [1]=> string(10) "0.00024300" 
 [2]=> string(17) "select headline, body, tags from posts" 
 }
```

The query took 0.00024300 seconds to complete. That's fast enough to not worry about. But when numbers ramp up, we must take a deeper look.

As a working example, get to know your app. Place a check for a `DEBUG` constant in your application's database abstraction layer / frameworks database driver, and then you can start auditing by enabling a profile case and outputting the result with a `var_dump` / `print_r`. Now you will be able to browse and profile your website's pages with ease!

## Fully Auditing Your App

To do a full audit of your queries, enable logging. Some developers I have worked with worry that this is a double-sided problem in that enabling the logging slightly affects performance, and so the stats you record will be slightly lower than in reality. Whilst this is true, many benchmarks show it's not too much of a difference.

To enable logging in MySQL version 5.1.6, you use the global `log_slow_queries` and can specify a file with `slow_query_log_file` global. This can be done in the runtime prompt as so:

```
set global log_slow_queries = 1;
set global slow_query_log_file = /dev/slow_query.log;
```

You can set this persistently in the `/etc/my.cnf` or `my.ini` configuration file for your server.

```
log_slow_queries = 1; 
slow_query_log_file = /dev/slow_query.log;
```

After making this change, you must restart the MySQL server, e.g. `service mysql restart` on Linux systems.

In the newer MySQL 5.6.1, `log_slow_queries` is deprecated and `slow_query_log` is used instead. Enabling `TABLE` as output type allows for a much nicer debugging experience and can be done as follows in MySQL 5.6.1 and later:

```
log_output = TABLE;
log_queries_not_using_indexes = 1;
long_query_time = 1
```

`long_query_time` specifies the number of seconds a slow query is classified as. The default is 10 and the minimum 0\. It can take millisecond values by specifying a float; here I have set it to 1 second. So any query taking longer than 1 second is going to get logged in the `TABLE` output format.

This will log to the `mysql.slow_log` and `mysql.general_log` tables within `MySQL`.

To disable logging, set `log_output` to `NONE`.

`log_queries_not_using_indexes` is a useful boolean that, when enabled in conjunction with the slow query log, means that only queries that are expected to retrieve all rows are logged.

This option does not always mean that no index is used. For example, when a query uses a full index scan, this would be logged because the index would not limit the number of rows.

### Logging in Production?

Enabling logging on a production site with traffic will pretty much always have to be done for a short period, whilst monitoring the load to ensure it doesn't affect service. If you are under heavy load and need an urgent fix, start by addressing the problem at the prompt with `SHOW PROCESSLIST` or via the `information_schema.PROCESSLIST` table directly, e.g. `select articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii from information_schema.PROCESSLIST;`.

Logging all queries in production can tell you a lot and is a good practice for research purposes when you are auditing a project, but leaving it running for days on end will often not give you any more usable data than at most 48 hours would do (on average, at least capture the peak times of usage to have a good look at the queries and get some ideas of frequency).

Note: if you run a site that experiences surges of peak traffic and then periods of not much at all (such as a sports website during on and off season), be logical with how you look at logging. Don't assume the site is working fast. Do audit and most importantly set up some graphing.

### Logging and Percona's pt-query-digest

Percona has some great tools bundled with it, and `pt-query-digest` is a command-line tool for analyzing query logs, the processlist or tcpdumps.

You can use `pt-query-digest` in the following ways:

Analyze a *.log file (outputted from your slow query logging for example):

```
$ pt-query-digest slow.log
```

Report on the slowest queries from host1 in real time (very useful!):

```
$ pt-query-digest --processlist h=host1
```

Use tcpdump to report the slowest queries from MySQL protocol data:

```
$ tcpdump -s 65535 -x -nn -q -tttt -i any -c 1000 port 3306 > mysql.tcp.txt

$ pt-query-digest --type tcpdump mysql.tcp.txt
```

Finally we can save slow query data from one host to another for later review. Here we save the query digest for slow.log to host2:

```
$ pt-query-digest --review h=host2 --no-report slow.log
```

To learn how to fully use the `pt-query-digest` tool of Percona, read the [manual page](https://www.percona.com/doc/percona-toolkit/2.2/pt-query-digest.html).

### Graphing MySQL and Server Performance

![InnoDB Row Operations](https://www.percona.com/doc/percona-monitoring-plugins/1.0/_images/mysql_innodb_row_operations.png) This graph of InnoDB Row Operations shows the row operations InnoDB has performed: updates, reads, deletes and inserts.

This is a big topic indeed and I will just touch on it enough in this guide to get you started with MySQL monitoring. It's important to note in general, however, that monitoring of all your website's services is ideal to really know what your performance and usages are.

To achieve this I recommend setting up a `RRDTool`-based solution such as `Cacti` with a MySQL configuration. Get a [template for Cacti](https://www.percona.com/doc/percona-monitoring-plugins/1.0/cacti/mysql-templates.html) from the guys at Percona.

Once you have got Cacti set up and can start to analyse your app, allow some time to pass so that the graphs can build up. After a few days you will start to see the day and night rhythms of your traffic and see how busy the server does truly get.

If you are looking for automated alerts and triggers, look into configuring [monit](https://mmonit.com/), an open-source proactive monitor for Unix systems. With monit you can create rules for your server and ensure you are alerted when the load rises so you can catch it while it happens.

### Slow Query Log

Logging all slow queries that take more than a second to complete can tell us something, but also knowing which queries are executing hundreds of times is equally important. Even if those queries are short to execute, the overhead of high requests still takes its toll on the server.

That's why staying around when you update something and put it live is the most crucial time for any new database work and changes. We always have a policy on my teams to never sync new feature database changes after a Wednesday on a live project. It must be done at the start of the week, at latest Tuesday, so that all teams can monitor and provide support accordingly.

Before going live with new queries, you must benchmark with a load-testing tool such as `ab`. When you run the benchmark you must be viewing the `SHOW PROCESSLIST`, and also enabling logging and be monitoring with system tools like `top`, `free` and `iostat`. This is a crucial step before putting any new query into a live production. But it is not a 100% acid test because live traffic can behave far differently to a computed benchmark.

To benchmark with `ab`, ensure you have the package installed, e.g.:

```
#centos users
$ sudo yum install ab
#debian / ubuntu users
$ sudo apt-get install ab
```

Now you can start by testing your app, for example:

```
$ ab -k -c 350 -n 20000 my-domain.com/
```

The `-k` means to `keep-alive` the connection, and the `-c 350` is the number of concurrent connections, i.e. the number of people/clients that will hit the site at once. Finally the `-n 20000` is the number of requests that will be made to `my-domain.com`.

So by running the command above, you will be hitting <http://my-domain.com/> with 350 simultaneous connections until 20,000 requests are met, and this will be done using the keep alive header.

After the process finishes the 20,000 requests, you will receive feedback on stats. This will tell you how well the site performed under the stress you put it when using the parameters above. This is a good way to know in an automated sense if your query has changed anything.

### Benchmarking Hot vs. Cold

Request amount and server load have a huge impact on performance, and query time can be affected due to this. In all you should enable the slow query log to catch this in production, and as a rule for development you must ensure all queries are executing in fractions of a millisecond (0.0xx or faster) on an idle server.

Implementing `Memcache` will have a dramatic impact on your load requirements and will be used to seriously offload resources that were being used up processing queries. Ensure you are using `Memcached` effectively and benchmark your app with a hot cache (preloaded with values) vs. a cold one.

To avoid stepping out into production with an empty cache, a pre-loader script is a good way of ensuring the cache will be read and you won't get a huge number of requests all coming in at once when returning from a downtime due to over-capacity failures.

## Fixing Slow Queries

So having enabled logging, you've now found some slow queries in your app. Let's get to fixing them! For example purposes, I will demonstrate various common problems you will encounter and the logic to fix them.

If you haven't found any slow queries yet, then maybe check what your settings where for the `long_query_time` if you are using the query logging method. Otherwise, having checked all your queries with profiling (`set profiling=1`), make a list of the queries which are taking longer than fractions of a millisecond to complete (0.000x seconds) and let's start on those.

### Common Problems

Here are six common problems I run into when optimizing MySQL queries:

#### 1\. `ORDER BY` using filesort.

```
mysql> explain select articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii from products where products.price > 4 and products.stock > 0 order by name;
+----+-------------+----------+------+---------------+------+---------+------+------+-----------------------------+
| id | select_type | table | type | possible_keys | key | key_len | ref | rows | Extra |
+----+-------------+----------+------+---------------+------+---------+------+------+-----------------------------+
| 1 | SIMPLE | products | ALL | NULL | NULL | NULL | NULL | 1142 | Using where; Using filesort |
+----+-------------+----------+------+---------------+------+---------+------+------+-----------------------------+
```

Avoiding filesort on this is impossible because of the `ORDER BY name`. No matter what index permutation you use, the best you will get is `Using where; Using Filesort` in your `Extra` column. To optimize this, save the result in Memcache, or do ordering in your application's logic layer.

#### 2\. Using `ORDER BY` on `WHERE` and a `LEFT JOIN`

`ORDER BY` has a significant toll on queries. For example, the following is a basic `LEFT JOIN` of a `products` table and `categories` table by means of an integer ID. When ordering is removed, so is the filesorting.

```
mysql> explain select products.* from products use index (idx_price) left join categories using (catID) where products.price > 4 and catID = 4 ORDER BY stock ASC limit 10;
+----+-------------+------------+-------+---------------+---------+---------+-------+------+-----------------------------+
| id | select_type | table | type | possible_keys | key | key_len | ref | rows | Extra |
+----+-------------+------------+-------+---------------+---------+---------+-------+------+-----------------------------+
| 1 | SIMPLE | products | ALL | idx_price | NULL | NULL | NULL | 986 | Using where; Using filesort |
| 1 | SIMPLE | categories | const | PRIMARY | PRIMARY | 4 | const | 1 | Using index |
+----+-------------+------------+-------+---------------+---------+---------+-------+------+-----------------------------+
2 rows in set (0.00 sec)

mysql> explain select products.* from products use index (idx_price) left join categories using (catID) where products.price > 4 and catID = 4;
+----+-------------+------------+-------+---------------+---------+---------+-------+------+-------------+
| id | select_type | table | type | possible_keys | key | key_len | ref | rows | Extra |
+----+-------------+------------+-------+---------------+---------+---------+-------+------+-------------+
| 1 | SIMPLE | products | ALL | idx_price | NULL | NULL | NULL | 986 | Using where |
| 1 | SIMPLE | categories | const | PRIMARY | PRIMARY | 4 | const | 1 | Using index |
+----+-------------+------------+-------+---------------+---------+---------+-------+------+-------------+
2 rows in set (0.00 sec)
```

When it can be avoided, try not to use an `ORDER BY`. If it absolutely must be used, order on an index key only.

#### 3\. `Order By` on a temp column

Just don't do it. If you are needing to aggregate your results, do that in your application logic; don't do the filtering or ordering on a temporary table inside MySQL. It's going to be very resource intensive.

#### 4\. Not using a `FULLTEXT` index

Using a `LIKE` query is by far the slowest way to perform a full-text match on your data. Implement a full-text search and reap the benefits of this brilliant feature of MySQL as so:

```
mysql> SELECT articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii FROM articles
 -> WHERE MATCH (title,body) AGAINST ('database');
+----+-------------------+------------------------------------------+
| id | title | body |
+----+-------------------+------------------------------------------+
| 5 | MySQL vs. YourSQL | In the following database comparison ... |
| 1 | MySQL Tutorial | DBMS stands for DataBase ... |
+----+-------------------+------------------------------------------+
2 rows in set (0.00 sec)
```

#### 5\. Selecting huge numbers of rows needlessly

Forgetting a `LIMIT` on a query can significantly change the lookup time over large datasets (over a million rows).

#### 6\. Over-joining instead of just making a composite table or view

When it gets to three or four levels of `LEFT JOIN`, you should ask yourself: "Am I doing this right?" If you have a reasonable argument for why this query must be so, for example it only appears in an admin screen in low demand or in usage of a larger statistical view that can be cached, then proceed. But if you are needing to access your data frequently with large numbers of joins, you should look at how compositing columns together into a new table may be more beneficial, or creating a [view](https://dev.mysql.com/doc/refman/5.0/en/views.html).

## Conclusions

We have discussed the fundamentals of optimization and the tools we have at our disposal to perform the work. We must audit with profiling, and use the `pt-query-digest` tool and `EXPLAIN` first to see what is really going on, and then from there we can design better.

We also looked at several example cases and common pitfalls you can run into when using MySQL. Using index hinting we can ensure MySQL selects the right indexes for the job and does not get confused, especially if there are multiple queries on the same table. To continue your reading on this topic, check out the [Percona project](http://www.percona.com) and [MySQL Performance blog](https://www.percona.com/blog/) for more information.
