# Vikings

![Image](/img/Vikings-Screenshot-01.png)

In diesem Writeup geht es um den Raum Vikings auf [TryHackMe](https://tryhackme.com/room/lle) von ... [mir](https://tryhackme.com/p/Shendayan) :)

Ich habe diesen Raum als CTF aufgebaut, welches viele verschiedene Techniken benötigt, um gelöst zu werden.

## Verwendete Techniken
````
- nmap
- steghide
- ftp
- reverse image search
- hydra
- decryption with cyberchef
- zip2john / john
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
<!-- WW91IGhhdmUgYXJyaXZlZCB3aXRoIHlvdXIgc2hpcCBvbiBhbiB1bmtub3duIGlzbGFuZC4gCkRvIHlvdSBzZWUgdGhhdCAvbGl0dGxlLWh1dCBpbiB0aGUgZGlzdGFuY2U/IApZb3UgbWlnaHQgZmluZCBhIGNsdWUgaW4gdGhlcmUuLi4= -->
````
````
❯ echo $comment | base64 -d
You have arrived with your ship on an unknown island. 
Do you see that /little-hut in the distance? 
You might find a clue in there...
````

/little-hut ist ein Hinweis auf ein Verzeichnis des Servers.

![Image](/img/Vikings-Screenshot-03.png)

Wirklich einladend sieht es nicht aus. Aber der base64-Text sagte ja: "You might find a clue in there"

Heißt also, dass IN der Hütte der nächste Hinweis ist.

````
❯ steghide --extract -sf little-hut.jpg
Passwort eingeben: 
Extrahierte Daten wurden nach "runestone.txt" geschrieben.
❯ cat runestone.txt
Hello Stranger.

I hope you come with peaceful intent. 
Oh, you're looking for the key to Valhalla. 
Many others have tried that before. 
The only way I know to get there is to die honorably on the battlefield. 
The Valkyries will then show you the way.

An old friend of mine once told me about a wanderer who is said to have spoken to the Allfather.
If there is any way to travel to the golden hall without the Valkyries help, maybe this wanderer knows about it.
You can find him in a port nearby. His name is Bjorn. 
When you meet him, tell him I sent you. He owes me a favor.
So that he can be sure you are telling the truth, give him this:

*The old man hands you a rune-stone-carved-from-wood.*
````


#### T2Q1: How many open ports can you find? 

Answer: 3
#### T2Q2: Where can you find the next clue? 

Answer: /little-hut

#### T2Q3:  What name did the old man mention? 

Answer: Bjorn


## Task 3 - Find his old friend

Okay, wir sollen Bjorn also "in a port nearby" suchen.
Da muss ich spontan an den FTP-Server denken.

![Image](/img/Vikings-Screenshot-04.png)

Wem muss ich denn hier Rede und Antwort stehen?

![Image](/img/Vikings-Screenshot-05.png)

Da ich ja aber weiß, wen ich suche - bjorn - lasse ich mich davon nicht abschrecken und suche weiter!

````
ftp> ls -la
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drw-r-xr-x    3 0        0            4096 Nov 18 12:13 .
drw-r-xr-x    3 0        0            4096 Nov 18 12:13 ..
drwxr-xr-x    2 0        0            4096 Nov 19 14:14 .hidden
-rw-r--r--    1 0        0             415 Nov 18 12:13 who-are-you.txt
226 Directory send OK.
ftp> cd .hidden
250-You mingle with the crowd and walk through the village.
250-You don't really know what you're looking for.
250-Suddenly you see a familiar face.
250 Directory successfully changed.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 0        0             272 Nov 18 12:13 what-are-you-searching-for.txt
226 Directory send OK.
ftp> get what-are-you-searching-for.txt
local: what-are-you-searching-for.txt remote: what-are-you-searching-for.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for what-are-you-searching-for.txt (272 bytes).
226 Transfer complete.
272 bytes received in 0.00 secs (2.4243 MB/s)
````
![Image](/img/Vikings-Screenshot-06.png)

Okay, das sieht nach einer Sackgasse aus!

Aber der alte Mann sagte ja auch, dass ich bjorn etwas geben soll, damit er weiß von wem ich komme.
Hört sich nach einer Art Passwort an. 

bjorn:rune-stone-carved-from-wood

![Image](/img/Vikings-Screenshot-07.png)

Was fange ich jetzt mit den beiden Dateien an? Das eine ist ein Bild und das andere scheint eine ausführbare Datei zu sein.

Mit hexedit in die binary geschaut, fällt mir auf, dass es sich hierbei um eine Kopie der "strings" binary handelt.
Bjorns Tagelharpa ist ja kaputt gegangen, also bin ich nett und gebe ihm eine neue Saite für seine Harfe:

````strings Tagelharpa```` zeigt einen interessanten Abschnitt in der Mitte der Anzeige:

![Image](/img/Vikings-Screenshot-08.png)

Wenn Bjorn sich nicht erinnert, muss ich eben nach dem Namen des Ortes suchen. So ganz ohne Anhaltspunkt bin ich ja nicht.

![Image](/img/Vikings-Screenshot-09.png)

Wenn man das Bild aus dem Task bei yandex hochlädt, bekommt man als ersten Treffer angezeigt, wo dieses Wikingerdorf steht.

![Image](/img/Vikings-Screenshot-10.png)

In [Gudvangen - Norwegen](https://gudvangenbudgethotel.com/about/).

````
❯ steghide --extract -sf vegvisir.jpg
Passwort eingeben: gudvangen
Extrahierte Daten wurden nach "wanderer.txt" geschrieben.
❯ cat wanderer.txt
wanderer:notallwhowanderarelost
````

Question 1:  What instrument was Bjorn playing? 

Answer: Tagelharpa

Question 2:  Where did he sent you? 

Answer: Gudvangen

Question 3:  What secret is Vegvisir exposing to you? 

Answer: wanderer.txt


## Task 4 - The Wanderer

Mit den soeben erhaltenen Daten gibt es endlich Zugriff auf die Maschine!

![Image](/img/Vikings-Screenshot-11.png)

Die .bash_history ist nicht gerade hilfreich:
````
wanderer@midgard:~$ cat .bash_history 
This thing is beyond your understanding, my child. 
Think no further on the matter and maybe you will read the riddle in the end. Who knows? 
Meanwhile the air is fresh and the day golden and my palace is near at hand. 
The young should enjoy themselves while they may, so come!
````

Mit ````sudo -l```` lässt sich überprüfen, ob wanderer etwas mit root-Rechten ausführen darf - Darf er!

![Image](/img/Vikings-Screenshot-12.png)

Das war eine kurze Vorstellung. Zum Glück kann man sich mit den Creds nochmal anmelden. 

Sonst wäre es etwas schwierig herauszufinden, dass es noch einen weiteren User gibt - berserker.

![Image](/img/Vikings-Screenshot-13.png)

Question 1:  What did the wanderer do?

Answer: /etc/landscape/disappear.sh

Question 2:  What did he tell you to fight with? 

Answer: brute force

## Task 5 - The Berserker

Wenn ich gegen einen Berserker mit "brute force" kämpfen soll, lasse ich das am besten die Hydra machen.
Die ist stärker, als ich :)

![Image](/img/Vikings-Screenshot-14.png)

Im home-dir sind zwei bash-scripte, die ich nicht lesen kann. ````sudo -l```` hilft mir auch hier weiter:

![Image](/img/Vikings-Screenshot-15.png)

Da die Box ja sehr realitätsnah aufgebaut ist, verhalte ich mich auch ganz real - NICHT!
````
berserker@midgard:~$ sudo ./flee.sh 
If you run away from a fight, you will just die tired!
This is definitly not the way to Valhalla!
Connection to 10.10.97.203 closed by remote host.
Connection to 10.10.97.203 closed.
````
Tja, wenn ich nur die Wahl zwischen Kampf oder Flucht habe (und Flucht nicht funktioniert), bleibt mir wohl nichts anderes übrig, als zu kämpfen:

![Image](/img/Vikings-Screenshot-16.png)

Den Kampf lasse ich hier mal aus - ich verliere irgendwie immer...

![Image](/img/Vikings-Screenshot-17.png)

iwillguideyoutothegreathall flüstert die Walküre, ja? Liest sich nicht, wie eine normale Aussage. Eher wie ein Passwort... Aber für wen?

````
berserker@midgard:~$ cd .. && ls
berserker  bjorn  valkyrie  wanderer
````
Ein neuer User...

Question 1:  What sound does the berserker make?

Answer: hahaha

Question 2:  Can you convince him not to fight? (Yay/Nay)

Answer: Nay

Question 3:  Who appeared after the fight? 

Answer: valkyrie

## Task 6 - The Valkyrie

Mit ````su valkyrie```` und dem Passwort iwillguideyoutothegreathall wechsle ich auf den neuen User.

Im home-dir von valkyrie liegt ein script, welches ich nicht ausführen kann (wieder mal...).
````
valkyrie@midgard:~$ ll
total 24
drwxr-xr-x 2 valkyrie valkyrie 4096 Nov 24 19:29 ./
drwxr-xr-x 6 root     root     4096 Nov 24 19:29 ../
-rw-r--r-- 1 valkyrie valkyrie  220 Apr  4  2018 .bash_logout
-rw-r--r-- 1 valkyrie valkyrie 3771 Apr  4  2018 .bashrc
-r-x------ 1 root     root     1382 Nov 24 19:29 gullintanni*
-rw-r--r-- 1 valkyrie valkyrie  807 Apr  4  2018 .profile
valkyrie@midgard:/home$ sudo -l
[sudo] password for valkyrie: 
Matching Defaults entries for valkyrie on midgard:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User valkyrie may run the following commands on midgard:
    (root : root) /home/valkyrie/gullintanni
```` 
Bevor ich das Script ausführe, möchte ich aber gerne Frage 1 beantworten. Für wen ist Gullintanni ein anderer Name?
Eine kurze Google-Suche liefert hier das gewünschte Ergebnos:

![Image](/img/Vikings-Screenshot-18.png)

Okay, dann mal schauen, was das Script so macht:
````
valkyrie@midgard:~$ sudo ./gullintanni
On your ride to Valhalla the colors suddenly become more colorful again...
Whats happening here..?
It looks like you didn't die on the battlefield after all.
Unfortunately, the Valkyrie noticed that too!

She moans softly:
I'm only allowed to bring fallen warriors to Valhalla!
But I don't have time to take you back to Midgard.
If I leave you here, you will be forever trapped between worlds.
Your only chance is to sneak past Heimdallr and head back to midgard via the Bifrost.
He's guarding the Bifrost very carefully, but today he'll be distracted.
A big feast is being celebrated and as far as I know him, he'll leave his post to get new mead,
everytime his drinking horn is empty. While he's not watching, you can search for a part of his key.
The key to Himinbjörg consists of four rune stones. Put them together in the correct order and enter Asgard.
````

Question 1:   Who is Gullintanni another name for?

Answer: hahaha

Question 2:  How many rune stones is the key made of?

Answer: Nay


## Task 7 - The Guardian

Plötzlich bekomme ich eine Broadcast message von root:

![Image](/img/Vikings-Screenshot-19.png)

Ja Prost! Sieht so aus, als hätte da jemand etwas zu trinken.
````cd .. && ll```` zeigen, dass es schon wieder einen neuen User und eine neue Datei in /home gibt.

heimdallr wurde hinzugefügt und HeimdallrsDrinkingHorn liegt in /home.

````
valkyrie@midgard:/home$ cat HeimdallrsDrinkingHorn 
full
````
Die Walküre hat vorhin noch gesagt: [...] as far as I know him, he'll leave his post to get new mead,
everytime his drinking horn is empty. While he's not watching, you can search for a part of his key. [...]

Jetzt kam ein Broadcast: "This horn must have a hole..." Ja, das kenne ich. Meine Mate-Flaschen haben auch immer ein Loch :)

````
valkyrie@midgard:/home$ cat HeimdallrsDrinkingHorn 
empty
````

Aha, jetzt kann ich also nach den vier Teilen des Schlüssels suchen. 

Teil 1 liegt in /home/valkyrie. Dort ist plötzlich ein Heuhaufen entstanden, in welchem eine kleine Box versteckt ist:

![Image](/img/Vikings-Screenshot-20.png)

Da es kein Base64 ist, habe ich es bei [Cyberchef](http://icyberchef.com/) eingegeben und siehe da: 

From Base62, From Hex -> youshallnot

Da bin ich ja mal gespannt, was ich nicht soll...

Teil 2 finde ich in /etc/ hinter einem .loose_board/ in einem .sachet/:

![Image](/img/Vikings-Screenshot-21.png)

Cyberchef verrät mir auch hier die Lösung:

From Decimal, From Hex -> passthebifrost

Alles klar, bis jetzt habe ich "youshallnotpassthebifrost".. Sehe ich ein, dass er das nicht möchte. Ich lebe ja und habe dementsprechend in Walhalla nichts verloren ;)

Teil 3 fine ich in /var/backups/ in einem .hollow_stone/.

![Image](/img/Vikings-Screenshot-22.png)

Interessant, dass sich heimdallr auch fragt, wie er die Kiste auf bekommt.

Denn man benötigt ein Passwort, um die Datei zu entpacken:
````
valkyrie@midgard:~$ unzip locked-chest.zip 
Archive:  locked-chest.zip
[locked-chest.zip] root/runestone.txt password:
````
Okay, ZIP-Passwörter sind ja einfach zu cracken, solange sie sich in einer Wordlist befinden.
Zuerst die Datei auf die eigene Maschine kopieren mit scp, dann mit zip2john das Passworthash auslesen und anschließend mit john cracken:

![Image](/img/Vikings-Screenshot-23.png)

Entpacken werde ich das Archiv wieder auf der VM:

![Image](/img/Vikings-Screenshot-24.png)

Sieht stark nach Morsecode aus. Den kann ich leider nicht - Cyberchef schon :)

From Morse Code, From Hex -> aslongas

Okay, youshallnotpassthebifrostaslongas ... so lange wie was?

Teil 4 finde ich in / unter einem .stack_of_blankets/ in einem .bag/.

![Image](/img/Vikings-Screenshot-25.png)

Bäh, Binärcode... Hatte ich zwar im Studium, aber auch hier ist Cyberchef deutlich schneller, als ich es wäre:

From Binary, From Hex -> iamthekeeper

Aha! youshallnotpassthebifrostaslongasiamthekeeper

Sieht für mich nach einem so gut wie uncrackbaren Password aus :) 

Während der Suche nach den Teilen, habe ich verschiedene Broadcasts bekommen. Einer ist base64 verschlüsselt und der andere ist irgendwie verdreht. ROTiert vielleicht? :)

Question 1:    What is the first part hidden in?

Answer: .little_box/

Question 2:  What is the second part hidden in?

Answer: .sachet/

Question 3:   What is the third part hidden in?

Answer: locked-chest.zip

Question 4:  What is the magic word to open the chest?

Answer: rainbow

Question 5:   What is the fourth part hidden in?

Answer: .bag/

## Task 8 - Valhalla

````su heimdallr```` und das ellenlange Passwort eingegeben. Passt!

Im home-dir befindet sich eine Notiz:

![Image](/img/Vikings-Screenshot-26.png)

Kurz mal ````sudo -l```` gechecked und siehe da, heimdallr kann /bin/enter_valhalla als root ausführen!

In der Datei steht nur ein einziger Befehl, doch der ist der Jackpot: ````chmod 666 /etc/passwd````

World-writeable :) Also schnell ausführen und einen neuen root-User eintragen.

![Image](/img/Vikings-Screenshot-27.png)

````
# cat /root/valhalla.txt
THM{Y0u_tr4v3l3d_t0_v4lh4ll4}
````

Question 1: What's the content of /root/valhalla.txt? 

Answer: THM{Y0u_tr4v3l3d_t0_v4lh4ll4}

Ich hoffe mit diesem Walkthrough war es kein Problem mehr, diesen Raum zu bewältigen.

Danke für's Lesen und happy pwning!

Kontakt -> [Twitter](https://twitter.com/_the_someone)
