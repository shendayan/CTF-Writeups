# Mission-Pumpkin v1.0: PumpkinFestival

In diesem Writeup geht es um den dritten und letzten Teil der Pumpkin-Reihe von [VulnHub](https://www.vulnhub.com/?q=pumpkin).

## Verwendete Techniken
````
- nmap
- ftp
- dirb
- wpscan
- hydra
````

![Image](https://github.com/shendayan/CTF-ressources/blob/master/Pumpkin......)

## Herausfinden der IP-Adresse

Im dritten Teil wird die IP ebenfalls direkt beim Starten der VM eingeblendet.

Jetzt heißt mein Ziel 192.168.178.61!

## Portscan

Auch diesmal beginne ich mit einem Portscan - jetzt aber mit nmap! So! :)

`nmap -A 192.168.178.61` verrät mir, dass es zwei offene Ports gibt.
````
PORT      STATE    SERVICE      VERSION
21/tcp    open     ftp          vsftpd 2.0.8 or later
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    2 0        0            4096 Jul 12  2019 secret
80/tcp    open     http         Apache httpd 2.4.7 ((Ubuntu))
|_http-generator: WordPress 4.9.3
|_http-server-header: Apache/2.4.7 (Ubuntu)
|_http-title: Pumpkin Festival &#8211; PumpkinToken : 06c3eb12ef2389e2752335...
````

Das fängt ja schonmal gut an, ein FTP-Server mit einem Verzeichnis, das "secret" heißt und ein Webserver mit irgendeinem Token und einem Hash dazu.

## FTP-Server

Für den Anfang habe ich mich für den FTP-Server entschieden. Mal schauen, was im "secret"-Verzeichnis liegt.

Hier habe ich mich über die Befehle `cd secret`, `ls` und `get token.txt` zum ersten Token navigiert.

![Image](https://github.com/shendayan/CTF-ressources/blob/master/Pumpkin......)

`PumpkinToken : 2d6dbbae84d724409606eddd9dd71265 `

## Webserver

So, jetzt wird es aber Zeit für den Webserver:

![Image](https://github.com/shendayan/CTF-ressources/blob/master/Pumpkin......)

Vielen Dank fürs Kürbisse züchten, Jack. Hilfe von Harry? Wer ist denn jetzt Harry?

Okay, PumpkinTokens helfen mir offensichtlich rein zu kommen. Das wird interessant.

Alohomora... Gehört habe ich das schon mal, aber ich weiß nicht mehr genau wo.

Ein kurzer Blick zu Google sagt mir, dass ich es wohl vor Jahren mal in einem Harry Potter Buch gelesen haben muss.

Es handelt sich dabei um einen Zauberspruch, der Türen öffnen soll. Könnte ein Passwort sein.

Dann ist auch klar, welcher Harry hier geholfen hat :)

Als ich mir die Source anschauen wollte, habe ich festgestellt, dass hier der Rechtsklick blockiert wurde.
Okay, dann eben fix ein `view-source:` vor die eigentliche Adresse getippt und es geht trotzdem.

Im Quelltext verbirgt sich noch die Aufforderung an Harry, dass er die Kürbisse finden soll.

Tricky! Das zweite Token ist in der Hintergrundfarbe auf die Seite geschrieben.

`PumpkinToken : 45d9ee7239bc6b0bb21d3f8e1c5faa52`

![Image](https://github.com/shendayan/CTF-ressources/blob/master/Pumpkin......)

## dirb

Mal sehen, ob es noch etwas interessantes zu entdecken gibt, was den Webserver angeht:

`dirb http://192.168.178.61`

Es gibt ein paar Objekte, die ich mir genauer anschauen möchte.

````
+ http://192.168.178.61/robots.txt (CODE:200|SIZE:102)
==> DIRECTORY: http://192.168.178.61/store/
==> DIRECTORY: http://192.168.178.61/users/
````

Zuerst werfe ich einen Blick auf die robots.txt:
````
User-agent: * 
Disallow: /wordpress/
Disallow: /tokens/
Disallow: /users/
Disallow: /store/track.txt
````
/wordpress/ gibt einen 404er Error.

/tokens/ gibt zumindest einen 403 - Forbidden Error.

/users/ ebenfalls

/store/track.txt kann ich mir ansehen:

````
Hey Jack!

Thanks for choosing our local store. Hope you like the services.
Tracking code : 2542 8231 6783 486

-Regards 
admin@pumpkins.local
````

Was genau ich mit dem Tracking Code anfangen kann, ist mir noch nicht klar. 

Aber erstmal notieren, wer weiß wofür das noch gut sein kann.

Der Absender sagt mir jedenfalls, dass ich pumpkins.local in meine `/etc/hosts` Datei schreiben sollte.

Eventuell ist das auch ein neuer Benutzername.

`nano /etc/hosts` und unter die Loopback-Adresse (127.0.0.1) folgendes eingetragen:

`192.168.178.61 pumpkins.local`

pumpkins.local habe ich dann im Browser eingegeben:


![Image](https://github.com/shendayan/CTF-ressources/blob/master/Pumpkin......)

Unten auf der Seite steht das dritte Token:

`PumpkinToken : 06c3eb12ef2389e2752335beccfb2080`

Noch etwas tiefer gescrollt findet sich der Hinweis, welcher den nächsten Schritt bestimmt:

## Proudly powered by WordPress

Dementsprechend lasse ich als nächstes wpscan laufen:

`wpscan -e u --url http://pumpkins.local`

Auch hier werden wieder interessante Tatsachen ans Licht gebracht:

````
[+] WordPress readme found: http://pumpkins.local/readme.html
[+] Upload directory has listing enabled: http://pumpkins.local/wp-content/uploads/
[i] User(s) Identified:
[+] admin
[+] morse
````

Okay, fangen wir oben an - Wordpress Readme:

![Image](https://github.com/shendayan/CTF-ressources/blob/master/Pumpkin......)

Das ist schonmal interessant :) 

K82v0SuvV1En350M0uxiXVRTmBrQIJQN78s ist weder base64, base32 noch eins der Hashes, welche `hash-identifier` kennt.

Das macht mich ja neugierig. Ich kann mir kaum vorstellen, dass einfach Kauderwelsch auf die Seite gesetzt wurde.

Auf [Cyber-Chef](https://gchq.github.io/CyberChef/) kann man diverse Arten von Verschlüsselungen testen.

Nach einer gefühlten Ewigkeit an manuellen Tests wurde ich dann bei base62 endlich fündig :)

`K82v0SuvV1En350M0uxiXVRTmBrQIJQN78s:morse & jack : Ug0t!TrIpyJ`

Sieht aus, als hätte ich da direkt zwei Paar Logindaten gefunden.

Jetzt schaue ich mir noch die restlichen Seiten von Wordpress an, bevor ich mich auf die Suche nach einer Möglichkeit mache, die Logindatne zu verwenden.

#6(no title) gibt einen schönen Hinweis:

![Image](https://github.com/shendayan/CTF-ressources/blob/master/Pumpkin......)

Ich brauche also alle PumpkinTokens, um das Ticket generieren zu können.

Gut zu wissen wäre jetzt, wie viele denn "alle" sind...

Die restlichen Seiten waren alle uninteressante Templates. Über die Blog-Site kommt man aber zum Login.

morse : Ug0t!TrIpyJ funktioniert!

Leider ist morse kein Admin, aber wie wir im letzten Teil der Serie gelernt haben, ist es ja auch nur der Lieferant für Kürbissamen.

Als ich die Seiten beinahe alle durchgeschaut hatte, fiel mein Blick auf das Profil. 

Unter `Biographical Info` verbirgt sich das vierte Token:

`PumpkinToken : 7139e925fd43618653e51f820bc6201b`

Weiter habe ich hier nichts gefunden, also -> Log out!

Jack : Ug0t!TrIpyJ funktioniert nicht! Dann muss ich es mit dem admin versuchen.

Aber für den habe ich leider kein Passwort...

Nach einer Weile und vielen erfolglosen Versuchen habe ich meine Notizen noch einmal durchgelesen und kam zu dem Schluss, dass "Alohomora!" jetzt der passende Zauberspruch für meine Situation wäre.

Tatsächlich klappt das!

admin : Alohomora! öffnet mir hier wirklich eine Türe.

Mal sehen, was sich jetzt noch finden lässt.

Auf jeden Fall gibt es einen Post-Entwurf, den ich mit morse schon entdeckt hatte, welchen ich mir aber nicht anschauen konnte.

Viel steht nicht drin. Muss aber auch nicht, der Inhalt reicht mir völlig:

![Image](https://github.com/shendayan/CTF-ressources/blob/master/Pumpkin......)

`PumpkinToken : f2e00edc353309b40e1aed18e18ab2c4`

Token Nummer 5!

Mehr war hier aber auch nicht zu finden. Irgendwie hing ich fest.

## Geht es nicht vorwärts, hat man vielleicht eine Abzweigung verpasst...

Um diesem Fehler aus dem Weg zu gehen, habe ich nochmal nmap angeworfen und diesmal alle Ports überprüfen lassen.
Parallel dazu lief dirbuster mit einer extrem großen Wordlist und rekursiver Suche.
Und damit ich nichts übersehe, habe ich hydra auf den FTP-Server losgelassen, um für die Benutzer jack, morse und harry nach Zugängen zu suchen:

`nmap -sC -sV -oA 192.168.178.61 -p-`

Dirbuster lief als GUI, weil dirb mit so großen Wordlists Probleme hat.

`hydra -e nsr -L users.txt -P /root/Wordlists/rockyou.txt`

Definitiv Zeit eine kleine Kaffeepause einzulegen.

Wieder am Rechner wurden mit Ergebnisse präsentiert, mit denen ich nicht gerechnet hatte:

Dirbuster hat auf dem Server http://192.168.178.61 noch eine Datei im /tokens/-Verzeichnis gefunden:

![Image](https://github.com/shendayan/CTF-ressources/blob/master/Pumpkin......)

`PumpkinToken : 2c0e11d2200e2604587c331f02a7ebea`

Token Nummer 6!

- - - To be continued - - -





















