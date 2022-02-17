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

        In this exercise password guessing was used to find out the password of 'michael' who is one of the users when we did the 'wpscan'. 
        This can generally create voluminous amount of traffic when conducted remotely.
        The VM has poor password implementation. In this case any attacker will succeed when strong passwords are not used.

  - Weak SSH configuration

        The Target VM uses the standard port 22 for SSH.      
        No filtering of IP addresses where the SSH connection is coming from. Therefore anyone can SSH to the VM from any IP address.

  - Vulnerable to WordPress User Enumeration

        User enumeration refers to a technique that allow attackers to get the usernames that are registered on the Target website.
        Attackers can use the usernames to attempt a brute force attack against the login page until they gain access to the system.

  - No file security permission implemented

        Sensitive files should have a proper read, write and execute permissions to maintain their confidentiality. 
        With this exercise we were able to explore and open folders and files from with using a regular user account. 

  - Executable Python root escalation privilege 

        Using a python command we were able to elevate a regular user privilege to a root user. 
        This is very dangerous for any system as anyone who can get into the system can own it and have the freedom to do whatever they wish.

### Exploitation
 

The Red Team was able to penetrate `Target 1` and retrieve the following confidential data:
- Target 1
  - `flag1.txt`: b9bbcb33e11b80be759c4e844862482d

    - **Exploit Used**
      - Using OSINT we have identified that the Target website is using WordPress. 
        When we right clicked on the website to go "Inspect Element" and search for "wordpress" we found two in the html as shown in the image below. 

![wordpress.png](https://github.com/krisyslab/ThirdProject/blob/64820315047f7039a054604e52f37ee6f31d2e16/images/wordpress.PNG)

      - Then we used 'wpscan' to enumerate the pages and users of the Target1 VM

      $ wpscan --url http://192.168.1.110/wordpress -eu

![wpscan.png](https://github.com/krisyslab/ThirdProject/blob/0a8440f1018e4a39d9c0b69f61ee2b9b1ba97567/images/wpscan.PNG)

![wpscan2.png](https://github.com/krisyslab/ThirdProject/blob/0a8440f1018e4a39d9c0b69f61ee2b9b1ba97567/images/wpscan2.PNG)

      - We focused on knowning the usernames when we executed the 'wpscan' and first targeted the user 'michael'. 
        We proceeded to SSH into the Target VM using username 'michael' and tried to guess the password.
      
![michael.png](https://github.com/krisyslab/ThirdProject/blob/1a1ceff35d03fc7adfde5f8a2b9c83830a69e864/images/michael.PNG)

      - Exploring the system and traversing to '/var/www' we used grep to search for flags.

      $ grep -RE flag html

![flag1.png](https://github.com/krisyslab/ThirdProject/blob/b5084ee3fa1d9fd63045ac162e5b6d52f48efad8/images/flag1.PNG)

  - `flag2.txt`: b9bbcb33e11b80be759c4e844862482d
      
    - **Exploit Used**

      - Doing an 'ls' in the '/var/www/' folder showed the flag2.txt.

    ```
      $ cd /var/www
      $ ls
      $ cat flag2.txt
    ```
 ![flag2hash.png](https://github.com/krisyslab/ThirdProject/blob/fd6a47e873f21d4f535d415f2c90ae1e43ec76ee/images/flag2hash.PNG)   
      

  - `flag3.txt`: afc01ab56b50591e7dccf93122770cd2

    `flag4.txt`: 715dea6c055b9fe3337544932f2941ce
      
    $ cat /var/www/html/wordpress/wp-config.php

![flag3,4.png](https://github.com/krisyslab/ThirdProject/blob/fd6a47e873f21d4f535d415f2c90ae1e43ec76ee/images/flag3and4.PNG)

![flag4.png](https://github.com/krisyslab/ThirdProject/blob/fd6a47e873f21d4f535d415f2c90ae1e43ec76ee/images/flag4theend.PNG)

- **Exploit Used**

    - We further traversed the system and logged-in to MySQL
      ```
        $ mysql -u root -p
        Enter password: R@venSecurity
      ```  
    - Having acquired the password hashes of the users, we used john the ripper to get the passwords.

![john.png](https://github.com/krisyslab/ThirdProject/blob/c7b8ed403f6e2e27dacb8ec37cde81660db078bb/images/johnfin.PNG)

  - We then SSH to the system using steven's password cracked in john the ripper.

![steven.png](https://github.com/krisyslab/ThirdProject/blob/c7b8ed403f6e2e27dacb8ec37cde81660db078bb/images/steven.PNG)

  - After getting access into SSH we then escalated out privilege to root.
          ```
            $ sudo python -c 'import pty;pty.spawn("/bin/bash");'
          ``` 
![sudoescalated.png](https://github.com/krisyslab/ThirdProject/blob/c7b8ed403f6e2e27dacb8ec37cde81660db078bb/images/sudoescalated.PNG)

Prepared by Kris Martinez

© 2021 Trilogy Education Services, a 2U, Inc. brand. All Rights Reserved.