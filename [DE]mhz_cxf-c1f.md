# mhz_cxf: c1f

Das [CTF](https://www.vulnhub.com/entry/mhz_cxf-c1f,471/) hatte mich angesprochen, weil ich nicht viel Zeit hatte und die Beschreibung vermuten lies, dass man recht schnell fertig sein müsste :)

## Verwendete Techniken
````
- nmap
- dirb
- binwalk
- steghide
````

![Image](https://github.com/shendayan/CTF-ressources/blob/master/mhz_c1f-Screenshot-4.png)

### Beschreibung von Vulnhub:

A piece of cake machine

You will learn a little about enumeration/local enumeration , steganography.

This machine tested on Virtualbox , so i'm not sure about it with Vmware

If you need any help you can find me on twitter @mhz_cyber , and i will be happy to read your write-ups guy send it on twitter too

cya with another machine #mhz_cyber
This works better with VirtualBox rather than VMware 

## nmap

Der erste Schritt bei jedem CTF: Herausfinden, welche IP die Maschine besitzt und schauen, ob Ports offen sind:

````
nmap -sn 192.168.178.0/24


Nmap scan report for mhz-c1f.fritz.box (192.168.178.65)
Host is up (0.0037s latency).
MAC Address: 08:00:27:46:A2:BE (Oracle VirtualBox virtual NIC)


Portscan

nmap -A -v 192.168.178.65

PORT      STATE    SERVICE      VERSION
22/tcp    open     ssh          OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp    open     http         Apache httpd 2.4.29 ((Ubuntu))
135/tcp   filtered msrpc
139/tcp   filtered netbios-ssn
445/tcp   filtered microsoft-ds
1110/tcp  filtered nfsd-status
2869/tcp  filtered icslap
19780/tcp filtered unknown
````

Eine beliebte Kombination bei "kleinen" CTFs -> Webserver und SSH.

Im Normalfall kann man sich irgendwo auf dem Webserver dann die Login-creds für SSH heraussuchen.

Mal schauen, ob die hier nach demselben Prinzip funktioniert:

## Webserver

Die landing page ist die Standard-Seite nach einer Apache Installation. Auch die Source wurde nich verändert.

Eine /.robots.txt gibt es nicht - Zeit zu schauen, ob es etwas anderes gibt!

## dirb

Wenn es schnell gehen soll und die Wordlist nicht allzu groß ist, ist dirb das Tool der Wahl:

````
dirb http://192.168.178.65 /usr/share/wordlists/dirb/common.txt -X .txt,-html,.php

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Wed Sep 23 09:14:15 2020
URL_BASE: http://192.168.178.65/
WORDLIST_FILES: /usr/share/wordlists/dirb/common.txt
EXTENSIONS_LIST: (.txt,-html,.php) | (.txt)(-html)(.php) [NUM = 3]

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://192.168.178.65/ ----
+ http://192.168.178.65/notes.txt (CODE:200|SIZE:86)                                                             
                                                                                                                 
-----------------
END_TIME: Wed Sep 23 09:14:49 2020
DOWNLOADED: 13836 - FOUND: 1
````

Naja gut, nicht viel, sieht aber vielversprechend aus!

`http://192.168.178.65/notes.txt`

![Image](https://github.com/shendayan/CTF-ressources/blob/master/mhz_c1f-Screenshot-5.png)

`http://192.168.178.65/remb.txt`

![Image](https://github.com/shendayan/CTF-ressources/blob/master/mhz_c1f-Screenshot-6.png)

Na wunderbar, das sieht doch aus wie Login-creds :)

Die Seite `http://192.168.178.65/remb2.txt` gibt es nicht. Vielleicht liegt die Datei ja im home-Verzeichnis...

## SSH















