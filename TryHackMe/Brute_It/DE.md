# Brute It

![Image](/img/Brute-It-Screenshot-01.png)

In diesem Writeup geht es um den Raum Brute It auf [TryHackMe](https://tryhackme.com/room/lle) von [ReddyyZ](https://tryhackme.com/p/ReddyyZ).
Ziel des Raums ist es, etwas über brute-forcing, hash cracking und privilege escalation zu lernen.

## Verwendete Techniken
````
- nmap
- gobuster
- hydra
- 
````

## Task 2 - Reconnaissance

Um die Fragen zu beantworten nutze ich folgende Befehle:

````
❯ nmap -A -p- 10.10.201.149
Starting Nmap 7.80 ( https://nmap.org ) at 2020-11-23 11:42 CET
Nmap scan report for 10.10.201.149
Host is up (0.037s latency).
Not shown: 65533 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4b:0e:bf:14:fa:54:b3:5c:44:15:ed:b2:5d:a0:ac:8f (RSA)
|   256 d0:3a:81:55:13:5e:87:0c:e8:52:1e:cf:44:e0:3a:54 (ECDSA)
|_  256 da:ce:79:e0:45:eb:17:25:ef:62:ac:98:f0:cf:bb:04 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 19.70 seconds
````
Hier gibt es direkt auf die ersten vier Fragen eine Antwort.
Für die fünfte Frage, ist ein directory-scan mit dirb, dirbuster oder gobuster nötig.
Ich habe gobuster gewählt und lasse über folgenden Befehl scannen:

````
❯ gobuster dir -u 10.10.201.149 -w /usr/share/wordlists/dirb/common.txt -t 100
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.201.149
[+] Threads:        100
[+] Wordlist:       /usr/share/wordlists/dirb/common.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2020/11/23 11:57:38 Starting gobuster
===============================================================
/admin (Status: 301)
/.hta (Status: 403)
/.htpasswd (Status: 403)
/.htaccess (Status: 403)
/index.html (Status: 200)
/server-status (Status: 403)
===============================================================
2020/11/23 11:57:45 Finished
===============================================================
````

Question 1: Search for open ports using nmap. How many ports are open?

Answer: 2

Question 2: What version of SSH is running?

Answer: OpenSSH 7.6p1

Question 3: What version of Apache is running?

Answer: 2.4.29

Question 4: Which Linux distribution is running?

Answer: Ubuntu

Question 5: Search for hidden directories on web server. What is the hidden directory?

Answer: /admin

## Task 3 - Getting a shell

Um eine SSH-Verbindung zu bekommen, macht es (gerade in einem Raum, der Brute It heißt) Sinn, einen Brute-Force-Angriff laufen zu lassen.
Da das versteckte Verzeichnis auf dem Webserver /admin heißt, nutze ich hier admin als Username.
Der Hinweis im Source-Code bestätigt meine Vermutung:
![Image](/img/Brute-It-Screenshot-03.png)
Die richtige Syntax für den hydra Befehl ziehe ich mir aus der Netzwerkanalyse meines Browsers:

![Image](/img/Brute-It-Screenshot-02.png)

````

````

Wenn ich an CTFs arbeite, bei welchen ich einen Usernamen habe, lasse ich eigentlich immer, quasi nebenher, ein Terminal mit hydra gegen SSH und/oder FTP laufen, um nach einer funktionierenden Kombination aus Unsername und Passwort zu suchen.
Als wordlist nutze ich hier meistens rockyou.txt, da diese seit einer gefühlten Ewigkeit mit Kali-Linux mitgeliefert wird und bei CTFs eigentlich der Standard gilt: Wenn du es mit rockyou nicht innerhalb von 5 Minuten cracken kannst, ist es nicht dafür gedacht gebruteforced zu werden.


Question 1: What is the user:password of the admin panel?

Answer: admin:xavier

Question 2: Crack the RSA key you found. What is John's RSA Private Key passphrase?

Answer: rockinroll

Question 3: user.txt

Answer: THM{a_password_is_not_a_barrier}

Question 4: Web flag

Answer: THM{brut3_f0rce_is_e4sy}
