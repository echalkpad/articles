# Getting dirty with open source databases

[Original URL](http://www.computerweekly.com/feature/Getting-dirty-with-open-source-databases)

> A decade ago, most enterprises building a database had only two or three choices: Oracle, Microsoft SQL Server and – to a lesser extent – IBM DB2\. Open source systems such as MySQL and...

A decade ago, most enterprises building a database had only two or three choices: Oracle, Microsoft SQL Server and – to a lesser extent – IBM DB2\. Open source systems such as MySQL and PostgreSQL existed, but they were not on the radar of most commercial organisations.

But as proprietary licensing has become more complex and costly, and businesses' needs have changed, so open source systems have emerged to meet evolving demands.

And while relational databases (RDBMSs) are not going away any time soon, non-relational databases are also making a significant comeback – most notably open source NoSQL systems such as MongoDB, Cassandra, Couchbase and Redis.

The big three proprietary suppliers have grown too greedy, believes Jamie Turner, CTO of PCA Predict (formerly Postcode Anywhere), a provider of address validation and e-commerce tools. "We have traditionally been a Microsoft SQL Server house, and 10 years ago licence costs were quite reasonable – maybe £3,000 per processor," he says. "That represented good value."

However, as computer processors have grown more powerful, traditional suppliers have tried to maintain healthy licence fees, charging not per processor but per core or some other measure that will allow them to protect their revenue stream.

<span> “</span>

It has turned into what seems like a greedy licensing fest," says Turner. "If you now want to put SQL Server onto a half-decent machine, you could easily be spending £10,000\. And different editions have different restrictions, for example limiting the amount of memory they will address.

"We have been forced to look elsewhere because it's not economical. If I want to do some serious number-crunching, I'm not going to spend £60,000 for a box when [I can get something like MySQL with zero licensing costs](http://www.computerweekly.com/feature/Open-source-its-the-logical-alternative) and which pretty much does the same thing. It might not be as polished, but the tooling has progressed immeasurably."

Analyst Matt Aslett, research director at 451 Research specialising in data platforms and analytics, says an increasing number of enterprises are reaching similar conclusions. "The high cost of proprietary RDBMSs is certainly one reason more businesses are investigating alternatives, although there are no simple answers," he says.

## **Agile development

For [bookmaker William Hill, which bet on Cassandra](http://www.computerweekly.com/news/4500252257/William-Hill-kicks-off-football-season-with-blast-from-the-past) for its new recommendation layer, the drivers were more about scalability and agile development. "We needed a technology that could scale easily," says Patrick Di Loreto, head of platform engineering. "As a result, NoSQL was the obvious choice. Cassandra is one of the most mature and its big table nature made it the best option for analysing time series data. Under a traditional approach to IT purchasing, we would have needed to construct a business case to justify the spend before starting development – a lengthy and costly process, which stifles risk-taking and experimentation. Now it has been pushed into production, the business case has formed naturally and there is a clear financial justification for additional investment to support it."

If you are purely looking for a lower-cost RDBMS alternative to Oracle or SQL Server, 451's Aslett says that as well as MySQL (now supported by Oracle), the more traditionally open source PostgreSQL (commonly known as Postgres) is a solid plug-in replacement. Although it has typically been deployed in smaller and non-commercial organisations, a lot of corporates are comfortably using it around the periphery.

"It continues to be refreshed and to innovate, even though, unlike some of the newer open source databases, there is no commercial organisation running it and it is written by a true community of developers," says Aslett.

Postgres has been around for more than 20 years and is used by some big names, including Skype, France's child benefits office and the NHS. Despite no commercial organisation dominating the community, there are a number of companies offering enterprise-grade support for the product.

Dave Page, chief architect at one such company, EnterpriseDB, is also a core member of the Postgres steering committee. He says: "Twenty years ago, it was a hobbyist project. Now it's grown to the point where all the people like me who were involved early on are now working for Postgres companies, building the features that enterprise customers want. So we put security and reliability ahead of all other concerns. Sometimes our competitors launch new features before us, but we put the emphasis on being rock-solid in terms of reliability. We are enterprise-ready and have feature sets comparable with the big three."

## **Savings of 80%

EnterpriseDB claims that even taking into account migration and support costs, companies switching to a [commercially supported version of Postgres](http://www.computerweekly.com/news/2240221828/Met-Office-swaps-Oracle-for-PostgreSQL) will typically see savings of 80% over a three-year period.

But in an increasing number of cases, RDBMSs are not the best solutions for today's needs. The twin challenges of being able to store and query growing swathes of unstructured data, such as JSON and XML documents, and to be able to deploy and scale up new applications quickly – potentially across multiple datacentres – has led to the growth of NoSQL databases.

Their new-found popularity was driven by web giants such as Google, Amazon and Facebook, which needed a highly scalable alternative to RDBMSs. They are typically open source, freely downloadable and – providing your IT team is happy to play around with a few different systems to learn what works best for your needs – may be worth considering.

PCA Predict recently developed a new customer experience app, Triggar, which relies on a lot of data to build models of customer behaviour. Turner tried all manner of database products, including MongoDB, Cassandra, Elasticsearch, MySQL and Postgres.

"They all have their limitations," he says. "For example, MongoDB couldn't reliably store the data and the calling model wasn't very good. Cassandra requires you to craft your schemas very carefully so you can get the data out efficiently, and you get very little ability to do any ad hoc querying. Elasticsearch has an incredibly rich querying environment, but when you put data in, there is a second or two delay before it becomes readable again and the default query language is horrible.

"Postgres worked really well until we realised we did an awful lot of write-heavy loads and it writes a new version of the record rather than updating the existing one. That means that if you've got a very active database, you end up with a lot of garbage. Cassandra does the same. MySQL didn't have a very good scaling model and it looked like it had been 'Oraclified' in terms of the documentation. We couldn't work our way round it, so we just gave up.

"Others that looked interesting include VoltDB, which is a crazy-fast in-memory database, and Aerospike. Both look towards more modern hardware and exploit things like SSDs [solid-state drive] that you typically can't do without hacking into the device layer."

## **Query language

In the end, Turner opted for Elasticsearch, but using an SQL-like query language rather than the default engine. "It gives us nice scalability, nice recovery from node failure, it's quite easy to deploy and there's a lot of richness in the querying," he says. "But the fact that it takes a second for data to appear when you write to it is a real problem. So we're now wondering whether to blend it with something else."

The point is, all these systems have compromises. "You have to put your engineering hat on and ask what you actually need to achieve rather than what you'd ideally like. I'd love to travel abroad on an aeroplane with a marbled interior and sofas, but all I really need to get from A to B is an economy flight," adds Turner.

451's Aslett agrees that getting your hands dirty is the only way to ascertain whether one of the newer, open source NoSQL or NewSQL databases will work for your particular application or workload. "Organisations have to play around with them," he says. "And since the vast majority are open source, you can just download and fiddle with them to figure out their nuances."

Aslett adds that although there may seem to be an unmanageable number of choices, it is "a fairly short list" if you look only at those that could be considered enterprise grade in terms of maturity, viability and support.

"[For NoSQL, the stand-out is MongoDB for a document database](http://searchdatamanagement.techtarget.com/feature/MongoDB-NoSQL-DBMS-overview), while if you're looking at key-value stores, Cassandra with support from Datastax, or Couchbase, or Riak from Basho," he says. "For graph databases, the one that jumps out is Neo4j. And if you're looking at more of a distributed SQL database, check out the so-called NewSQL databases like MemSQL or NuoDB. There are others, but not so many that you can't assess them."
