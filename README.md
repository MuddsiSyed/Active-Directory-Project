# Active Directory Project
## Objective:
The objective of this project is to build an Active Directory home lab in an AWS EC2 environment, integrating key security tools such as Splunk, Kali Linux, and Atomic Red Team. This setup will provide hands-on experience with understanding and managing a domain environment, learning how to ingest events into Splunk, and generating telemetry data to simulate real-world cyber attacks. The primary goal is to enhance the ability to detect and respond to security incidents by analyzing attack patterns and data streams, ultimately improving future detection capabilities.

## Skills Learned:
- <b> Active Directory Management: </b> Gained experience in setting up and managing an Active Directory environment, including domain controllers, user management, and group policies.
- <b> AWS EC2 Management: </b> Developed proficiency in launching and configuring AWS EC2 instances, networking, and ensuring proper security configurations for cloud-based infrastructure.
- <b> Security Tool Integration: </b> Learned how to configure and integrate security tools like Splunk for event monitoring and analysis, Kali Linux for penetration testing, and Atomic Red Team for simulating attack techniques.
- <b> Event Ingestion & Analysis with Splunk: </b> Developed the ability to ingest security-related logs and telemetry data into Splunk, creating alerts and dashboards to detect malicious activities and anomalies.
- <b> Penetration Testing & Attack Simulation: </b> Gained practical experience in using Kali Linux for vulnerability scanning and penetration testing, and using Atomic Red Team to simulate a variety of cyber attacks.
- <b> Threat Detection & Incident Response: </b> Enhanced skills in threat hunting and incident response by analyzing the data generated by simulated attacks, identifying attack patterns, and preparing actionable insights for security improvement.

## Tools Used:
- <b> AWS EC2: </b> For hosting and managing the virtual environment for Active Directory, Splunk, Kali Linux, and Atomic Red Team.
- <b> Active Directory: </b> To simulate a real-world domain environment for testing, including creating user accounts, group policies, and organizational units.
- <b> Splunk: </b> Used for ingesting and analyzing logs, creating search queries, and visualizing security events for effective monitoring and detection.
- <b> Kali Linux: </b> Deployed for penetration testing and vulnerability assessments, allowing the simulation of attacks against the Active Directory infrastructure.
- <b> Atomic Red Team: </b> Used for simulating known attack techniques and generating telemetry data to help fine-tune detection capabilities within Splunk.

## Steps:
### Launch 2 Windows Server, 1 Ubuntu Server and 1 Kali Linux instance in AWS
- One Windows Server will be our Target-PC
- The other Windows Server will be our Active Directory
- Ubuntu Server will be our Splunk Server
- Kali Linux will be our attacker machine </br> </br>

#### Configuration on AWS for launching instances
- <b> Windows Server (Target-PC) </b>
   - Enter the name as Target-PC
   - Select Windows from the Quick Start Option
   - Microsoft Windows Server 2025 BASE
   - T3 Medium (2 vCPU and 4 GiB RAM)
   - Select a Key Pair 
   - Network Settings: Select existing security group </br>
    &nbsp; &nbsp; &nbsp; &nbsp; - I have selected my existing security group (I will mention the i/b and o/b rules on the next steps)
   - Storage: 50 GB </br></br>
- <b> Windows Server (Active Directory - ADDC01) </b>
   - Enter the name as ADDC01
   - Select Windows from the Quick Start Option
   - Microsoft Windows Server 2025 BASE
   - T3 Medium (2 vCPU and 4 GiB RAM)
   - Select a Key Pair 
   - Network Settings: Select existing security group </br>
    &nbsp; &nbsp; &nbsp; &nbsp; - I have selected my existing security group (I will mention the i/b and o/b rules on the next steps)
   - Storage: 50 GB </br></br>
- <b> Ubuntu Server (Splunk Server) </b>
   - Enter the name as Splunk Server
   - Select Ubuntu from the Quick Start Option
   - Ubuntu Server 24.04 LTS
   - T3 Large (2 vCPU and 8 GiB RAM)
   - Select a Key Pair 
   - Network Settings: Select existing security group </br>
    &nbsp; &nbsp; &nbsp; &nbsp; - I have selected my existing security group (I will mention the i/b and o/b rules on the next steps)
   - Storage: 100 GB </br></br>
- <b> Kali Linux </b>
   - Enter the name as Kali Linux
   - Select Kali Linux from AMI options
   - Ubuntu Server 24.04 LTS
   - T2 Medium (2 vCPU and 4 GiB RAM)
   - Select a Key Pair 
   - Network Settings: Select existing security group </br>
    &nbsp; &nbsp; &nbsp; &nbsp; - I have selected my existing security group (I will mention the i/b and o/b rules on the next steps)
   - Storage: 30 GB </br></br>

![Screenshot from 2025-03-13 23-03-51](https://github.com/user-attachments/assets/64dc0b83-9559-4a8c-a9c0-0a39d0d00b45)</br>
REF 2 - All EC2 Instances launched for Active Directory Project

#### Intalling Splunk on The Ubuntu Server (Splunk Server)
- <b> SSH into the Splunk Server and enter the following commands to install Splunk </b>
     - sudo apt-get update && sudo apt-get upgrade (To upgrade Ubuntu)
     - wget -O splunk-9.2.2-d76edf6f0a15-linux-2.6-amd64.deb "https://download.splunk.com/products/splunk/releases/9.2.2/linux/splunk-9.2.2-d76edf6f0a15-linux-2.6-amd64.deb" (To Download Splunk DEB File)
     - sudo apt install ./splunk-9.2.2-d76edf6f0a15-linux-2.6-amd64.deb (To install Splunk Enterprise)
     - sudo /opt/splunk/bin/splunk start — accept-license (To accept the license before starting Splunk)
     - Press and enter Y to accept the licence and proceed with the installation
     - Create your username and password for splunk log in
     - sudo /opt/splunk/bin/splunk start (To start Splunk)
     ![Screenshot from 2025-03-13 23-55-12](https://github.com/user-attachments/assets/4c0d9e2f-98c8-47a5-a7d0-1e90eccf3d96)</br>
     REF 3 - Splunk Installed Successfully
     - If you see above screen where it says that the web interface is available at the privateIP:8000, then splunk is installed successfully
- <b> After Splunk is installed on the splunk server, you can access the splunk web from your local machine </b>
     - Go to Amazon EC2 instances page and check the public IP Address of the Splunk Server instance
     - Go to your local machine's web browser and enter the address as (http://publicIP of splunk server:8000) - In my case the address is http://43.204.233.77:8000
     ![image](https://github.com/user-attachments/assets/9d3e69fb-3e41-4e5d-9f35-26af290c7ba2)</br>
     REF 4 - Splunk Web Interface
     - Enter your credentials to log in
  


## Conclusion: 
This project provided valuable hands-on experience in building and managing a security lab within AWS, simulating real-world attack scenarios, and gaining deep insights into network and domain security. By integrating tools like Splunk, Kali Linux, and Atomic Red Team, the project not only deepened understanding of Active Directory but also significantly enhanced the ability to detect and respond to modern cyber threats. The ability to monitor and analyze security events within a simulated attack environment has reinforced key skills in security operations, threat detection, and incident response, which will be crucial for securing networks and systems in real-world scenarios.
