# Blue Team: Summary of Operations

### Table of Contents
- Network Topology
- Description of Targets
- Monitoring the Targets
- Patterns of Traffic & Behavior
- Suggestions for Going Further

### Network Topology

![images/Network Topology Final Project.jpg](https://github.com/krisyslab/ThirdProject/blob/4bbe1ec4545dfd5617ca4b9cf5f2b4d785719bb4/images/Network%20Topology%20Final%20Project.jpg)

The following machines were identified on the network:

- Kali
  - **Operating System**: 

      Linux 5.4.0-kali3-amd64 x86_64
  - **Purpose**:

      The VM used to attack Target 1
  - **IP Address**:

      192.168.1.90
- ELK
  - **Operating System**:

      Linux 4.15.0-99-generic x86_64
  - **Purpose**:

      The ELK stack VM that will receive inputs from Target 1

      Access the Kibana from the browser in the Host VM
  - **IP Address**:

      192.168.1.100

- Capstone
  - **Operating System**:

      Linux 4.15.0-108-generic x86_64
  - **Purpose**:
    
    Used to test alerts

    Filebeat, Metricbeat and PacketBeat are installed and will forward logs to the ELK machine
  - **IP Address**:

      192.168.1.105

- Target 1
  - **Operating System**:

      Linux 3.16.0-6-amd64 x86_64
  - **Purpose**:

      The VM to be that is compromised. Exposes a vulnerable WordPress server.
      
      FileBeat, MetricBeat and PacketBeat installed to forward logs to ELK machine
  - **IP Address**:

      192.168.1.110


### Description of Targets

The target of this attack was: `Target 1` (192.168.1.110).

Target 1 is an Apache web server and has SSH enabled, so ports 80 and 22 are possible ports of entry for attackers. As such, the following alerts have been implemented:

- CPU Usage Monitor
- HTTP Request Size Monitor
- Excessive HTTP Errors

### Monitoring the Targets

Traffic to these services should be carefully monitored. To this end, we have implemented the alerts below:

  #### CPU USAGE Monitor

  **Implementation**:
  - **Metric**: WHEN max() OF system.process.cpu.total.pct OVER all documents IS ABOVE 0.5 FOR THE LAST 5 minutes
  - **Threshold**: IS ABOVE 0.5
  - **Vulnerability Mitigated**: When a malicious software (malware) in the system is running and takes up resources
  - **Reliability**: The Alert is highly reliable as it measures a spike in the use of resources from the normal usage

  #### HTTP Request Size Monitor

  **Implementation**:
  - **Metric**: WHEN sum() of http.request.bytes OVER all documents IS ABOVE 3500 FOR THE LAST 1 minute
  - **Threshold**: 3500
  - **Vulnerability Mitigated**: Code injection in HTTP requests (XSS and CRLF) or DDOS
  - **Reliability**: It can be rated as Medium reliability as this may create false positives. A large non malicious HTTP request or legitimate HTTP traffic may occur but nonetheless the alert needs to be implemented to establish a more appropriate threshold for this particular website in the future when enough data has been gathered.

  #### Excessive HTTP Errors

  **Implementation**:
  - **Metric**: WHEN count() GROUPED OVER top 5 'http.response.status_code' IS ABOVE 400 FOR THE LAST 5 minutes
  - **Threshold**: IS ABOVE 400
  - **Vulnerability Mitigated**: Enumeration/Brute Force
  - **Reliability**: This can be rated as highly reliable. Considering the size of the business and the service it offers, an error code higher than 400 for 5 minutes will indicate a malicious activity being directed to the website. 

### Suggestions for Going Further

The logs and alerts generated during the assessment suggest that this network is susceptible to several active threats, identified by the alerts above. 
In addition to watching for occurrences of such threats, the network should be hardened against them. The Blue Team suggests that IT implement the fixes below to protect the network:

- VUlnerability 1: Vulnerable to Brute Force attack due to weak passwords and poor SSH configurations
  - **Patch**:

              - Change SSH port to a non-standard port 
  - **Why It Works**:

              - SSH by default is set to listen on port 22. 
              
              Running SSH on a non-standard port offers additional security by obscurity. 

              Disclaimer: Although it will hide the SSH server from the casual prober, but it will not hide the server from someone who is targeting the business specifically.
    - **Patch**:

              - Limit SSH access to specific clients by IP address
                    * Filtering with IPTABLES
  - **Why It Works**:

              - Iptable rules are effective at limiting which client are given access via SSH. 
              For example to allow traffic from a particular IP address and otherwise drom the traffic coming from other IP address.

                  iptables -A INPUT -p tcp --dport 22 --source 192.168.1.105/24 -j ACCEPT
                  iptables -A INPUT -p tcp --dport 22 -j DROP

- Vulnerability 2: WordPress User Enumeration to identify usernames
  - **Patch**:

              - Use Stop User Enumeration Plugin
  - **Why It Works**:

              - Tools like `WPSCAN` are designed for use by ethical hackers to find users login names. 
              
              `Stop User Enumeration` is a security plugin designed to detect and prevent malicious individuals from scanning the website for login usernames.

            INSTALLATION:
              Either using the dashboard ‘Add Plugin’ feature to find, install and activate the plugin, or
              1. Download and the plugin from the download link.
              2. Upload the entire stop-user-enumeration directory to your website’s /wp-contents/plugins/stop-user-enumeration using a file manager or FTP.
              3. Activate the plugin through the Plugins menu.
              
- Vulnerability 3: Weak security configurations to protect wp-config.php

  - **Patch**: 
  
            Secure wp-config.php through:
              - Protection through .htaccess file
              - Moving wp-config.php file
              - Modify wp-config.php File
  - **Why It Works**:

              - Protection through .htaccess file 
                  * This basically blocks access to the wp-config.php from internal hacking and code modification.

                      INSTRUCTIONS:
                        Download the .htaccess file which can be found in the root directory of the WordPress website.
                        Open the .htaccess file using any text editor application.
                        Include the following lines of code in the end of the .htaccess file:
                        
                        ```bash
                            #secure wp-config.php

                            <files wp-config.php>

                            order allow, deny

                            deny from all

                            </files>
                        ```         

              - Moving wp-config.php file
                  Usually, the wp-config.php file is located in the root directory. 
                  
                  The best practice is to move it to an unpredictable location in order to secure the sensitive data stored inside the file.


              - Modify wp-config.php File
                  During this exercise we found the wp-config.php in the /var/www/html. 
                  
                  This is a directory that is a public_html file which can ne reached by the WordPress website visitors. 
                  
                  Create a new configuration file that is stored in a different location such that when wp-config.php is opened, the sensitive data is included from a separate file: 
                      
                      INSTRUCTIONS:
                      Open the current wp-config.php file and move the lines which contain the database connection details, database prefix and also WordPress security keys. 
                      
                      Append <?php at the starting of the new configuration file and ?> at the end of the file.

                      After moving all the sensitive data from the wp-config.php file, add the following line just as the <?php term in the wp-config.php file:

                            <?php

                            include(‘/home/yourusername/config.php’);


Prepared by Kris Martinez

© 2021 Trilogy Education Services, a 2U, Inc. brand. All Rights Reserved.