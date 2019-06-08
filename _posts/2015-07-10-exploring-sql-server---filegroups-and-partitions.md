---
layout: post
tags: [SQL Server]
---
Welcome to the next exciting<sup>[1]</sup> instalment of [Exploring SQL Server]({% post_url 2015-05-29-exploring-sql-server %}), in which we will have a very quick look at Filegroups and Partition Schemes.

<sup>[1] Excitement not guaranteed.</sup>

#### Isn't it just one big file?
No, by default a SQL Server database requires at least two files the database and the log. For recovery reasons these should really be on separate disks. From here on out I'm just going to ignore the log file for simplicity's sake.

By default you will have one database file, which is great until the database gets much larger and this will cause performance issues. OK, so I add a second file and I'll just put a max file size on each. That works fine, the files will grow as needed and I can just add more files.

#### So why do I need Filegroups and Partitions?
The answer is if you want to group certain parts of the data in certain files or on certain drives. A filegroup is what it sounds like - it is a group of files, so you can talk about the group rather than an individual file - this means you can add/remove/change names of the files without affecting the objects in the database. When you create an object you can say which filegroup it should live in, so your stored procedures may live in a separate location to your tables and data.

You may want even more finegrained control of how the database is stored down to the data level, and this is where partitions come in. A partition is made of two parts a function and a scheme. The function says how to split the data (e.g. <1997, 1997,1998,>1998), and then you apply this to a table or index and say which filegroup each partition should reside in.

#### Why would I bother partitioning my tables?
If you are doing operations on distinct groups of data then you can just manipulate that file and not the file for the whole table. For example I wish to do something to all the rows before 1997, I can go straight to that partition and ignore everything from the other partitions gaining a performance boost because I don't have to look at them or do anything to their on disk representations.

Hopefully this sheds some light on what filegroups and partitions are, and why you might want to use them.
