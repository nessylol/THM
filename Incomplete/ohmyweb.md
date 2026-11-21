https://tryhackme.com/room/ohmyweb

This one pretty much challenged me successfully as it's currently 12:43 AM and I'm pretty tired + I wasn't able to grasp some new methods that I was supposed to use, specifically after I NMAP scanned I didn't notice Apache Version being off from a default stable one so I spent my time using gobuster and looking through Assets / JS and .DS_Store files in the directory for a potential OSINT channel until I got stuck after a few minutes after realizing nothing was in the HTML so I used a hint and it lent me onto the Apache version which I noticed was off, by looking up the version there was a RCE flow on Metasploit which I used to get shell access.

I tried looking around for the user.txt but couldn't find it as unknown to me I was supposed to get root before getting user because this is an isolated docker environment which actually does replicate a real scenario pretty well so I will look out for that.

Since I'm new to Linux Privilege Escalation I was only focusing on SUID bits which nothing seemed feasible till I looked at a guide and saw I had to use getcap -r / 2>/dev/null to potentially find something with permissions and then it returned python and on GTFOBins python is pretty much instant escalation pretty much and that's how I was able to get user.txt in /root/.

However I was completely stumped after and just gave up and looked at a guide and low behold you had to look at networking and see if this was an isolated instance and see what was communicating then do a local nmap scan again to reveal 5986 to use CVE-2021-38647.

Conclusion:

It's pretty fun and challenging for a beginner in these sorts of Linux > Root like this, I'm definitely progressing and learning as I know a lot already from self using and self teaching so it's nice to learn more and TLDR to myself I need to pay more attention to outgoing networks because as soon as I've got shell access I think it's going to be easy which it is in most environments as the shell is the "most secure place" but I guess you still need to be prepared and challenged for this sort of stuff.
