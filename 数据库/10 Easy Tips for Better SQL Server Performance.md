# 10 Easy Tips for Better SQL Server Performance

MICROSOFT SQL SERVER lets you quickly build powerful and reliable database applications, but making those apps perform their best is tricky. Luckily, database administrators and developers can use several simple techniques to coax more speed out of a SQL Server database. Here are 10 such performance-boosting tactics you can quickly and easily apply to your SQL Server database applications.

Tip 1: Assign Data Type Numeric Columns
Database administrators and developers often assign the CHAR (character) data type to columns containing only numeric information. Character data columns can hold anything and are a good catch-all. However, specifying CHAR for numeric columns can decrease performance with filters and join conditions and can increase storage requirements. For example, suppose you're developing an application that tracks equipment stored at several sites. Tables 1 and 2 show the application's inventory_header and inventory_detail. As you design your application's tables, you notice that the part_number column always contains a number between 1 and 12,000. However, when you create the tables, you define all these columns with a char(20) type and place an index on these columns in each table.

A commonly used report in your application prints a listing of the inventory-item counts at all locations. The following statements make this report printout possible:

MICROSOFT SQL SERVER lets you quickly build powerful and reliable database applications, but making those apps perform their best is tricky. Luckily, database administrators and developers can use several simple techniques to coax more speed out of a SQL Server database. Here are 10 such performance-boosting tactics you can quickly and easily apply to your SQL Server database applications.

Tip 1: Assign Data Type Numeric Columns
Database administrators and developers often assign the CHAR (character) data type to columns containing only numeric information. Character data columns can hold anything and are a good catch-all. However, specifying CHAR for numeric columns can decrease performance with filters and join conditions and can increase storage requirements. For example, suppose you're developing an application that tracks equipment stored at several sites. Tables 1 and 2 show the application's inventory_header and inventory_detail. As you design your application's tables, you notice that the part_number column always contains a number between 1 and 12,000. However, when you create the tables, you define all these columns with a char(20) type and place an index on these columns in each table.

A commonly used report in your application prints a listing of the inventory-item counts at all locations. The following statements make this report printout possible:

select    b.location,  a.part_number, 
          a.part_desc, b.part_count 
from      inventory_header a, inventory_detail b 
where     a.part_number = b.part_number
order by  b.location, a.part_number
If you define the part_number columns in Tables 1 and 2 as char(20), the join operation requires the engine to compare the values in each table byte by byte, up to 20 times per row. Because you know the part_number column is always numeric and between 1 and 12,000, you can redefine the part_number column's data type in both tables as smallint. This data type can hold values between -32767 and +32767.

This data type helps your application perform better in several ways. First, because SQL Server stores smallint values in only 2 bytes of data, you save a significant amount of CPU work during join operations or filter conditions, especially if you are processing several rows. Instead of comparing up to 20 bytes to see whether two rows are logical join candidates, the engine can finish its work after comparing only two bytes. Thus, using smallint instead of char(20) saves you 18 bytes per row per table for all indexes that use this column.

Tip 2: Prevent Substring Searches 
When search criteria in a SQL statement start at the leftmost position of a column and move right, searching through the indexed character data is inherently easier than when your search criteria begin in the middle of the data--that is, in a substring. For example, suppose you have to locate two types of information in a phone book: all last names starting with a capital L and all entries with a lower-case l as the third letter in the last name.

The first request is easy because the phone book is already indexed (last name, first name). You simply turn to the L section of the book and read through the names until you find the first entry with a last name starting with M.

This type of processing is analogous to running the SQL statement

select * from names 
where last_name >= 'L%'' 
     and last_name < 'M%''
If an index is on the last_name column in the names table, the optimizer uses the index to help process this query.

For both machines and humans, the second request is harder to process than the first. With the phone book, you have to read the first three letters of every entry from cover to cover and mark all entries that have l as the third letter.

This type of processing is analogous to running a SQL statement that searches for a substring within the last_name column.

select * from names
where substring(last_name,
     3,1) = 'L'' 
Although an index is on last_name, the optimizer can't use it because you're not beginning your search from the leftmost byte of the last_name column. To satisfy your request, the optimizer has to operate sequentially on what could be an enormous table, which definitely hurts performance.

TABLE 1: Inventory_header Table
part_number	part_desc	
78	Monochrome ASCII terminal
143	Monochrome 3270 terminal
TABLE 2: Inventory_detail Table
location	part_number	part_count
Phoenix	78	0
Detroit	78	25
New York	78	148









TABLE 3: Bonus Program Participation
account_number	participated	
55095543	Y	
55095544	Y	
55095545	N	
55095546	Y	
55095547	N	
55095548	Y	
If you must sometimes sort on a noninitial substring, consider revising your database design. Try to divide the column in question into several smaller columns that you can index. This revision will help accelerate searching, joining, and sorting.

Tip 3: Don't Assign Too Much Memory
As part of installation and administration, the SQL Enterprise Manager lets you set the amount of memory dedicated to SQL Server. If you allocate too much memory to SQL Server, you increase the burden on NT by denying it valuable memory resources necessary to service other tasks, such as other applications and the operating system. This burden can result in swapping, which occurs when the OS runs out of memory and starts resorting to the disk to make up the shortfall. In fact, too high a memory value can make booting SQL Server difficult, if not impossible. Swapping can quickly negate any performance gains you obtain by increasing the cache hit ratio. (The cache hit ratio is a measure of the percentage of time that SQL Server finds information in memory, rather than on disk. Disk I/O is expensive, so reducing it is a good idea whenever possible. A high cache-hit ratio means that the engine can avoid this costly effort.)

The most logical approach to allotting memory to SQL Server is to raise the value gradually and monitor performance carefully between changes. If caching rates don't improve after an increase, you've reached the optimal memory value. Also keep track of the swap rate, because raising the memory value can inadvertently increase swapping.

Tip 4: Avoid Highly Duplicate Indexes
A highly duplicate index contains a limited number of unique values. For example, if you place an index on a column that contains only two or three values, that index is highly duplicate.

Having a highly duplicate index on a column is worse than having no index at all. The database engine must work a lot to process a highly duplicate index during important operations such as inserting, updating, or deleting data. For example, suppose you have a table that tracks customers' participation in a special bonus program, as you see in Table 3. If you place an index on the participated column, you have only two possible values for that index: Y or N. When you add a new row into this table, the engine must add the new value to the existing index.

This addition can cause the undesired side effect of internal index restructuring to handle the new value. Deleting, querying, or sorting based on the index also makes the engine work harder, thus adding many unnecessary I/O operations to your system and likely degrading performance. Instead of using the highly duplicate index, having the engine go directly to the data and read it sequentially is probably faster and simpler.

You can determine whether an index is highly duplicate by running the Database Consistency Checker (DBCC) show_statistics command or selecting the Index Distribution Statistics option on the Index Information screen in SQL Enterprise Manager. With other information, the statistics utility reports on the index's selectivity (as you see in Screen 1), the relative number of distinct values in the index. In this case, only two values are possible, which yields a poor degree of selectivity.

When confronted with such a poor degree of selectivity, SQL Server may not even use the highly duplicate index at all to retrieve information. However, the highly duplicate index still causes extra work for SQL Server during inserts, updates, and deletes.

To avoid performance problems when you must sort or search on a highly duplicate value, combine the column in question with a column containing more selective values. This step creates a composite index. In the previous example, the correct composite index is

create index bonus_acct_
   participated_ix on
     bonus(account_number, participated) 
Because the account_number column is more selective than the participated column, combining these two columns yields a more selective and efficient index. You can now use this index to search for and sort by account_number for all customers who have participated in the bonus plan. (Don't forget that to use a composite index in filtering or sorting data, your SQL statement must always specify columns in a table from left to right.)

Tip 5: Use the update statistics Command 
To process indexed database operations as efficiently as possible, SQL Server keeps track of index key values, including the location of index key values and the values themselves. To do so, SQL Server runs the update statistics command when you create or re-create an index.

If you expect your key values to change over time, periodically rerun this important command. If your key values change and you don't refresh the key distribution statistics by running update statistics, the optimizer can make a wrong query-processing decision based on this old information. Although old key distribution statistics won't affect the data the engine returns, they can substantially slow system performance.

Tip 6: Prevent Long Transactions 
SQL Server uses the transaction log to track alterations to data. In addition to protecting your data, the transaction log has an important effect on database speed. One crucial transaction log performance issue is the length of your transactions. Keep your transactions as brief as possible because long, open transactions can wreak havoc on database application performance.

Suppose you've developed a client support application for a large insurance company. This application lets customer service reps give customers information about their policies over the phone. One day, a rep is working with a customer who has reported a billing error. The rep pulls up the application's payment record screen to modify the customer's record. After the phone call, the rep goes to lunch, leaving the customer's record on screen to complete it later. If the client-support application resembles the following pseudocode example, you have a potential performance problem.

get the customer's name and account number
begin transaction
build up a query
retrieve the record
display the record
prompt the user for changes
update the record
check for errors
if no errors
commit transaction
else
rollback transaction
If the rep takes a break after pulling the record but before changing it, a long transaction can result. Because SQL Server can reclaim transaction log space for only finished transactions, the transaction log can't be freed until this transaction is complete. And all data modification operations will probably come to a grinding halt until the log can restore its empty space.

Modifying the above example to avoid the long transaction problem produces the following pseudocode:

get the customer's  name and account number
build up a query
retrieve the record
display the record
prompt the user for changes
begin transaction
update the record
check for errors
if no errors
commit transaction
else
rollback transaction
By beginning the transaction only after a user enters changes to the record, you eliminate the potential for a long transaction. You can add some logic to the code to ensure that another user doesn't change the record's contents between the time you select the record and when you update it.

Tip 7: Place the Transaction Log on a Separate Device
SQL Server lets you choose where to store the transaction log. For most read and write applications, the transaction log is a heavily accessed disk structure because it is at the core of SQL Server's data integrity architecture. Because I/O is often the bottleneck on today's fast systems, spreading the I/O load among as many devices as possible is a good idea. To accelerate I/O performance, consider placing the transaction log on a different physical device from the database. Another reason to put the transaction log on a different device is so that you can periodically perform transaction log dumps without having to back up the data.

Tip 8: Use One Large Database
SQL Server developers and database administrators frequently make the mistake of dividing information into several small databases, rather than storing the data in one large database. Why is this a problem? One way SQL Server speeds processing is to use its memory buffers (i.e., cache) to reduce disk I/O. These memory buffers hold data, indexes, and stored procedures. When a SQL Server application must access one of these resources, the engine first looks in the appropriate memory cache area. On a busy system, many such requests are satisfied from memory rather than from disk. Reducing the number of times the engine must make a costly disk inquiry accelerates access.

If you divide your data into many small databases, chances are users can't share much of the information stored in memory, which leads to degraded performance. For example, suppose you maintain a manufacturing system that supports your firm's worldwide operations and resides on one large server. You divide your data into separate databases, according to geography--for example, WestEurope, EastEurope, WestNorthAmerica--to let users access data for only their region. As part of this strategy, you duplicate many lookup tables in the small databases.

The combination of separate, small databases and duplicated lookup tables greatly reduces the potential for memory caching. Although users can be examining identical copies of lookup tables in each database, the database engine can't take advantage of memory caching across databases.

For example, what happens when Ted looks for part number 20993 in the WestNorthAmerica parts table and Alex must find the same part in the EastEurope database? Ted pulls the appropriate page into memory, yet Alex won't see that page: Although Alex is reading a row on the same page, he retrieves it from a different database. If one large database holds this data, Alex's process doesn't have to issue a disk read request because the page is already in memory. If you multiply the number of unnecessary read requests by the number of users, you can see how performance can suffer.

Lost caching opportunities also affect stored procedures. Besides data, users can share stored procedures (although the engine must generate query plans if the procedure is already in use).

Although you sometimes need multiple small databases, try to use one large database. To limit data access to appropriate users, you can choose among several methods, such as key design and views.

Tip 9: Index Temporary Tables 
Temporary tables (work tables) are transitory storage locations that SQL Server uses to satisfy processing demands. SQL Server and you can create these tables.

Often the engine can build an implicit temporary table. For example, SQL Server can create temporary tables when it can't use available indexes to process information. When SQL Server automatically creates work tables, aside from reworking your queries or altering database index structure, you don't have much control over these work tables. For example, you can't create indexes on them, but SQL Server often builds indexes on the key columns of implicitly created work tables.

You can specify the create table command to build explicit temporary tables to hold data until the application finishes. When you explicitly create a temporary table, you largely control how that table is indexed, if at all. If you expect the work table to contain large amounts of data, you can create indexes where appropriate. Doing so helps reduce the time needed to locate information in your temporary table, which should lead to better application response.

Tip 10: Control the Amount of Published Data
SQL Server provides powerful replication features as part of its core capabilities. When you use these features, following good techniques to replicate information is important. For example, just because you can replicate a specific table doesn't mean you should. Sometimes, administrators replicate unnecessary tables. This replication increases the work for all machines in your environment, adds to your network's burdens, and enlarges the amount of disk space necessary on subscription machines.

Before you add a table to a publication, decide whether to replicate the table. If you do, you then have to decide whether to replicate horizontally or vertically. Either operation will reduce the amount of information SQL Server passes among systems.

For example, to replicate an account table that has several million rows, you must decide whether this table is a good candidate for replication: Do multiple systems need to see the table at the same time? If the answer is yes, you have to decide whether all subscription servers will want all the information in the table. Chances are you can subdivide this data horizontally (across rows) or vertically (across columns). Taking these extra analysis steps can significantly improve performance, because you can sharply reduce the amount of information flowing among your systems.

Future Reading
You can immediately start improving SQL Server performance by applying any of these techniques. For in-depth information about the tips in this article and other performance-enhancing ideas, see the forthcoming Microsoft SQL Server: Designing and Building a High Performance Database (Prentice Hall, Upper Saddle River, NJ, November 1996, ISBN 0-132-66222-1).