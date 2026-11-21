# tryhackme — cowboy hacker writeup :3

**room:** [cowboy hacker](https://tryhackme.com/room/cowboyhacker)  
**difficulty:** easy  

## overview

cowboy hacker was pretty easy, and i only really messed up at the end because, due to my initial `sudo` attempts, i thought i didn't have access to any general privilege escalation methods.

i should've solved this way faster, but my brain had a little oopsie moment.. :3

## 1. initial enumeration — nmap

so i first started off with an nmap scan to see what services and ports were running:

```bash
nmap -sV -sC -p- -T4 10.65.179.233
```

and i got this response:

```text
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-11-21 21:12 UTC
Nmap scan report for ip-10-65-179-233.ec2.internal (10.65.179.233)
Host is up (0.00022s latency).
Not shown: 55529 filtered tcp ports (no-response), 10003 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.5
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: PASV failed: 550 Permission denied.
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to ::ffff:10.65.66.115
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.5 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 ec:ab:69:51:71:6d:5a:f1:d6:0d:78:2d:54:58:ff:dd (RSA)
|   256 d4:f6:be:11:23:b6:7f:7e:7c:12:4f:70:2b:f1:bd:3c (ECDSA)
|_  256 34:99:89:e5:bc:fb:a8:77:78:d5:4d:fe:82:2e:54:10 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

what immediately caught my attention was **anonymous ftp login allowed**, so that's where i decided to start!

## 2. anonymous ftp — finding the credentials

i logged into ftp anonymously and found two files: `task.txt` and `lock.txt`.

`task.txt` contained:

```text
1.) Protect Vicious.
2.) Plan for Red Eye pickup on the moon.

-lin
```

and `lock.txt` contained various possible passwords.

since `task.txt` was signed by **lin**, i figured that might be a username i could use with ssh.

## 3. checking the website — gobuster

before trying that, i quickly used gobuster against the web server just in case there was something interesting hiding there.

it only turned up a pretty standard `images` directory, though, so i moved back to the ftp findings.

## 4. ssh — getting the user flag

i used the username `lin` from `task.txt` along with the password list from `lock.txt` to bruteforce ssh on the machine.

i ended up finding a working password using hydra:

```text
username: lin
password: RedDr4gonSynd1cat3
```

with that, i logged in through ssh and got the **user flag**! :3

## 5. privilege escalation — the oopsie part

after getting the user flag, i started checking for potential privilege escalation vulnerabilities.

these were the commands i used:

```bash
find / -type f -perm -4000 2>/dev/null
getcap -r / 2>/dev/null
```

these check for **suid binaries** and **linux file capabilities**.

i then spent around 10 minutes just looking for extra hints in random `.txt` files because some previous challenges did that. i even found a firefox instance on the machine and tried checking its browser history, lol.

i also checked [gtfobins](https://gtfobins.github.io/) and looked into `tar` and `snap`. `tar` seemed like one of the more promising options, but my earlier `sudo` attempts made me assume it wouldn't work. i got a bit stuck thinking i didn't have any useful escalation methods.

but then i finally tried the **gtfobins `sudo` method for `tar`**, and it worked! i managed to escalate to root, then went straight for the root flag:

```bash
cd /root/
cat root.txt
```

and that was it, room completed! :3

## final thoughts

cowboy hacker was a pretty fun and easy room overall. the ftp and ssh parts were straightforward, but i definitely overthought the privilege escalation part.

my biggest mistake was assuming that because my first few `sudo` attempts didn't work, i couldn't use `sudo` for anything useful. i should've checked the specific permissions and gtfoBins methods more carefully instead of running around looking for random hints.

**i should've solved this way faster but brain had an oopsie moment..**
(this message was edited by gpt cause I was lazy to type cause I don't save a lot of my progression in real time but rest assured I don't use AI for CTF like this and only my own knowledge HOWEVER for aids stuff like comparing GTFOBin Escalations and for some commands to for example find TXTS in common directories since I'm too lazy to type I do use AI but that's it)
