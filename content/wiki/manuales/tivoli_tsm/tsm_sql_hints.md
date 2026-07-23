---
title: "TSM SQL Hints"
date: 2012-04-10T10:06:00-03:00
---

## SQL tips and syntax

The basic SQL syntax is\
SELECT ALL \| DISTINCT \|columnname(,columnname)\
FROM tablename(, tablename)\
WHERE ‘column selection conditions’\
GROUP BY columnname\
HAVING ‘row selection conditions’\
ORDER BY columnname ASC\| DESC

The standard SQL operator, ‘=’, is case sensitive when comparing strings. You can use SELECT DISTINCT, to eliminate duplicate records, and get data from more than one table using the JOIN operator. You can also combine WHERE statements using the AND operator.

The first thing you need, is to know what TSM tables are available to query. These are described in the next section. After that, the best way to learn SQL is to try it out. It’s a read only query language! The examples below might help explain what the syntax means, otherwise, try the following sites.\
\
[www.sqlcourse.com](http://www.sqlcourse.com/)\
[www.sqlcourse2.com](http://www.sqlcourse2.com/)\
[www.dcs.napier.ac.uk/~andrew/sql](http://www.dcs.napier.ac.uk/~andrew/sql/)

TSM SQL query command does not support the full SQL language syntax. The following operations will not work:

- UNION
- INTERSECT
- EXCEPT
- subqueries that return multiple values
- You cannot use a semicolon as a command terminator

You can do maths in SQL statements, for example

```
  SELECT AVG  (total_mb/1024) -  
  AS "Average Total GB" - 
  FROM auditocc
```

You can select several columns, or items from a table by separating them with commas, like

```
 select platform_name,count from nodes
```

or you can join items together either of the two examples below will work.

```
 select concat(FILESPACE_NAME,HL_NAME, LL_NAME) from backups
 select filespace_name || hl_name || ll_name from backups
```

You can combine two tables together and select columns from each like this

```
SELECT nodes.domain_name,summary.activity FROM nodes, summary
```

or you can simplify the expression by giving the tables an alias

```
SELECT nn.domain_name,ss.activity FROM nodes nn, summary ss
```

Note that the aliases have two characters. For some reason TSM does not always like a single character. It seems to really object if you abbreviate ‘summary’ to ‘s’.

If you invoke SQL from a script then it may ask for confirmation to proceed, for example if it may check that you are happy to process a lot of output. You can suppress the confirmation messages with the option -noconfirm

[back to top](#top)

---
## What are the TSM tables?

To find out what TSM tables exist and what they contain, run the following queries\
select \* from syscat.tables\
select \* from syscat.columns\
select \* from syscat.enumtypes

The **SUMMARY** table contains a lot of useful entries for general statistics. A couple of fields are SUMMARY.ACTIVITY AND SUMMARY.SUCCESFUL. The activity field currently contains; ‘TAPE MOUNT’, ‘FULL_DBBACKUP’, ‘INCR_DBBACKUP’, ‘EXPIRATION’, ‘RECLAMATION’, ‘MIGRATION’, ‘MOVE DATA’, ‘STGPOOL BACKUP’, ‘BACKUP’, ‘RESTORE’, ‘ARCHIVE’ and ‘RETRIEVE’. The successful field can be ‘YES’ or ‘NO’. However you cannot rely on the summary table to report on the success of client events like backup and restore as it just reports on progress so far.

The DATE field in the **EVENTS** table does not support expressions like ‘scheduled_start \>= current_timestamp-24 hours’. If you issues this query at 14:00 it will return all events that started after midnight today, but not those between 14:00 and midnight yesterday. You can get the correct results by combining the relative time stamp with a constant timestamp, for example if yesterday was the 25th March then this will work.

```
  scheduled_start > '2006-03-25' and scheduled_start >= current_timestamp-24 hours
```

This is no good if you want to schedule a query, but in that case you can simply use a very early fixed timestamp date, for example

```
 select node_name, schedule_name, scheduled_start, status -
   from events -
   where scheduled_start >= '1900-01-01' and -
         scheduled_start >= current_timestamp - 24 hours
```

[back to top](#top)

---
## SQL output formatting

If you enter SQL queries from the command line in the browser, you get the results in tabular format. It is possible to execute SQL from a host command line, and then you can pipe the command to a file and get the results in comma delimited format for importing to an Excel spreadsheet or similar.\
The command is

```
dsmadmc -id=adminid -password=adminpassword -commadelimited
'select etc '  > filename
```

Command output direction can be a bit complicated as it works differently for different operating systems. In general the ‘\>’ symbol will direct output to a file, but it is also a valid SQL mathematical operator. If the ‘\>’ symbol has spaces on both sides of it it will be considered as output redirection. If it has no space on either side, it will be considered as mathematical greater than.

So for example\
select \* from summary \> summary.txt will direct lots of output text to a summary file, while\
select \* from summary where date\>current_timestamp - 24 hours will look for events that happened today. Of course you can combine these as\
select \* from summary where date\>current_timestamp - 24 hours \> summary.out

If you run these commands in batch, the operating system might try to interpret the redirection command as greater than even if it is surrounded by spaces. In UNIX and LINUX you can put a slash before the command /\> but the easier way is to put the whole command in quotes. “select \* from summary where date\>current_timestamp - 24 hours \> summary.out”

[back to top](#top)

---
## Using Indexed Columns to speed up queries

TSM SQL queries can run for a long time, and use up a lot of resource. This is usually because you are searching the whole database to get the data you want. You can reduce the amount of database searching by selecting specific data from an indexed column using a WHERE statement. To find out which columns are indexed, use the query

```
 select * from syscat.columns
```

A partial result looks like

```
 TABSCHEMA: ADSM
      TABNAME: MEDIA
      COLNAME: LRD
        COLNO: 9
 INDEX_KEYSEQ: 
  INDEX_ORDER: 
     TYPENAME: TIMESTAMP
       LENGTH: 0
        SCALE: 0
        NULLS: TRUE
      REMARKS: Last Reference Date

    TABSCHEMA: ADSM
      TABNAME: MGMTCLASSES
      COLNAME: DOMAIN_NAME
        COLNO: 1
 INDEX_KEYSEQ: 1
  INDEX_ORDER: A
     TYPENAME: VARCHAR
       LENGTH: 30
        SCALE: 0
        NULLS: FALSE
      REMARKS: Policy Domain Name
```

This tells you that the DOMAIN_NAME column in the MGMTCLASSES table is indexed, but the LRD column in the MEDIA table is not. So if you run a query like

```
 SELECT * FROM MGMTCLASSES -
 WHERE DOMAIN_NAME = 'DO_TDP'
```

Then you can expect your query to be quite fast.

[back to top](#top)

---
## TSM SQL date formats

The timestamp format is:

```
   'yyyy-mm-dd hh:mm:ss.nnnnnn'

  yyyy = year
  mm = month
  dd = day
  hh = hours
  mm = minutes
  ss = seconds 
  nnnnnn = fraction of a second
```

‘ss’ and ‘nnnnnn’ are both optional. When referring to a timestamp, put it in single quotes, for example to select records that started after 12:00 on July 21st you would specify **start_time \<= ‘2005-07-21 12:00:00’**.\
You can split the time stamp using a date function or a time function. For example to select records that began on July 21st, use **date(start_time) \>= ‘2005-07-21’**.\
If you just want records that started after 21:00, you would add **time(start_time) \<= ‘12:00:00’**.

[back to top](#top)

---
## How to combine two TSM tables in one query

It is possible to combine two TSM tables in one query, but be aware that the TSM database is not really relational, so table joins take ages and use a lot of resource. It may be faster to do two queries, copy out the results then combine the data with an external program.\
The key to database joins is

- Both tables must have one column that contains the same data
- You must give each table an alias name for reference purposes using ‘table name alias’ in the FROM statement
- You select the common column in both tables using aliasname.table name
- You then join the data with WHERE alias1.column2=alias2.column2

For example

```
 SELECT pct_utilized, node_name, -
  vm.volume_name, vu.volume_name -
 FROM volumes vm,volumeusage vu -
 WHERE node_name='NODE01' -
 AND vm.volume_name=vu.volume_name
```

This combines the percent volume utilised column from the volumes table with the nodename column in the volumeusage table, combined with the volume column from each. Be aware that this is a really CPU intensive query.

[back to top](#top)

---
## How many client nodes are registered by domain

```
select domain_name,num_nodes -
 from domains
```

Result -

```
 DOMAIN_NAME       NUM_NODES
 ------------------     ---------
 DO_AIX                          39
 DO_HOLAN                     61
 DO_HOSAN                      2
 DO_LOTUSNOTES             34
 DO_TDP                       32
 DO_TSMSERV                  4
 DO_UDB                      7
 DO_WINNT                  126
 STANDARD                    0
```

[back to top](#top)

---
## How many client nodes are registered by platform?

```
 select platform_name,count(*)as 'Number of Nodes' -
 from nodes -
 group by platform_name
```

Result -

```
 PLATFORM_NAME        Number of Nodes
 ----------------     ---------------
                                   16
 AIX                               57
 AIX-RS/6000                        4
 DB2                                2
 Mac                                2
 NetWare                           59
 OS/2                               1
 SUN SOLARIS                        6
 TDP Domino                         3
 TDP Domino NT                      2
 TDP Oracle AIX                     6
 WinNT                            147
```

[back to top](#top)

---
## How do I find locked nodes?

```
 select nodes,locked from nodes -
  where locked='YES'
```

Result -

```
 NODE_NAME                     LOCKED
 ----------------     ---------------
 D001SMH01                        YES
```

The following search should find them

[back to top](#top)

---
## Query all tapes for a node

How do I find all the tape volsers associated with a specific node?

```
  
  select distinct node_name,volume_name,stgpool_name -
  from volumeusage -
  where node_name='xxxxx'
```

[back to top](#top)

---
## What tapes were used today?

How do you find out what tapes were used on a specific day.

```
  select volume_name,last_write_date -
  from volumes -
  order by last_write_date
```

[back to top](#top)

---
## Library inventory

How can I display an inventory of my library in order of slot number

```
  select home_element, volume_name -
  from libvolumes -
  order by home_element
```

[back to top](#top)

---
## Which volume has my file

How can I find out which volume contains a specific file?

```
  select volume_name,node_name,filespace_name,file_name -
  from contents -
  where node_name='nodename' -
    and filespace_name='filespace' -
    and file_name='filename'
```

[back to top](#top)

---
## List all volumes that are not in READWRITE status

select VOLUME_NAME,ACCESS from volumes where access !=‘READWRITE’\
Result -

```
 VOLUME_NAME            ACCESS
 ------------------     ----------
 QZ1039                 READONLY   
 QZ1170                 READONLY
```

[back to top](#top)

---
## How many scratch tapes are there?

How do I tell how many scratch tapes we have?

```
  select count(*) as Scratch_count -
  from libvolumes - 
  where status='Scratch'
```

If you have more than 1 library, you can find all your scratch tapes using the query

```
  select LIBRARY_NAME,count(*)'scratches' from libvolumes where -
  upper(status)='SCRATCH' group by LIBRARY_NAME
```

Thanks to Sven Neirynck of Compu-mark for that tip

TSM has a MAXSCRATCH parameter which is set independently for each storage pool. This defines the maximum number of tapes that each tape pool can contain. The following query will display how close each pool is to its limit.

```
 SELECT STGPOOLS.STGPOOL_NAME, STGPOOLS.MAXSCRATCH, -
 Count(STGPOOLS.MAXSCRATCH) as "Allocated_SCRATCH", -
 STGPOOLS.MAXSCRATCH-count(STGPOOLS.MAXSCRATCH) as "Remaining_SCRATCH" - 
 FROM STGPOOLS,VOLUMES -
 WHERE (VOLUMES.STGPOOL_NAME = STGPOOLS.STGPOOL_NAME) - 
 AND ((STGPOOLS.DEVCLASS="3590_CLASS")) -
 GROUP BY STGPOOLS.STGPOOL_NAME, STGPOOLS.MAXSCRATCH
```

Typical output looks like

```
STGPOOL_NAME            MAXSCRATCH     Allocated_SCRATCH     Remaining_SCRATCH
------------------     -----------     -----------------     -----------------
ARCHTAPEPOOL                   100                     5                    95
CARTPOOL                      1340                   932                   408
VIRTCARTPOOL                   200                    13                   187
```

[back to top](#top)

---
## How many tapes can I reclaim by changing the reclamation threshold?

```
 select count(*)from volumes -
 where stgpool_name='poolname' -
  and upper(status)='FULL' -
  and pct_utilized
```

# Fuente

<http://www.lascon.co.uk/d005104.htm>
