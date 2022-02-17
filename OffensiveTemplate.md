# Red Team: Summary of Operations

## Table of Contents
- Exposed Services
- Critical Vulnerabilities
- Exploitation

### Exposed Services


Nmap scan results for each machine reveal the below services and OS details:

```bash
$ nmap -sP 192.168.1.1-255
```
![nmapsp.png](https://github.com/krisyslab/ThirdProject/blob/f1352bff20be756a318ad185690589a3d0503887/images/nmapsp.PNG)

```bash
$ nmap -sV 192.168.1.110
```
![nmapsv.png](https://github.com/krisyslab/ThirdProject/blob/3bf8dea1035f77c35cb41acb0eec110af16f806a/images/nmapsv.PNG)


This scan identifies the services below as potential points of entry:

| List of Ports  | Services               | 
|----------------|------------------------|
| 22/tcp         | SSH                    | 
| 80/tcp         | http                   |
| 139/tcp        | netbios-ssn Samba smdb | 
| 445/tcp        | netbios-ssn Samba smdb | 


### Vulnerabilities

The following vulnerabilities were identified:

  - Weak password implementation

      In this exercise password guessing was used to find out the password of 'michael' who is one of the users when we did the 'wpscan'. This can generally create voluminous amount of traffic when conducted remotely.
      
      The VM has poor password implementation. In this case any attacker will succeed when strong passwords are not used.

  - Weak SSH configuration

      The Target VM uses the standard port 22 for SSH.
      
      No filtering of IP addresses where the SSH connection is coming from. Therefore anyone can SSH to the VM from any IP address.


![michael.png](https://github.com/krisyslab/ThirdProject/blob/1a1ceff35d03fc7adfde5f8a2b9c83830a69e864/images/michael.PNG)      

  - Vulnerable to WordPress User Enumeration

![wpscan.png](https://github.com/krisyslab/ThirdProject/blob/0a8440f1018e4a39d9c0b69f61ee2b9b1ba97567/images/wpscan.PNG)

![wpscan2.png](https://github.com/krisyslab/ThirdProject/blob/0a8440f1018e4a39d9c0b69f61ee2b9b1ba97567/images/wpscan2.PNG)

  - No file security permission implemented


  - Executable Python root escalation privilege   


### Exploitation
 

The Red Team was able to penetrate `Target 1` and retrieve the following confidential data:
- Target 1
  - `flag1.txt`: 

![flag1.png](https://github.com/krisyslab/ThirdProject/blob/b5084ee3fa1d9fd63045ac162e5b6d52f48efad8/images/flag1.PNG)

    - **Exploit Used**
      - _TODO: Identify the exploit used_
      - _TODO: Include the command run_

  - `flag2.txt`:
      
![flag2hash.png](https://github.com/krisyslab/ThirdProject/blob/fd6a47e873f21d4f535d415f2c90ae1e43ec76ee/images/flag2hash.PNG)  
    - **Exploit Used**
      - _TODO: Identify the exploit used_
      - _TODO: Include the command run_

  - `flag3.txt` and `flag4.txt` :
      
![flag3,4.png](https://github.com/krisyslab/ThirdProject/blob/fd6a47e873f21d4f535d415f2c90ae1e43ec76ee/images/flag3and4.PNG)

![flag4.png](https://github.com/krisyslab/ThirdProject/blob/fd6a47e873f21d4f535d415f2c90ae1e43ec76ee/images/flag4theend.PNG)
    - **Exploit Used**
      - _TODO: Identify the exploit used_
      - _TODO: Include the command run_


Prepared by Kris Martinez

© 2021 Trilogy Education Services, a 2U, Inc. brand. All Rights Reserved.