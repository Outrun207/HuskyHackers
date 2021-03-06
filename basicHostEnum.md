# Basic Host Enum

Basic handbook for enumerating a host for vulnerabiltiies and mis-configurations.

## How do I use something?

  `<tool> -h --help` Linux/Bash
  
  `help <tool>`  Windows/Powershell
  
  `man <tool>` 

## What does it look like on the outside?

  `sudo nmap -sV -sC -Pn <host>  (optionally -p- or -p <specific port>)`
  
### Nmap vuln scan
  
  `sudo nmap -sV -Pn --script=smb-vuln* <host> --script=<proto>-vuln*` 
  
Works for common protos

  * http 
  * ftp 
  * ssh
  * etc… 

See all vuln scripts with `locate .nse | grep vuln`

## What does it look like on the inside?

  Users on box
  
  `cat /etc/passwd`  - Linux all users and services
  
  `cat /etc/passwd | grep home` - Linux users with a folder in /home
  
  `cat /etc/passwd | grep home | egrep -v -f <textFileOneItemPerLine>`
  
  `net users`  - Windows
  
  Whos a superuser/what group are they in? 
  
  `cat /etc/sudoers` - Linux
  
  `cat /etc/group` - Linux - check group membership
  
  Use a text editor and edit /etc/group to change group membership
  
 ### Who’s logged in
  
  `w` - Linux 
  
  `quser`  - Windows
  
 ### Delete Users
 
 `userdel <username>` - Linux 
 
 `Remove-LocalUser <username>` - Windows/Powershell
  
 ### What ports are open and what service is running on them?
  
  `sudo netstat -tulpn`    Linux/Bash 
  
  `netstat -abno`     Windows/Powershell - what ports are open and what service is running on them? 
  
  ### What does the firewall look like? 
  
  `iptables -L`
  
  #### iptables cheat sheet
  
  `sudo iptables -A INPUT -s x.x.x.x -j DROP`  - block ip address
  
  `sudo iptables -A OUTPUT -p tcp -d malware.com -j DROP`  - block outbound to remote site
  
  `sudo iptables -A INPUT -p tcp -s 10.0.0.0/8 --dport 22 -j ACCEPT`  - allow ssh from specific ip/cidr
  
  ### What programs are running? 
  
  Check the Ubuntu software manager - set updates to daily 
  
  Set Windows to check for updates daily 
  
  `ps aux`   Linux/Bash
  
  `get-process`   Windows/Powershell 
  
  ### What programs are installed? 
  
  `ls -lh /bin`  Linux/Bash
  
  `ls -lh /sbin` Linux/Bash
  
  `ls C:\program files` & `ls "C:\Program Files (x86)\"`  - Windows/Powershell 
  
  `dpkg -l` -installed packages Debian flavors
  
  ### What's running at startup? 
  
  Linux 
  
  * rc.local
  * .bashrc
  * init.d tab
  * systemd
  * crontab
  
  Windows 
  
  * startup folder
  * registry
  * services
  * scheduled tasks

## Hivestorm Checklist

* Forensics Quesiton 
* Removed Unauthorized User
* Checked Admin Group
* Changed insecure password (do it on both boxes)
* Check for daily updates
* Browser updated
* Inseucre programs updated
* Make sure right programs are available for all users
* Enable password complexity requiremets - Windows and pam on Linux)
* Enable windows firewall
* Remove root access from SSH (permit root login set to no)
* Remove "Guide to England"
