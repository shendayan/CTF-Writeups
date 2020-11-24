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
<!--WW91IGhhdmUgYXJyaXZlZCB3aXRoIHlvdXIgc2hpcCBvbiBhbiB1bmtub3duIGlzbGFuZC4gCkRvIHlvdSBzZWUgdGhhdCAvbGl0dGxlLWh1dCBpbiB0aGUgZGlzdGFuY2U/IApZb3UgbWlnaHQgZmluZCBhIGNsdWUgaW4gdGhlcmUuLi4= -->````

Question 1:  How many open ports can you find? 

Answer: 3

Question 2:  Where can you find the next clue? 

Answer: 

Question 3:  How many open ports can you find? 

Answer: 



## Task 3 - Unit 1 - ssh

Die beste Shell bekommt man natürlich über SSH. Hier braucht man aber entweder eine Username:Password-Kombination, einen Eintrag in der "authorized keys"-Datei, oder den privaten SSH-Key eines Users.
Den privaten Schlüssel findet man in Linux-Systemen meistens im Verzeichnis ````/home/$USER/.ssh/````.
Sobald man sich diese Datei auf seinen Rechner kopiert hat, kann man mit folgendem Befehl auch ohne ein Passwort eine SSH-Verbindung aufbauen:
````ssh user@ip -i keyfile````

Question 1: Where can you usually find the id_rsa file? (User = user) 

Answer: /home/user/.ssh/id_rsa

Question 2: Is there an id_rsa file on the box? (yay/nay)

Answer: nay

## Task 4 - Unit 2 - Basic enumeration

Bei jeder Maschine, in die man (auf welche Art auch immer) hereingekommen ist, ist es sinnvoll sich erst einmal etwas umzuschauen, um festzustellen womit man es eigentlich zu tun hat.

Der erste Befehl hierzu wäre ````uname -a````. 
Die Ausgabe sieht auf meinem System so aus:

````Linux PT 5.4.0-54-generic #60-Ubuntu SMP Fri Nov 6 10:37:59 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux````

Sie setzt sich wie folgt zusammen:
- Name des Betriebssystems (Linux)
- Hostname (PT)
- Kernel Release (5.4.0-54-generic)
- Kernel Version (#60-Ubuntu SMP mit Datum der Installation)
- Architektur der Maschine (x86_64)
- Architektur des Prozessors (x86_64)
- Architektur der Hardware-Plattform (x86_64)
- OS (GNU/Linux)

Danach sollte man sich anschauen, wie es mit den Bash-Dateien aussieht. Auch hier kann man nützliche Informationen herausziehen.

````.bash_profile````, ````.bashrc```` und ````.bash_history```` sind hier die Objekte der Wahl.

Question 1: How would you print machine hardware name only?

Answer: uname -m

Question 2: Where can you find bash history?

Answer: ~/.bash_history

Question 3: What's the flag?

Answer: thm{clear_the_history}

## Task 5 - Unit 3 - /etc

Im Verzeichnis /etc liegen quasi die Kronjuwelen einer Linux-Maschine. Primär geht es hier um die beiden Dateien ````/etc/passwd```` und ````/etc/shadow````.

Bei einer normalen Installation von Linux, ist die passwd-Datei für alle Nutzer des Systems lesbar. Auf shadow trifft das allerdings nicht zu, da sich in dieser Datei die gehashten Passwörter aller Nutzer befinden.

Question 1: Can you read /etc/passwd on the box? (yay/nay)

Answer: yay

## Task 6 - Unit 4 - Find command and interesting files

Interessante Dateien muss man auf einem System oft suchen. Unter Linux wird nicht gesucht, sondern gefunden - mit dem find-Befehl.

Die Fragen dieses Tasks werden mit folgenden Befehlen beantwortet:
````find / -type f -name "*.bak" 2>/dev/null```` und ````find / -type f -name "*.conf" 2>/dev/null````.

Question 1: What's the password you found? 

Answer: THMSkidyPass

Question 2: Did you find a flag?

Answer: thm{conf_file}

## Task 7 - Unit 4 - SUID

Dateien, welche ein gesetztes SUID-Bit haben, lassen sich mit den Rechten des Dateibesitzers ausführen. Für ein CTF sind dementsprechend natürlich die Dateien interessant, die root gehören und das SUID-Bit gesetzt haben um mit root-Rechten eine Datei auszuführen.

Auch diese Dateien lassen sich mit dem find-Befehl anzeigen: ````find / -perm -u=s -type f 2>/dev/null```` 
-u=s kann auch ersetzt werden durch "/4000", es hat die selben Auswirkungen auf die Suche.

Question 1: Which SUID binary has a way to escalate your privileges on the box? 

Answer: grep

Question 2:  What's the payload you can use to read /etc/shadow with this SUID?

Answer: grep '' /etc/shadow

## Weiterführende Links

- [Static Binaries](https://github.com/andrew-d/static-binaries) 
- [Upgrading simple shells to fully interactive tty](https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys)
- [Most common Linux file extensions](https://lauraliparulo.altervista.org/most-common-linux-file-extensions/)
- [GTFO Bins mit SUID Filter](https://gtfobins.github.io/#+SUID)
- [LinPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS)
- [LinEnum](https://github.com/rebootuser/LinEnum)

Ich hoffe mit diesem Walkthrough war es kein Problem mehr, diesen Raum zu bewältigen.

Danke für's Lesen und happy pwning!

Kontakt -> [Twitter](https://twitter.com/_the_someone)
