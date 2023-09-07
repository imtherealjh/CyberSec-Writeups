# Bounty Hacker
You talked a big game about being the most elite hacker in the solar system. Prove it and claim your right to the status of Elite Bounty Hacker!

https://tryhackme.com/room/cowboyhacker

Author:Sevuhl

## Recon
### Nmap Scan
```
sudo nmap -sC -sV -O -T4 $IP -vv -p- -oG all
```
```
PORT   STATE SERVICE REASON         VERSION
21/tcp open  ftp     syn-ack ttl 61 vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.4.28.214
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     syn-ack ttl 61 OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 dcf8dfa7a6006d18b0702ba5aaa6143e (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCgcwCtWTBLYfcPeyDkCNmq6mXb/qZExzWud7PuaWL38rUCUpDu6kvqKMLQRHX4H3vmnPE/YMkQIvmz4KUX4H/aXdw0sX5n9jrennTzkKb/zvqWNlT6zvJBWDDwjv5g9d34cMkE9fUlnn2gbczsmaK6Zo337F40ez1iwU0B39e5XOqhC37vJuqfej6c/C4o5FcYgRqktS/kdcbcm7FJ+fHH9xmUkiGIpvcJu+E4ZMtMQm4bFMTJ58bexLszN0rUn17d2K4+lHsITPVnIxdn9hSc3UomDrWWg+hWknWDcGpzXrQjCajO395PlZ0SBNDdN+B14E0m6lRY9GlyCD9hvwwB
|   256 ecc0f2d91e6f487d389ae3bb08c40cc9 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBMCu8L8U5da2RnlmmnGLtYtOy0Km3tMKLqm4dDG+CraYh7kgzgSVNdAjCOSfh3lIq9zdwajW+1q9kbbICVb07ZQ=
|   256 a41a15a5d4b1cf8f16503a7dd0d813c2 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICqmJn+c7Fx6s0k8SCxAJAoJB7pS/RRtWjkaeDftreFw

```

We've observed that the FTP server allows anonymous access, so we utilize this feature to log in to the server.

### FTP
![FTP Files](/THM/BountyHunter/images/1.PNG)

We've identified some files on the server, and we plan to use the "mget *" command to retrieve and download them to our local machine.

**task.txt**
```
1.) Protect Vicious.
2.) Plan for Red Eye pickup on the moon.

-lin
```

We've observed the presence of users named "lin" and there may also be a user named "vicious."

**locks.txt**
```
rEddrAGON
ReDdr4g0nSynd!cat3
Dr@gOn$yn9icat3
R3DDr46ONSYndIC@Te
ReddRA60N
R3dDrag0nSynd1c4te
dRa6oN5YNDiCATE
ReDDR4g0n5ynDIc4te
R3Dr4gOn2044
RedDr4gonSynd1cat3
R3dDRaG0Nsynd1c@T3
Synd1c4teDr@g0n
reddRAg0N
REddRaG0N5yNdIc47e
Dra6oN$yndIC@t3
4L1mi6H71StHeB357
rEDdragOn$ynd1c473
DrAgoN5ynD1cATE
ReDdrag0n$ynd1cate
Dr@gOn$yND1C4Te
RedDr@gonSyn9ic47e
REd$yNdIc47e
dr@goN5YNd1c@73
rEDdrAGOnSyNDiCat3
r3ddr@g0N
ReDSynd1ca7e
```

Given the file naming convention, it's possible that the "locks.txt" file might contain the password required for SSH server login.

## Exploitation
At this stage, I've decided to attempt a brute force attack on the SSH server using the "lin" user and the "locks.txt" file.

![Hail Hydra](/THM/BountyHunter/images/2.PNG)

We have successfully obtained the password for the "lin" user on the SSH server.

We will now utilize the credentials we've acquired to access the SSH server.

![SSH Login](/THM/BountyHunter/images/3.PNG)

## Privilege Escalation
Having gained access and obtained the user flag, our next step is to conduct enumeration to identify potential opportunities for privilege escalation to root.

After running several commands, I've discovered that the user "lin" has the capability to execute "/bin/tar."

![sudo -l](/THM/BountyHunter/images/4.PNG)

We will proceed by checking GTFOBins to determine if there are any exploits or methods available for executing actions with the "tar" command.

![GTFOBins](/THM/BountyHunter/images/5.PNG)

![Root](/THM/BountyHunter/images/6.PNG)

Viola, we managed to get root!

## Conclusion
This room focuses on basic enumeration and leveraging the information gathered to gain access to the server. It serves as a valuable reinforcement of the concepts I've been studying on the TryHackMe platform.