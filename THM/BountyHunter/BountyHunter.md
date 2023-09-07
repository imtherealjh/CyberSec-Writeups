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

We've observed the presence of users named "lin," and there may also be a user named "vicious."

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