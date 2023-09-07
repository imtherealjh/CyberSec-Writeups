# Capture!
Can you bypass the login form?

https://tryhackme.com/room/capture

Author:tryhackme and toxicat0r

## Recon
### Nmap Scan
At the commencement of each CTF challenge, our initial step consistently involves conducting an nmap scan.

```
sudo nmap -sC -sV -O -T4 10.10.41.60 -vv -p- -oG all
```

![Nmap Scan](/THM/Bypass/images/Nmap.PNG)

The nmap scan show that port 80 is open.

### Gobuster Scan
![Gobuster Scan](/THM/Bypass/images/Gobuster.PNG)

I've exhausted my attempts using different wordlists, but I still haven't been able to uncover any concealed directories that we can utilize for this challenge.

### Website
![Initial Website](/THM/Bypass/images/Initial.PNG)

I began by examining the webpage for any potential hints related to the challenge, but I couldn't find any clues. As a next step, I decided to test the input fields for possible vulnerabilities, particularly focusing on SQL injection vulnerabilities. During this testing, I entered the input "admin' OR 1=1 --" to check for any signs of vulnerabilities in the system's response.

After experimenting with several different input values, I observed that all the error messages or responses were identical.

![Test Input](/THM/Bypass/images/TestInput.PNG)

I decided to open Burp to inspect the server's responses and gain a deeper understanding of how it was handling the input I provided.

![Burp Request](/THM/Bypass/images/Burp1.PNG)

![Burp Response](/THM/Bypass/images/Burp2.PNG)

It appears that the server is returning a web page as a response instead of the expected JSON response

## Exploitation
At this stage, I recalled that the challenge had provided a text file containing usernames and passwords. With this in mind, I proceeded to create a script for brute-forcing the login page using these credentials. This approach can be an effective way to test the login page's security and gain access with the provided credentials.

![Initial Script](/THM/Bypass/images/InitialScript.PNG)

![Initial Script Result](/THM/Bypass/images/InitialScriptResult.PNG)

I believed that I had identified the correct username for the challenge and decided to test it by entering it into the login page using my web browser. 

![Initial Brute Force Attempt](/THM/Bypass/images/Captcha.PNG)

It seems like a new issue has arisen on the webpage, indicating that there have been too many bad login attempts.

It appears that solving a CAPTCHA is now required to continue attempting to brute-force the login page. 

At this point, I began my research on techniques for extracting numbers from HTML pages. During my investigation, I recalled that I could employ regular expressions (regex) to parse and retrieve numeric data from the web page's response.

Therefore, I visited the website https://regex101.com and attempted to create a regular expression (regex) pattern for extracting the captcha from the web page.

I managed to come out with the regex that works for our use case.

```
(\d+ [+\-\/*] \d+)
```

Time to add it to our code...

![Modified Script](/THM/Bypass/images/ModifiedScript.PNG)

After executing the script, we successfully identified the correct username!

![User Found](/THM/Bypass/images/UserFound.PNG)

At this juncture, I decided to verify whether the script was functioning as intended. When I entered the username into the website, I encountered a new error or issue.

![New Error](/THM/Bypass/images/NewError.PNG)

Now that we've discovered the correct username, it's time to modify the script to search for the password.

![New Script](/THM/Bypass/images/NewScript.PNG)

After running the script, we managed to find out the password!

![Password Found](/THM/Bypass/images/PasswordFound.PNG)

After running the modified script, we have successfully identified the password!

![Credential Entered](/THM/Bypass/images/Credential.PNG)

![Flag Found](/THM/Bypass/images/Flag.PNG)

Viola, we managed to find the flag!

## Conclusion
In this room, the task involves a straightforward brute-force attack on the login page. Personally, I had no prior experience in scripting a brute-force script for a login page but was able to learn and apply this skill while tackling this challenge.