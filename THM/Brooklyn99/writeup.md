# Brooklyn Nine Nine
This room is aimed for beginner level hackers but anyone can try to hack this box. There are two main intended ways to root the box.

https://tryhackme.com/room/brooklynninenine

Author: Fsociety2006

## Recon
### Nmap Scan
```
21/tcp open  ftp     syn-ack ttl 63 vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.11.45.82
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             119 May 17  2020 note_to_jake.txt
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 167f2ffe0fba98777d6d3eb62572c6a3 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDQjh/Ae6uYU+t7FWTpPoux5Pjv9zvlOLEMlU36hmSn4vD2pYTeHDbzv7ww75UaUzPtsC8kM1EPbMQn1BUCvTNkIxQ34zmw5FatZWNR8/De/u/9fXzHh4MFg74S3K3uQzZaY7XBaDgmU6W0KEmLtKQPcueUomeYkqpL78o5+NjrGO3HwqAH2ED1Zadm5YFEvA0STasLrs7i+qn1G9o4ZHhWi8SJXlIJ6f6O1ea/VqyRJZG1KgbxQFU+zYlIddXpub93zdyMEpwaSIP2P7UTwYR26WI2cqF5r4PQfjAMGkG1mMsOi6v7xCrq/5RlF9ZVJ9nwq349ngG/KTkHtcOJnvXz
|   256 2e3b61594bc429b5e858396f6fe99bee (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBItJ0sW5hVmiYQ8U3mXta5DX2zOeGJ6WTop8FCSbN1UIeV/9jhAQIiVENAW41IfiBYNj8Bm+WcSDKLaE8PipqPI=
|   256 ab162e79203c9b0a019c8c4426015804 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIP2hV8Nm+RfR/f2KZ0Ub/OcSrqfY1g4qwsz16zhXIpqk
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.29 ((Ubuntu))
| http-methods: 
|_  Supported Methods: POST OPTIONS HEAD GET
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
```

Anonymous access is permitted on the FTP server. It's time to log in and inspect for any valuable clues that can aid in advancing with the challenge.

### FTP
As you may have noticed in the Nmap scan, there is a file named "note_to_jake.txt" on the FTP server that we can access. Upon viewing the file, we were able to extract the following content:

```
From Amy,

Jake please change your password. It is too weak and holt will be mad if someone hacks into the 
nine nine
```

It appears that there are three users present on the server: Amy, Jake, and Holt.

Additionally, it appears that Jake's password is weak, as indicated by the information provided in the file.

## Exploitation
### First way to get access (FTP)
The initial thought that came to mind was to use the "rockyou.txt" file for brute-forcing the password.

Running the following command:

```
hydra -l jake -P /usr/share/wordlists/rockyou.txt $IP ssh -v
```

Resulted in successfully obtaining Jake's password.

```
[22][ssh] host: 10.10.62.96   login: jake   password: *********
```

Using the login credentials that were discovered through Hydra, we were able to gain access to the SSH server.

## Privilege Escalation
After running default commands such as "id" and "uname -a" and exploring other options to find a way to escalate privileges, I discovered a potential avenue when I executed "sudo -l".

![sudo -l](/THM/Brooklyn99/images/1.PNG)

I visited GTFOBins to check if there was a command available that could be executed with the granted permissions in order to achieve root access.

![GTFOBins](/THM/Brooklyn99/images/2.PNG)

Following the command as specified on the website.

![Root](/THM/Brooklyn99/images/3.PNG)

Boom! We successfully obtained root access.

Given that the room description mentioned two ways to access the server, I decided to challenge myself by uncovering the second method to gain access.

## Second way to gain access (Website)
### Website
When I visited the website and examined the page source, I was able to retrieve information that was commented out.

```
<!-- Have you ever heard of steganography? -->
```

Furthermore, there was an image on the main page of the website. I downloaded the image using the command "wget http://$IP/brooklyn99.jpg".

### Second way to gain access (Website)
After downloading the imange, i decided to run "steghide extract -ef brooklyn99.jpg" on it.

However, I encountered an error message during this process.

![Steghide](/THM/Brooklyn99/images/4.PNG)

It means that the jpg image is being password-protected and steghide was not able to extract the file.

At this stage, I opted to search for a method to brute-force the image and extract the file within it. I came across a tool called "stegseek" that proved effective for this purpose.

![Stegseek](/THM/Brooklyn99/images/5.PNG)

I managed to obtained a file and holt's password in the file.

```
Holts Password:
*******************

Enjoy!!
```

I managed to use the information obtained to gain access to the ssh server.

### Privilege Escalation
After running default commands such as "id" and "uname -a" and exploring other options to find a way to escalate privileges, I discovered a potential avenue when I executed "sudo -l".

![sudo -l](/THM/Brooklyn99/images/6.PNG)

I visited GTFOBins to check if there was a command available that could be executed with the granted permissions in order to achieve root access.

![GTFOBins](/THM/Brooklyn99/images/7.PNG)

Following the command as specified on the website.

![Root](/THM/Brooklyn99/images/8.PNG)

Boom! We successfully obtained root access.

## Conclusion
In this scenario, the task primarily involved a direct brute-force attack on the SSH page as the first method of gaining access. Additionally, the second method allowed me to enhance my skills in steganography and become proficient in using tools like stegseek to extract hidden information from images. This experience provided valuable knowledge and skills.