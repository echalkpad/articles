# Project Spartan Forensics

[Original URL](http://articles.forensicfocus.com/2015/07/27/project-spartan-forensics/)

> by James Gratchoff & Guido Kroon, University of Amsterdam Project Spartan is the codename of the new Microsoft Edge browser and successor to its previous, Internet Explorer. This research paper...

_by James Gratchoff & Guido Kroon, University of Amsterdam_

Project Spartan is the codename of the new Microsoft Edge browser and successor to its previous, Internet Explorer. This research paper gives insight into the current artefacts that the current development versions of Project Spartan leaves behind on workstations. The authors analysed what these artefacts are, where they are located and how can they be gathered. This research led to the conclusion that Project Spartan's back end does not differ much from the latest Internet Explorer versions, as Project Spartan still uses similar ways to store data on the workstation it runs on. Furthermore, an open source tool has been developed to gather some of these artefacts in an automated way. The purpose of the tool is to gather the location of the artefacts not present in the database.

**Introduction**

Web browsing activity is a major source of information in forensics investigation. Much open-source and proprietary software already exists to perform forensic investigation on the most popular leading web browsers. These forensic tools depend on the architecture of the web browsers and thus need to adapt their code to new versions or new browsers.

Microsoft is moving away from their traditional web browser, called Internet Explorer (IE), and launching their new Edge web browser, formerly codenamed Project Spartan, which will be shipped by default on Windows 10\. The web browser uses the new Edge engine, which is a fork from their former Trident engine that IE is based on. However, as Edge is currently still in development as Project Spartan, this research will refer to it as Project Spartan, and not as Edge.

The purpose of this project is to gather information about new artefacts that Project Spartan leaves behind on workstations. If time permits, an open source tool for analysing these artefacts will be created as a proof of concept.

**1.1 Scope, motivation and research question**

As Edge is a newly developed browser, it is interesting to research the artefacts it leaves on workstations, especially if more and more people are to start using it when Windows 10 is released next summer. Therefore, this new information may be valuable to the digital forensics community and will soon be needed for investigations. This project will only target the browser artefacts. A quick investigation of the new Cortana features has been also carried out. Information that can be found on this project is related to Project Spartan and not to the Edge browser that has not yet been released. However the browser is supposedly just to be given another product name thus the artefacts found should be the same and located in the same directory structure with a difference in the path name. The path name that will be used in Edge is not known on the day of writing.

Overall discussion of the significance and motivation resulted in the following research question:

_What and where are the artefacts Project Spartan leaves behind on workstations, and how can these artefacts be gathered for further analysis to serve as forensic evidence?_

The above research question can be divided into the following research sub-questions:

_1\. As the new Project Spartan engine is forked from its predecessor's Trident engine used with IE, how much does Project Spartan differ from its predecessor and to what extent can existing forensic toolkits for browsers still gather these artefacts in the same way they gather artefacts for Internet Explorer?_

_2\. Can a tool be developed, based on the assembled results, in order to gather the artefacts of the Project Spartan web browser in an automated way?_

**Related work**

Due to it being a recent product, no forensic research related to Windows 10 or Project Spartan/ Edge has been published at the time of writing. However much research has been done regarding web browser forensics. This project started by analysing the structure of Project Spartan and also how the latest version of Microsoft IE stored its information. Version 10 and later of IE will be referred to as IE v10+ in the rest of this report. Then the similar features of Microsoft Project Spartan were compared to IE v10+ in terms of artefacts location and databases. Furthermore the new features of Project Spartan were analysed and traced back to find where the artefacts location were stored on disk.

**2.1 Browser forensics**

Forensics tools that investigate browser activity, rely on the location of artefacts stored on disk. These locations are specific to each browser. Thus these tools need to adapt the locations and way of gathering information when a new browser is released. Forensics investigators need to gather detailed and trustworthy information about all the artefacts left on the disk by the browser. Moreover, any kind of information that a browser leaves behind can be valuable and of extreme importance in investigations. That is why it is important not to neglect any artefacts that could lead to a stronger proof of user activity.

Private browsing has also become popular as it is a way of increasing privacy while browsing. Using private browsing, the browser is not supposed to store any browsing activity during the session. Thus it is understandable that private browsing forensics has been a developing area of research. Said _et al_ researched Microsoft IE as well as Mozilla Firefox and Google Chrome regarding their privacy browsing features. They concluded that Google Chrome and Mozilla Firefox complete a better task in hiding their private browsing data, while Internet Explorer seems to leave evidence 'all over the hard drive'. Chivers conducted another research project targeting the private browsing feature of IE 10, and was able to recover data from private browsing in a specific window of time. Indeed by carving log files he was able to identify some substantial records of private browsing that had taken place the last time the browser was opened. Due to the short life cycle of private browsing records in the database, these records could not be found after opening the browser a second time. To<br>
carve the log files containing the previous records of the private browsing he developed a tool, ESECarve.

**2.2 Structure of Internet explorer**

A great deal of research has been done related to IE version 10 and later. And from our early investigation on the structure of Project Spartan we could say that it is extremely similar to IE v10+. Microsoft Project Spartan and IE v10+ rely on an Extensible Storage Engine (ESE) database, previously known as Joint Engine Technology (JET), to store their information. Metz, detailed in his research what the format of the database is and Chivers describes how the ESE works. In IE 10, a single database named WebCacheV1.dat is dedicated to storing artefacts. This database is located at:

[![Selection_001](https://forensicfocus.files.wordpress.com/2015/07/selection_001.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_001.png)Artefacts present in this database differ in their type (e.g. Cache, History, Cookies) and these types are divided into different containers tables ('Containers XX'). These containers can be identified using another container table present in the same database, named 'Containers', that acts as an index table specifying which artefacts correspond to which containers. Each container shares the same fields that can be found in [9]. All these fields are valuable for forensic investigations. The functioning of the database follows the steps described in [6]. When a transaction is taking place the ESE first stores in memory the information regarding this transaction in a log cache, then it subsequently stores in memory the necessary database pages. As soon as the system is ready it writes to the log file (e.g. V01.log). After this, if possible, the database is updated with the new transaction and proceeds in a clean state, if not it will proceed in a dirty state. If the state of the database is dirty it will have to be recovered using the .chk files (that stores logged transactions from a known checkpoint) and the corresponding log files. The database can also be recovered to a clean state using the esentutl Windows tool. Most of the artefacts are not only stored in the database but can also be found on the disk as files. For IE 10, these artefacts are located in the subdirectories of:

[![Selection_002](https://forensicfocus.files.wordpress.com/2015/07/selection_002.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_002.png)The artefacts that can be found there are for example the cache files, the cookies, the favourites and what have you. Another location where IE stores information is in the registry key. The information located there is obfuscated but can be read with IE PassView[4]. The information that can be found there is autocomplete forms, auto complete password or typed URLs. The location of the registry key is:

[![Selection_003](https://forensicfocus.files.wordpress.com/2015/07/selection_003.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_003.png)**Approach**

The first part of the research was to understand the structure of the Project Spartan browser and understand what methods it was using to store information about an user. Secondly an investigation on how and where artefacts were found in most used web-browsers was carried out. This investigation was mainly focused on IE version 10 and later, due to the similarities found in the first step with the Project Spartan browser. Further to this investigation the authors compared the artefacts from IE and Project Spartan and documented what new artefacts could be found on Project Spartan. The next step was to discover where and how artefacts are stored on the disk. Thereafter tools used to investigate browser activity were tested on the new browser. The last step was to summarise what had been found using available tools and to create a tool that is able to find the new artefacts discovered on Project Spartan.

The following tools were used for this research:

- **ESEDatabaseView v1.30** ESEDatabaseView is simple utility to browse through ESE structured database files, developed by Nir Sofer. We used it to browse through the ESE databases Project Spartan uses to store its data in, namely the WebCacheV01.dat and the Spartan.edb files.
- **ESECarve v1.20** ESECarve is a forensic tool written by Chivers that is used to inspect and and recover deleted data from ESE database files.
- **Notepad++ v6.7.8** (with the hex editor plugin v0.9.5) Notepad++ is an open source text editor for Windows operating systems. Together with the hex editor plugin we used this tool to open and read contents of many files.

**Artefacts Analysis**

This section describes where Project Spartan store its artefacts on disk and detail whereas or not these artefacts could be found in the Extensible Storage Engine (ESE) database. A section also describes what features, that are likely to leave artefacts, are not implemented yet in Project Spartan.

**4.1 Database**

Microsoft Project Spartan uses the same database structure as the latest versions of Internet Explorer, namely the ESE database. The Internet Explorer 10 ESE database structure has been researched in-depth by. The main WebCache database file is located in a dispersed fashion, which differs per user, hence the %LocalAppData% environment variable:

[![Selection_004](https://forensicfocus.files.wordpress.com/2015/07/selection_004.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_004.png)Numerous tools exist for reading these database files, which we will also list in the Tools section, but we mostly used ESECarve, ESEDatabaseView and esentutl. Within this database file, all sorts of information is stored, but not much actual content (some, but definitely not all). Rather, it is more like an index which keeps track of all the locations where the actual artefacts are stored.

When we try to open this file with a hex editor, we can see that this version still uses the same format that previous versions use. The hex dump of the database headers can be interpreted as follows (see figure 4.1). This can be verified when analysing the database with esentutl, which is installed by default on every Windows system. Note that the database is using little endian, so when compared with a hexadecimal dump, every byte range needs to be read in reverse order. While the database mostly stores Metadata as opposed to actual content, there are some interesting artefacts to be harvested from within this file, such as:

1. visited URLs (see section 4.5)
2. Cortana search queries (see section 4.8)
3. download history (see section 4.6).

[![Selection_005](https://forensicfocus.files.wordpress.com/2015/07/selection_005.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_005.png)The ESE database also contains the location of every other artefacts that are stored locally on the system (see figure 4.2).

The container that is being viewed in figure 4.2 shows all the container IDs of the other containers that can be viewed. It shows what content is being stored in which container and where it can be found on the system (folder paths).

**4.2 Cache**

Project Spartan stores its caches in a dispersed fashion as well, which differs per user, hence the %LocalAppData% environment variable:

[![Selection_006](https://forensicfocus.files.wordpress.com/2015/07/selection_006.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_006.png)Just like IE there are four cache folders in this directory, which each contain a portion of the cache. They contain all sorts of content which is saved locally when browsing with Project Spartan, like HTML pages, pictures and even downloads which are stored here temporarily before they are moved to the actual download folder. This is an example of such a cache folder:

[![Selection_007](https://forensicfocus.files.wordpress.com/2015/07/selection_007.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_007.png)**4.3 Cookies**

Project Spartan stores its cookies in a dispersed fashion as well, which differs per user, hence the %LocalAppData% environment variable:

[![Selection_008](https://forensicfocus.files.wordpress.com/2015/07/selection_008.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_008.png)The cookies are stored in txt files with a randomly chosen name. This is an example of a cookie '1YTEYKVD.txt':

[![Selection_009](https://forensicfocus.files.wordpress.com/2015/07/selection_009.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_009.png)Project Spartan knows which cookie file belongs to which domain as this is being tracked in the WebCachev01.dat database.

**4.4 Bookmarks**

Project Spartan stores its bookmarks in a dispersed fashion as well, which differs per user, hence the %LocalAppData% environment variable:

[![Selection_010](https://forensicfocus.files.wordpress.com/2015/07/selection_010.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_010.png)[![Selection_011](https://forensicfocus.files.wordpress.com/2015/07/selection_011.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_011.png)Figure 4.4 shows a screenshot of the bookmarks folder.

[![Selection_012](https://forensicfocus.files.wordpress.com/2015/07/selection_012.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_012.png)**4.5 Visited URLs**

The visited URLs is a form of Metadata that reveals information of what URLs the user browsed. It does not show the actual content of the web pages, but it is still valuable information for forensic investigators. The URLs are stored within the database file we covered in section 4.1\. The following screenshot (see figure 4.5) gives an impression of these artefacts. Some columns are not shown, which also reveal information about dates and time, and which can be useful when creating time lines:

[![Selection_013](https://forensicfocus.files.wordpress.com/2015/07/selection_013.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_013.png)**4.6 Download history**

Downloads history is also found in the database file we covered in section 4.1\. The container name is 'iedownload'. There are multiple containers under that name, but it is container ID 17 on our system (see figure 4.6). The values are hex encoded and thus need to be converted to ASCII readable text in order to read it (see appendix B for a more detailed overview). The downloads are temporarily stored in the cache location we covered in section 4.2\. When the download is completed, it is no longer to be found there, as it is then moved to the download folder.

[![Selection_014](https://forensicfocus.files.wordpress.com/2015/07/selection_014.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_014.png)**4.7 Web Notes**

Interestingly enough, Microsoft Spartan stores its Web Notes in the bookmarks folder as well (see figure 4.7):

[![Selection_015](https://forensicfocus.files.wordpress.com/2015/07/selection_015.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_015.png)[![Selection_001](https://forensicfocus.files.wordpress.com/2015/07/selection_0011.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_0011.png)**4.8 Cortana**

Project Spartan uses Bing as its search engine for Cortana search queries. This is an experimental feature that was not available to our country yet (The Netherlands), so we used an OpenVPN[5] connection to the US to test this new feature. Spartan stores its search queries inside the database file we covered in section 4.1\. The container name is 'DependencyEntry 5′(see figure 4.9).

[![Selection_002](https://forensicfocus.files.wordpress.com/2015/07/selection_0021.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_0021.png)**4.9 Reading list**

The reading list is stored inside a separate database, also separate for each user. We added a web page to the reading list, which could be found inside the database when we opened the database with ESEDatabaseView (see figure4.10).

[![Selection_003](https://forensicfocus.files.wordpress.com/2015/07/selection_0031.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_0031.png)**4.10 Tiles**

Since Windows 8, tiles are available and can be modifiable. This feature is included in Project Spartan as well. It consists of fonts, colours and interface elements for applications. The tiles are not stored in the ESE database but can be found on the disk. They are located at:

[![Selection_004](https://forensicfocus.files.wordpress.com/2015/07/selection_0041.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_0041.png)**4.11 Private browsing**

For analysing artefacts for InPrivate browsing, we needed to upgrade our system with a newer version of Windows 10 (build 10122), which had the new Spartan Browser that supported InPrivate browsing. In order to retrieve the InPrivate pages visited, we used a tool created by Chivers, named ESECarve. This tool was intended to retrieve InPrivate browsing artefacts from the IE 10 browser. Due to incompatibility of the software on Windows 10, it was necessary to move the folder containing the database files (.chk, .log and WebCacheV01.dat) to an earlier version of Windows. We were successfully able to recover Project Spartan InPrivate pages with Windows 7 and the ESECarve tool (see figure 4.11).

The life cycle of InPrivate logs described in [6] was verified with Project Spartan. Indeed the InPrivate history could be recovered from the same session with ESEDatabaseView (see figure 4.12) but as soon as we cleared the cache and restarted the browser these entries disappeared from the container. However the entries were recovered using the ESECarve tool that uses the .log and .chk files to recover information about the InPrivate browsing.

[![Selection_005](https://forensicfocus.files.wordpress.com/2015/07/selection_0051.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_0051.png)**4.12 Features not (yet) integrated in Project Spartan**

The version of Project Spartan available in the latest Windows 10 build (10122) does not include all the features that should be present on a browser. New features are awaited such as the password storage or extensions capability. IThome, leaked some screenshots of an unreleased build of Windows 10 (10123).

These screenshots show new features implemented in Project Spartan such as:

• **Credential storage** As of yet, Project Spartan does not enable users to store their credentials whenever they login to a certain website.

• **Forms storage** As of yet, Project Spartan does not enable users to store forms whenever a user fills in a digital form.

• **New features in Cortana** Current features, such as Cortana, may change over time or have added features, which is also interesting for future research. These features could not be investigated as the 10123 release was not available at the time of writing.

Other potential features that are currently not part of Project Spartan:

• **Synchronisation** Current popular browsers are currently offering synchronisation of passwords, bookmarks and such. It would not be a far fetched idea that Microsoft may implement such a feature in later development versions of Project Spartan, or final versions of EDGE.

**Results**

This section presents what are the results of our investigation on the Project Spartan browser. First the similarities and differences found between Project Spartan and IE v10+ will be described. This is followed by a description of the automated tool created to find the missing artefacts that are not documented in the ESE database.

**5.1** **Project Spartan vs. Internet Explorer (similarities and differences)**

The investigation performed in this research, highlighted the Project Spartan artefacts. This section compares the artefacts found in Project Spartan with the latest versions of IE. This comparison is done as the artefacts created by the two browsers are extremely similar. At the time of writing not all the features of Project Spartan are available. It is thus difficult to deduce all the similarities and differences.

First of all, it is worth mentioning that the back end of Project Spartan is really similar to IE v10+. They both use the same database engine, named ESE database, in order to store information about user activity and to provide a way of recovering crashes occurring in software. It is understandable that these two browsers use the same database engine as the ESE database is used as the core system of many Windows-like features such as Microsoft Exchange Server, Active Directory and Desktop Search. As a result, the structure of the Project Spartan database is also really similar to the latest versions of IE v10+. These allow most of the software created to find artefacts in the ESE database to work in with Project Spartan. However some tweaks need to be implemented to make them work with Windows 10.

However, new features have been introduced with Project Spartan. These features introduces new artefacts that can be of considerable importance in forensic investigations. The new features have been documented in Chapter 4\. As an example, the information stored by Cortana can be valuable for an investigator as it stores the values that are searched using the engine. In this database suggestions made to the user (based on its profile) by the engine are also stored. Other new features such as the reading list or the Web Notes are likely to be of great interest to an investigator.

To conclude, the structure of Project Spartan is in the end similar to the latest versions of IE. New artefacts appeared as the browser offers features that were not implemented on IE. This artefacts have been documented and the upcoming section presents a proof of concept reuniting the artefacts that were not found in the ESE database.

**5.2** **Automated tool**

Not all the artefacts are stored in the ESE database, that is why the authors created a proof of concept able to retrieve the missing artefacts. The script does not retrieve the artefacts present in the database as this database can be read with ESEDatabaseView or with the ESECarve tool. The goal was not to reinvent the wheel but to complete the tool present in the open source community.

This tool (named SpartanLeftovers) can be run next to ESECarve to retrieve the most valuable artefacts from the Project Spartan browser. The script is written in PowerShell 3.0 and allows an investigator to easily summarise the location of the missing artefact in clear and readable csv format. SpartanLeftovers is open source and available in appendix C. The artefacts that are targeted are the favourites, the web notes, the stored tiles and the last unexpectedly closed tabs. Figure 5.1 shows an output of the script. The script lists all the files present in the related directories with their path, creation time, last accessed time, last modification time, owner of the file, attributes and size. From a forensic standpoint the tool can be run on an mounted disk and does not write on the targeted disk. It has been chosen not to access and carve the files in order not to change the access time values this is why the tool only provides the location of the files.

The following figure shows the hash difference created using FTK:

[![Selection_006](https://forensicfocus.files.wordpress.com/2015/07/selection_0061.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_0061.png)It is however advisable to use a write blocker to prevent the connection from the disk to the forensic station to change the disk image and thus the hash.

[![Selection_007](https://forensicfocus.files.wordpress.com/2015/07/selection_0071.png?w=750&h=282)](https://forensicfocus.files.wordpress.com/2015/07/selection_0071.png)**Conclusion**

Currently the way in which and the location where Project Spartan stores its artefacts is very similar to previous versions of Internet Explorer. The browser relies heavily on the ESE database structure, which makes current ways of collecting artefacts not much harder. Most artefacts of features have been analysed that are part of the current development builds of Project Spartan and we suspect that current forensic toolkits that also harvest artefacts of IE will not need to drastically alter their harvesting techniques to also gather artefacts from Project Spartan. Toolkit developers are advised to use the path locations specified in this paper to acquire the artefacts of Project Spartan. The new features such as the Web Notes or Cortana integration can also give insight into the digital footprint a user can leave on a system. These new features should also be added to existing forensic toolkits as well. It should be noted that Project Spartan is still in development and artefacts may change over time (see chapter 7 for more on future work considerations).

The authors also developed a tool which gathers some information analysed in an automated way. The tool is open source and has been designed for forensic/research purposes. It provides a way of recovering the artefacts, left behind by the Project Spartan browser, that are not stored in the ESE database and/or that cannot be retrieved with the ESECarve tool developed by Chivers. The source code (Appendix C) is open to any improvements.

**Future work**

This research outlines some new artefacts that can be gathered within the current development versions of Project Spartan. However, there are a couple of elements to be considered for future work.

This research should be reviewed whenever Microsoft releases a final and stable version of Edge. This research only focused on the development versions of Project Spartan. Current features that have been analysed during this research may change over time, as well as new features that might be added in the future, which we already outlined in section 4.12\. Features like a credential manager, forms storage, synchronisation of connected device are features that would be very interesting subjects for research once they have been implemented.

Currently, the ESE database structure has not been greatly researched, and this also differs per implementation that uses the ESE database structure, such as IE, Exchange and now Project Spartan. Also, as InPrivate (private browsing) artefacts can still be harvested from the ESE database, it would be good to see Microsoft fix this and perform a similar project as done by Chivers[6] to see if these artefacts can still be harvested. However questions arise if this possibility to harvest such information, with the right forensics skills, was made intentionally for forensics purposes.

**Bibliography**

[1] Extensible storage engine. Microsoft Developer Network, 2012.<br>
[2] Exclusive broke the news: Win10 preview version 10123, edge browser new change. IT House Original, 2015.<br>
[3] Forensically interesting spots in the windows 7, vista and xp file system and registry. irongeek, 2015.<br>
[4] Ie passview. Nirsoft, 2015.<br>
[5] Openvpn. OpenVPN Technologies, Inc, 2015.<br>
[6] Howard Chivers. Private browsing: A window of forensic opportunity. 2013.<br>
[7] Jens Lorenz. Notepad++ Plugins – Browse Files at SourceForge.net. <http://sourceforge.net/projects/npp-plugins/files/>, 2015.<br>
[8] Bonnie Malmstr ̈m and Philip Teveldal.o database in internet explorer 10\. 2013\. Forensic analysis of the ese<br>
[9] Joachim Metz. Extensible storage engine (ese) database file (edb) format specification. 2010.<br>
[10] Nir Sofer. ESEDatabaseView – View/Open ESE Database Files (Jet Blue/ .edb files). <http://www.nirsoft.net/util/ese_database_view.html>, 2015.<br>
[11] Junghoon Oh, Seungbong Lee, and Sangjin Lee. Advanced evidence collection and analysis of web browser activity. digital investigation, 8:S62–S70, 2011.<br>
[12] Huwida Said, Noora Al Mutawa, Ibtesam Al Awadhi, and Mario Guimaraes. Forensic analysis of private browsing artifacts. In Innovations in information technology (IIT), 2011 International conference, pages 197–202\. IEEE, 2011.<br>
[13] Jason Weber. Project spartan and the windows 10 January preview build. Microsoft IE, 2015.

**Appendix A**

**Spartan's WebCache database**

As previously mentioned before, Microsoft Project Spartan uses the same Extensible Storage Engine (ESE) database structure as previous versions of IE. The IE 10 ESE database structure has been researched in-depth by Malmstrom and Teveldal. When opening this file with a hex editor, we can see that this version still uses the same format that previous versions use:

[![Selection_008](https://forensicfocus.files.wordpress.com/2015/07/selection_0081.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_0081.png)[![Selection_009](https://forensicfocus.files.wordpress.com/2015/07/selection_0091.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_0091.png)This can be verified when analysing the database with esentutl, which is installed by default on every Windows system. Note that the database is using little endian, so when comparing with a hex dump, every byte range needs to be read in reverse order. For example, the page size is 0x00800000 which we need to reverse in Endianess, so that gives us 0x00008000, which is 32768 in decimal, which means it is 32768 bytes, or 32 KiB per page. Every page's offset starts at 32 KiB increments, which is offset 0x8000 when exploring in a hex dump. If we go to this offset, we can see the start of the first page. The second starts at 64 KiB, and so on.

[![Selection_010](https://forensicfocus.files.wordpress.com/2015/07/selection_0101.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_0101.png)[![Selection_011](https://forensicfocus.files.wordpress.com/2015/07/selection_0111.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_0111.png)[![Selection_012](https://forensicfocus.files.wordpress.com/2015/07/selection_0121.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_0121.png)**Appendix B**

**Download history**

The download history is part of the ESE database. This is an example of :

[![Selection_013](https://forensicfocus.files.wordpress.com/2015/07/selection_0131.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_0131.png)[![Selection_014](https://forensicfocus.files.wordpress.com/2015/07/selection_0141.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_0141.png)[![Selection_015](https://forensicfocus.files.wordpress.com/2015/07/selection_0151.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_0151.png)If one were to convert this directly to ASCII, one would get a similar text like this:

[![Selection_016](https://forensicfocus.files.wordpress.com/2015/07/selection_016.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_016.png)[![Selection_017](https://forensicfocus.files.wordpress.com/2015/07/selection_017.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_017.png)This is not very helpful yet as some character cannot be converted to ASCII. If one were to omit all the unnecessary signs (here question marks) one would get the following text:

[![Selection_018](https://forensicfocus.files.wordpress.com/2015/07/selection_018.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_018.png)One can derive from this string that Piriform Recuva from filehippo.com has been downloaded with Project Spartan.

**Appendix C**

**Powershell script**

[![Selection_019](https://forensicfocus.files.wordpress.com/2015/07/selection_019.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_019.png)[![Selection_020](https://forensicfocus.files.wordpress.com/2015/07/selection_020.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_020.png)[![Selection_021](https://forensicfocus.files.wordpress.com/2015/07/selection_021.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_021.png)[![Selection_022](https://forensicfocus.files.wordpress.com/2015/07/selection_022.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_022.png)[![Selection_023](https://forensicfocus.files.wordpress.com/2015/07/selection_023.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_023.png)[![Selection_024](https://forensicfocus.files.wordpress.com/2015/07/selection_024.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_024.png)[![Selection_025](https://forensicfocus.files.wordpress.com/2015/07/selection_025.png?w=750)](https://forensicfocus.files.wordpress.com/2015/07/selection_025.png)You can contact the authors on james.gratchoff@os3.nl (James Gratchoff) and guido.kroon@os3.nl (Guido Kroon). Download a PDF version of the original paper [here](https://forensicfocus.files.wordpress.com/2015/07/ccf-project-spartan-forensics-james-guido.pdf).

## Like this:

<span class="button">
  <span>Like</span>
</span>

 

<span class="loading">Loading…</span>

[]()

## _Related_
