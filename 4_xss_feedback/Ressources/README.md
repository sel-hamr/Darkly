# Definition XSS (Cross Site Scripting)

Cross-site Scripting (XSS) is a security vulnerability usually found in websites and/or web applications that accept user input. Examples of these include search engines, login forms, message boards and comment boxes.  
Cybercriminals exploit this vulnerability by inputting strings of executable malicious code into these functions. This injects the malicious code into the targeted website’s content, making it a part of the website and thus allowing it to affect victims who may visit or view that website. The code may also present itself as transient content that isn’t actually part of the website but only appears to be to the visitor. This makes it look like the website is indeed compromised by cybercriminals.

## What is a Stored XSS Attacks

Stored attacks are those where the injected script is permanently stored on the target servers, such as in a database, in a message forum, visitor log, comment field, etc. The victim then retrieves the malicious script from the server when it requests the stored information. Stored XSS is also sometimes referred to as Persistent or Type-I XSS.

# Steps

After visiting `http://IP_ADDRESS/index.php?page=feedback` we can see that there's two input boxes  
Let's test them to check if the're vulnerable to xss
By sending `<b>hi</b>` for both name and message  
After sending inputs we see that name is vulnerable to xss
The funniest thing here is if we send a valid xss to name its won't work but if we send anything tha related to the word `script` like (s) or (p) it'll display the flag

# Flag: 0FBB54BBF7D099713CA4BE297E1BC7DA0173D8B3C21C1811B916A3A86652724E

# Prevent XSS

To keep yourself safe from XSS, you must sanitize your input and than store them.

# Resources

### [XSS OWASP](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)

### [XSS Web Security Academy](https://portswigger.net/web-security/cross-site-scripting)

### [XSS Exercise 1](https://www.hacksplaining.com/exercises/xss-stored)

### [XSS Exercise 2](https://application.security/free-application-security-training/cross-site-scripting-vulnerability-in-tiktok)
