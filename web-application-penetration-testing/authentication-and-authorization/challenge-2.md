# Challenge#2 - Music Shop 1

## Credentials

Username: Mario
Password: pass123

## Objectives

- At first, you want to understand the voting system and find any flaws.
- Then, you should take advantage of these flaws to have any other band, beside Bieber, win the competition and be in the first position.
- There's only one secret that will be given to you when some serious music will be on top of the chart.

## Hints

- Recommended tools
  - Burps suite
  - Web Browser
- Insecure direct object reference
- Cookie manipulation

## What you will learn

- How to reverse engineer the functioning of a web application
- How to configure advanced options of Burp Suite
- How to use Burp Suite Match and replace feature


## Steps

With burp suite turned on, login to the system and vote in any band besides Bieber.

On the burp side, on history you have a request like below.

```
POST /vote.php HTTP/1.1
Host: 1.challenge.auth.site
Cookie: PHPSESSID=f08v4921ulho1kigij3dvkr7j6; username=MzAx

band_id=10
```

You have a cookie username with the value 'MzAx' that is the number 301 encoded in base64.

Now, send this request to burp intruder.

Go to positions and clear all positions.

Select only the username value. Change the PHPSESSID session cookie to your own. Keep the attack type as snipper.

```
Cookie: PHPSESSID=f08v4921ulho1kigij3dvkr7j6; username=§MzAx§
```

On payload select payload type as numbers.

```
From: 0
To: 500
Step: 1
```

Now in the payload processing, add encode and then base64-encode. This will encode the payload before send.

Then start the attack.

Now, back on the voting page you can see that your band is now the winner.
