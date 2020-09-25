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
  
  `cat /etc/passwd`  - Linux
  
  `net users`  - Windows
  
 ### Who’s logged in
  
  `w` - Linux 
  
  `quser`  - Windows
  
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
  
  `ps aux`   Linux/Bash
  
  `get-process`   Windows/Powershell 
  
  ### What programs are installed? 
  
  `ls -lh /bin`  Linux/Bash
  
  `ls -lh /sbin` Linux/Bash
  
  `ls C:\program files` & `ls "C:\Program Files (x86)\"`  - Windows/Powershell 
  
  `dpkg -l` -installed packages Debian flavors
  
  
