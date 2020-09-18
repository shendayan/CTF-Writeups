## Mission-Pumpkin v1.0: PumpkinGarden

The series took my eye on vulnhub, so I decided to [check them out.](https://www.vulnhub.com/?q=pumpkin) 

![Image](https://github.com/shendayan/CTF-ressources/blob/master/PumpkinGarden-Screenshot-10.png)

### Finding out the IP-Adress

This time it was very easy - the vm itself showed it on the welcome screen.

The IP I had to deal with was 192.168.178.59

### Looking for open doors

I fired up Zenmap (yeah I know, but I really like the GUI) to take a closer look at open ports.
![img](https://github.com/shendayan/CTF-ressources/blob/master/PumpkinGarden-Screenshot-19.png)

So there is nothing but a ftp-server. Let's check it out.
vsftp (very secure ftp) supports often an anonymous login. Thats what I tried first:

![img](https://github.com/shendayan/CTF-ressources/blob/master/PumpkinGarden-Screenshot-13.png)
```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
````
