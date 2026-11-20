https://tryhackme.com/room/overpass

Overpass was very fun and tested skills quite a bit and gave me experience, 1. First I started using NMAP via "nmap -sV -sC -p- -T4 10.66.172.167" to get a grasp on what services and ports were running and it returned with this: 

Nmap scan report for overpass.thm (10.66.172.167)
Host is up (0.0040s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 a7:95:4d:8a:41:75:b3:2b:9d:3e:89:e5:3a:3e:de:e2 (RSA)
|   256 df:78:d3:ce:69:77:59:db:d2:92:62:94:55:c3:e0:a1 (ECDSA)
|_  256 b2:c5:00:da:7f:c0:38:7d:2b:1a:4b:d2:36:a1:a3:84 (ED25519)
80/tcp open  http    Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Overpass
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 15.56 seconds


As on most THM / real world environments SSH is typically a deadend till you get more details, So I visited the website and it had inscriptions of a download manager and I looked at the JS and just found "main.js" which just said hello world and regular jQuery, I looked at the source code of the password manager but it seems it was local and not any HTTP connections so I loaded up gobuster and got these results:


[+] Url:                     http://10.66.172.167
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /root/Desktop/Tools/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
/aboutus              (Status: 301) [Size: 0] [--> aboutus/]
/admin                (Status: 301) [Size: 42] [--> /admin/]
/css                  (Status: 301) [Size: 0] [--> css/]
/downloads            (Status: 301) [Size: 0] [--> downloads/]
/img                  (Status: 301) [Size: 0] [--> img/]
/index.html           (Status: 301) [Size: 0] [--> ./]
Progress: 4614 / 4615 (99.98%)

The first thing I noticed after this was the admin page which had nothing particularly interesting in the HTML itself but in the JS which had "login.js", I noticed in the code that it didn't have any modifying redudancies to prevent abuse specifically this line:

Cookies.set("SessionToken",statusOrCookie)

Which caused me to set SessionToken as a regular value and then boom, regular admin page appeared which contained an SSH Private Key and a hint on to bruteforce it.

Doing that I put the SSH key into a TXT folder and used ssh2john to hash it and then used john on rockyou.txt to try to get something and I did which was "james13" after getting into the server as james.

I redeemed the user.txt file then after I looked at crontab and found an interesting request executing a .sh file as root and bash towards overpass.thm, I also tried looking at SUID bits to see if I could just escalate but nothing seemed vulnerable and then I was confused for a bit then realized through a bit of research /etc/hosts was vulnerable to editing so I hosted my own HTTP Server via Python on /downloads/src/buildscript.sh with a reverse shell on my attack server with port 4444 and then due to that I was able to get root access and the root flag :3

(first post yay :3 (believe in me not to give up))
