# Basic Host Enum

This markdown guide is a very basic handbook for enumerating a host from the outside and inside for vulnerabiltiies and mis-configurations.

##How do I use something?
  `<tool> -h --help` Linux/Bash
  `help <tool>`  Windows/Powershel
  `man <tool>` 

##What does it look like on the outside?
  `sudo nmap -sV -sC -Pn <host>  (optionally -p- or -p <specific port>)`
  ####Nmap vuln scan
  `sudo nmap -sV -Pn --script=smb-vuln* <host> --script=<proto>-vuln*` 
  
Works for common protos
http 
ftp 
ssh
etc… 

##What does it look like on the inside?

  Users on box
  `cat /etc/passwd`
  
  Who’s logged in
  `w` 
  
  What ports are open and what service is running on them?
  `netstat -tulpn`    Linux/Bash  
  `netstat -abno`     Windows/Powershell - what ports are open and what service is running on them? 
  
  What programs are running? 
  `ps aux`   Linux/Bash
  `get-process`   Windows/Powershell 
  
  What programs are installed? 
  `ls -lh /bin`  Linux/Bash
  `ls C:\program files` & `ls "C:\Program Files (x86)\"`  - Windows/Powershell 
