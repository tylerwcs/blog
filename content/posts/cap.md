---
title: "Cap [HTB Walkthrough]"
date: 2022-04-27T19:08:29+08:00
draft: false
tags: ["HTB", "Easy"]
---

---

### Knowledge Gained 🙉
- GTFObins
- wireshark
- capabilities


---

## Enumeration

First we did a `rustscan` + `nmap` scan, which found 3 ports, **SSH**, **FTP**, and **HTTP**.

```
rustscan -a 10.10.10.245
```

![](../../img/cap1.png)

Next I run `gobuster` to find sub directories for the website. Nothing much is interesting.

```
gobuster dir -u http://10.10.10.245 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 100

```

![](../../img/cap2.png)

---

## Foothold

When I browse around the website, I noticed that the only one interesting page is the `data` page. It seems to include a **pcap** file that it capture.

![](../../img/cap3.png)

I then noticed that the url shows a **ID** number at the end, so I played with it a bit and found out that if I change the ID num to **0**, a very interesting capture shows up.

![](../../img/cap4.png)

I then downloaded the file and open in up with `wireshark` and found some creds inside!

![](../../img/cap5.png)

I then use the creds to connect to `SSH` and it worked!

![](../../img/cap6.png)

---

## Privilege Escalation

After I took the user flag, I then move on to priv esc. I used `Linpeas` to enumerate the machine, and found one interesting point. It appears the `/usr/bin/python3.8` binary has the `cap_setuid` capability enabled.

![](../../img/cap7.png)

So I went to [GTFObins](https://gtfobins.github.io/gtfobins/python/) to find some more info and upon consulting GTFOBins, it appears this can be exploited, as it practically works in the same way as SETUID:

![](../../img/cap9.png)

Running the command mentioned above and we gain root-level privileges.

![](../../img/cap8.png)

Thats all for this box, thanks for reading!
