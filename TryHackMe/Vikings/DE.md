# Vikings

![Image](/img/Vikings-Screenshot-01.png)

In diesem Writeup geht es um den Raum Vikings auf [TryHackMe](https://tryhackme.com/room/lle) von [mir :)](https://tryhackme.com/p/Shendayan).
Ich habe diesen Raum als CTF aufgebaut, welches viele verschiedene Techniken benötigt, um gelöst zu werden.


## Verwendete Techniken
````
- nmap
````

## Task 2 - Roam around

Wie bei jedem neuen CTF ist es sinnvoll einen Portscan mit nmap durchzuführen.

````
nmap -A -sV 10.10.97.203
Starting Nmap 7.80 ( https://nmap.org ) at 2020-11-24 13:44 CET
Nmap scan report for 10.10.97.203
Host is up (0.031s latency).
Not shown: 997 closed ports
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 05:a0:f7:73:1c:92:25:7f:76:ca:85:4b:e6:1e:9d:ee (RSA)
|   256 22:14:4d:87:a0:93:06:08:66:25:44:43:5a:b4:2e:ae (ECDSA)
|_  256 00:fe:b4:9a:31:bc:97:1a:91:b5:39:b4:2e:83:42:ae (ED25519)
80/tcp   open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Your arrived at a new shore!
1053/tcp open  ftp     vsftpd 2.0.8 or later
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             415 Nov 18 12:13 who-are-you.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.11.19.136
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 43.57 seconds
````
Wenn es einen Webserver gibt, schaue ich mir den immer an:

![Image](/img/Vikings-Screenshot-02.png)

Im Sourcecode der Seite versteckt sich ein interessanter Kommentar:
````
<!--WW91IGhhdmUgYXJyaXZlZCB3aXRoIHlvdXIgc2hpcCBvbiBhbiB1bmtub3duIGlzbGFuZC4gCkRvIHlvdSBzZWUgdGhhdCAvbGl0dGxlLWh1dCBpbiB0aGUgZGlzdGFuY2U/IApZb3UgbWlnaHQgZmluZCBhIGNsdWUgaW4gdGhlcmUuLi4= -->
````

Question 1:  How many open ports can you find? 

Answer: 3

Question 2:  Where can you find the next clue? 

Answer: 

Question 3:  How many open ports can you find? 

Answer: 


Ich hoffe mit diesem Walkthrough war es kein Problem mehr, diesen Raum zu bewältigen.

Danke für's Lesen und happy pwning!

Kontakt -> [Twitter](https://twitter.com/_the_someone)
