# Linux: Local Enumeration

![Image](/img/lle-Screenshot-01.png)

In diesem Writeup geht es um den Raum Linux: Local Enumeration auf [TryHackMe](https://tryhackme.com/room/lle) von [Swafox](https://tryhackme.com/p/Swafox).
Ziel des Raums ist es, Techniken und Ansätze zu vermitteln, was man nach einer erfolgreichen Reverse-Shell tun kann.

Den Inhalt des Raumes beschreibt Swafox wie folgt:
- Unit 1 - Stabilizing the shell

	Exploring a way to transform a reverse shell into a stable bash or ssh shell.
- Unit 2 - Basic enumaration

	Enumerate OS and the most common files to identify possible security flaws.
- Unit 3 - /etc

	Understand the purpose and sensitivity of files under /etc directory.
- Unit 4 - Important files

	Learn to find files, containing potentially valuable information.
- Unit 6 - Enumeration scripts

	Automate the process by running multiple community-created enumeration scripts.

## Verwendete Techniken
````
- perl
- 
````

## Task 2 - Unit 1 - tty

Eine netcat-Shell ist immer ziemlich instabil und kann durch kleine (Tipp-)Fehler schnell versehentlich beendet werden.
Dass nicht alle Befehle in einer nc-shell funktionieren (z.B. su oder sudo), ist ein weiterer Grund die Shell zu "upgraden".

Da ein einfaches ````/bin/bash```` leider in den meisten Fällen nicht funktioniert, ist es sinnvoll die gängigen Programmiersprachen durchzuprobieren.

Ich verwende den angegebenen Python-oneliner selbst sehr oft in CTF's: ````python3 -c 'import pty; pty.spawn("/bin/bash")'````

Das ist eine sehr einfache Möglichkeit, die Shell zu einer "interaktiven Shell" upzugraden.

Question 1: How would you execute /bin/bash with perl? 
Answer: ````perl -e 'exec "/bin/bash";'````

## Task 3 - Unit 1 - ssh



## Weiterführende Links

- [Static Binaries](https://github.com/andrew-d/static-binaries) 
- [Upgrading simple shells to fully interactive tty](https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys)
- 

Danke für's Lesen und happy pwning!

Kontakt -> [Twitter](https://twitter.com/_the_someone)
