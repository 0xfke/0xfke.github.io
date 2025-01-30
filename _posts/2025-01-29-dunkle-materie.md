---
title: "TryHackMe Dunkle Matrie"
date: 2025-01-29 00:00:00 +0800
category: [Try Hack Me, Malware Analysis]
tags: [thm, writeups, Procdot, malware analysis, Windows, Threat Intel]
image: Images/Dunk-thm/Dunk-1.webp
alt: "TryHackMe Dunkle Matrie"
---

# Investigating Ransomware with ProcDOT | TryHackMe Dunkle Materie

![duk-2](Images/Dunk-thm/Dunk-2.png)


The firewall alerted the Security Operations Center that one of the machines at the Sales department, which stores all the customers' data, contacted the malicious domains over the network. When the Security Analysts looked closely, the data sent to the domains contained suspicious base64-encoded strings. The Analysts involved the Incident Response team in pulling the Process Monitor and network traffic data to determine if the host is infected. But once they got on the machine, they knew it was a ransomware attack by looking at the wallpaper and reading the ransomware note.

Can you find more evidence of compromise on the host and what ransomware was involved in the attack?
>[lINK TO ROOM:](https://tryhackme.com/r/room/dunklematerieptxc9)
--- 

## Question One

Provide the two PIDs spawned from the malicious executable. (In the order as they appear in the analysis tool)


- Spin up the machine, and it will give you a split-screen view. Then, continue with the challenge.

- Go to C:\Users\Administrator\Desktop\procdot_1_22_57_windows\win64 and run procdot as administrator

![Dunk-3](Images/Dunk-thm/Dunk-3.png)

- Then enter the file address of the .csv file from procmon which is located at C:\Users\Administrator\Desktop\Analysis Files\Logfile.csv to the field and launch the render configuration.

![Dunk-4](Images/Dunk-thm/Dunk-4.png)

- After close inspection of the Process names and googling found that the names exploreer.exe has typo and they had d/t PID so we got our first answer's for 


![Dunk-5](Images/Dunk-thm/Dunk-5.png)


![Dunk-6](Images/Dunk-thm/Dunk-6.png)


--- 

## Question Two

Provide the full path where the ransomware initially got executed? (Include the full path in your answer)

- Scroll down and you will find process name called system with PID 4 double-click on the **System** process (PID 4), then click **Refresh**, the visualization will display a graphical representation of system events.
![Dunk-7](Images/Dunk-thm/Dunk-7.png)

### What You Get from ProcDOT Visualization

When you launch **ProcDOT** and analyze the **System** process (PID 4), the visualization provides a powerful way to understand process activities and interactions.

### Key Insights from the Visualization:

1. **Process Behavior Mapping**  
   - Displays how processes interact with each other, making it easier to track suspicious activities.  

2. **Clear Dependency Graphs**  
   - Shows parent-child process relationships, helping to analyze execution flow and malware propagation.  

3. **Network Activity Insights**  
   - Highlights connections made by processes, revealing potential Command & Control (C2) communications.  

4. **File System Interactions**  
   - Identifies file modifications, deletions, or new file creations by specific processes.  

5. **Anomaly Detection**  
   - Helps in spotting unusual behaviors, such as unexpected script executions or unauthorized file access.  

This graphical approach simplifies **malware detection, security incident investigation, and system activity analysis**, allowing for quicker and more effective threat hunting.

- When you find this don't panic, if it looks complex just zoom in using *ctr + scroll*

![Dunk-8.png](Images/Dunk-thm/Dunk-8.png)

 - Zoom in and scroll until you find the exploreer.exe, then right click it and choose details Boom you have found **answer for question 2** which was --> Provide the full path where the ransomware initially got executed? (Include the full path in your answer).

![Dunk-9.png](Images/Dunk-thm/Dunk-9.png)

--- 

## Question Three

This ransomware transfers the information about the compromised system and the encryption results to two domains over HTTP POST. What are the two C2 domains? (**no space in the answer**)

- As we see here are 3 possible outgoing connections to C2 domains, looking at each Ip using online **Threat Intel**  tools like virus total we find  interesting evidences. 

![Dunk-10.png](Images/Dunk-thm/Dunk-10.png)

- Directly going to virus total and investigating we find the first malicious domain to be 

![Dunk-11.png](Images/Dunk-thm/Dunk-11.png)

- Upon close inspection there is no domain name for other two using online tools, we should use the .pcap file and search for **HTTP POST** request.


![Dunk-12.png](Images/Dunk-thm/Dunk-12.png)

- Lets resolve the network address name by going into view/name resolution/resolve network address. Then we find our next domain name.

![Dunk-13.png](Images/Dunk-thm/Dunk-13.png)

--- 
## Question Four

Provide the user-agent used to transfer the encrypted data to the C2 channel.

- We are already on that **POST** request, Lets follow the TCP stream by right clicking on the POST request and we come across a failed POST request returning 403 Forbidden message from Cisco Umbrella server and we see that the user agent that made the request is **Firefox/89.0**.

![Dunk-14](Images/Dunk-thm/Dunk-14.png)
## Question Five

Provide the cloud security service that blocked the malicious domain.
- We also know this from the previous question. Ans **Cisco Umbrella**

![Dunk-15](Images/Dunk-thm/Dunk-15.png)
## Question Six

Provide the name of the bitmap that the ransomware set up as a desktop wallpaper.

- Now lets get back to the procdot visualization and relaunch the Processes names and this time go to the 2nd exploreer.exe and double click and refresh to bring the close look visualization for the process from the exploreer.exe.

![Dunk-16](Images/Dunk-thm/Dunk-16.png)
## Question Seven

Find the PID (Process ID) of the process which attempted to change the background wallpaper on the victim's machine.

- To get this go backwards on the thread to registry key in yellow, then back on the green thread you find the PID.

![Dunk-17.png](Images/Dunk-thm/Dunk-17.png)
## Question Eight

The ransomware mounted a drive and assigned it the letter. Provide the registry key path to the mounted drive, including the drive letter.

- We are looking for a registry key path here. without hustle we find it blow our position in the same PID.

![Dunk-18.png](Images/Dunk-thm/Dunk-18.png)
## Question Nine

Now you have collected some IOCs from this investigation. Provide the name of the ransomware used in the attack. (external research required)

- Using our Indicator of Compromise and using online tools like virus total we find the name of the malware. This IOCs we have accumulated from the beginning of the challenge help us find the wright name. This Ransom is called **Blackmatter Ransomware**.

![Dunk-19.png](Images/Dunk-thm/Dunk-19.png)