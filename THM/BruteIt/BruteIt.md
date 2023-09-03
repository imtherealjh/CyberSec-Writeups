# Brute It!
Learn how to brute, hash cracking and escalate privileges in this box!

https://tryhackme.com/room/bruteit

Author:ReddyyZ

## Recon
### Nmap Scan
At the commencement of each CTF challenge, our initial step consistently involves conducting an nmap scan.

```
sudo nmap -sC -sV -O -T4 10.10.41.60 -vv -p- -oG all
```

![Nmap Scan](/THM/BruteIt/images/Nmap.PNG)

The nmap scan show that port 22 and 80 is open.

I explored the website for hints to advance in the challenge, but didn't find any. Consequently, I chose to utilize Gobuster to scan for hidden directories for the challenge.

### Gobuster Scan
![Gobuster Scan](/THM/BruteIt/images/Gobuster.PNG)

We observed the presence of a hidden directory named "admin."

### Website
![Initial Website](/THM/BruteIt/images/LoginPage.PNG)

Upon visiting the "admin" sub-directory, we came across a login page. Subsequently, I thoroughly explored the website in search of potential hints to proceed with the challenge.

![Website Hint](/THM/BruteIt/images/Login.PNG)

We discovered that there is a user named "john," and it indicates that the username for the login page is "admin."

Certainly, keeping in mind that the room's theme revolves around brute-forcing and hash-cracking, and with the hint from the TryHackMe page explicitly suggesting to "Employ Hydra for brute force," it's evident that Hydra should be used as the tool of choice for this particular task.

Consequently, I conducted research on how to utilize Hydra for performing a brute-force attack on a login page.

## Exploitation
Following my research, I successfully find the command necessary  to execute for the challenge.
```
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.165.147 http-post-form "/admin/:user=admin&pass=^PASS^:Username or password invalid"
```

After executing the command for a while, we managed to get the password for the login page

![Hydra Success](/THM/BruteIt/images/HydraSuccess.PNG)

After logging in with the username and password that Hydra successfully cracked, we gained access to the website.

![Login Success](/THM/BruteIt/images/LoginSuccess.PNG)

The website has provided us with a flag and a private RSA key for SSH login.

To begin, we will copy the private RSA key provided by the website and save it as a file named "id_rsa" to store its value.

Next, we will use the following command to attempt to crack the passphrase for the private RSA key.

```
ssh2john id_rsa > forjohn
john --wordlist=/usr/share/wordlists/rockyou.txt forjohn
```

After allowing John the Ripper to run for some time, we successfully obtained the passphrase for the private SSH key.

![John Success](/THM/BruteIt/images/JohnSuccess.PNG)

Subsequently, we will employ the "id_rsa" file and the cracked passphrase to log in to the SSH server.

![SSH Login](/THM/BruteIt/images/LoginSSH.PNG)

### Privilege Escalation
After executing a few commands to gather information like id and uname -a, we identified a privilege escalation opportunity using the sudo -l command.

![sudo -l](/THM/BruteIt/images/PrivEsc.PNG)

Given that we can run /bin/cat as sudo without a password, we can consult https://gtfobins.github.io/ to find a method for privilege escalation using this command.

![GTFOBins](/THM/BruteIt/images/SudoCat.PNG)

Since this command is designed for reading files, the initial idea that came to mind was to use it to read the /etc/passwd and /etc/shadow files.

![Passwd](/THM/BruteIt/images/Passwd.PNG)

![Shadow](/THM/BruteIt/images/Shadow.PNG)

I observed that the root user has a password, so I proceeded to copy the passwd and shadow entries for the root user to my local machine.

I utilized the unshadow command on the passwd and shadow files to prepare them for password cracking with John the Ripper.

![John Root](/THM/BruteIt/images/JohnRoot.PNG)

We successfully obtained the password for the root user! 

Now it's time to use the root user's credentials to log in.

![Root](/THM/BruteIt/images/root.PNG)

Viola, we managed to get root!

## Conclusion
In this room, the primary focus is on conducting brute-force and hash cracking activities based on the information we've collected during our reconnaissance. Personally, I gained valuable experience in brute-forcing login pages with Hydra during this task, and it was a valuable learning process. This room effectively reinforced the concepts I've been studying on the TryHackMe platform. I'd like to express my gratitude to the author for creating this educational room.