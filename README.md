# Active Directory Project
## Objective:
The objective of this project is to build an Active Directory home lab in an AWS EC2 environment, integrating key security tools such as Splunk, Kali Linux, and Atomic Red Team. This setup will provide hands-on experience with understanding and managing a domain environment, learning how to ingest events into Splunk, and generating telemetry data to simulate real-world cyber attacks. The primary goal is to enhance the ability to detect and respond to security incidents by analyzing attack patterns and data streams, ultimately improving future detection capabilities.

![Screencastfrom2025-03-1621-33-38-ezgif com-speed](https://github.com/user-attachments/assets/751a51bc-e805-47b3-869e-142475072f85)</br>
REF 1 - Logical Diagram of Active Directory Project


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
- All the servers must be in the same subnet

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
REF 2 - All EC2 Instances launched for Active Directory Project and all servers are in the same subnet

#### Installing Splunk on The Ubuntu Server (Splunk Server)
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
     - Enter your credentials to log in </br></br>

#### Installing Splunk Forwarder and Sysmon on Windows Server (Target-PC)
- <b> RDP into the windows server and change the PC name to Target-PC </b>
    - Open any RDP client and gain the remote access of the windows server (Target-PC)
    - Click on search on the taskbar and type "View your PC Name"
    - Click on View your PC Name
    - Click Rename this PC
    - Type "Target-PC" and click Next
    - Click Restart Now (The remote desktop connection will also be lost)
    - RDP into the server again and got to View your PC Name and you will see the PC name is successfully changed to "Target-PC"
      ![Screenshot from 2025-03-14 01-25-08](https://github.com/user-attachments/assets/993f75de-27af-49d2-baec-e030854ed004)</br>
      REF 5 - PC name changed to "Target-PC"</br>
- <b> Installing Splunk Universal Forwarder and Sysmon </b>
    - On Target-PC, go to web browser and go to splunk.com
    - Log in using your Splunk Credentials
    - Go to Products > Free Trial and Downloads
    - Scroll down to Universal Forwarder and click on Get My Free Download
    - Go to Previous Releases and download the version sama as the version you have installed on the Splunk Server
    - In my case the version of the Splunk is 9.2.2 so I have downloaded 9.2.2 Splunk Forwarder
    - Open the downloaded MSI file
    - Check the box for License Agreeement and check "As on-premise Splunk Enterprise Instance" and click Next
    - Create a username and password and click Next
    - We don't have a deployment server so we can skip this option by clicking Next
    - For the Receiving Indexer, enter the IP Address of the host as the private IP Address of Splunk Server and the port will be 9997</br>
      ![Screenshot from 2025-03-14 01-40-31](https://github.com/user-attachments/assets/bcf57ced-71d8-4494-ba78-9803edafbe58)</br>
      REF 6 - Configuring the Receiving Indexer on Splunk Forwarder while installation
    - Click Next and Click Install
    - After Installation click on Finish
    - Go to Browser and search sysmon download and click on Sysmon - Sysinternals from Microsoft
    - Scroll down and click on Download Sysmon
    - Go to browser again and search sysmon olaf config (We will use olaf sysmon configuration)
    - Click on the GitHub page, scroll down and click on sysmonconfig.xml
    - Click on Raw, then right click > Save as and save in your Downloads folder
    - Go to your Download Directory then right click on Sysmon zip file and click extract all
    - Click on the address bar and copy the file path (C:\Users\Administrator\Downloads\Sysmon)
    - Open Powershell (Run as admin)
    - cd C:\Users\Administrator\Downloads\Sysmon
    - Enter the following command to install sysmon: .\sysmon.exe -i ..\sysmonconfig.xml
    - Click Agree
      ![Screenshot from 2025-03-14 02-33-06](https://github.com/user-attachments/assets/56a2c60f-830d-478d-a8d1-ac7ca5e67d44)</br>
      REF 6 - Sysmon is installed successfully

 - <b> Configuring Splunk Universal Forwarder on Target-PC to Send Data to Splunk Server </b>
    - For our splunk forwarder to forward the data, we will have to create and configure inputs.conf in the local configuration folder
    - On Target-PC go to C:\Program Files\SplunkUniversalForwarder\etc\system\local
    - Right Click and New > Text Document
    - Paste the following configuration </br></br>
      [WinEventLog://Application]</br>
      index = endpoint</br>
      disabled = false</br></br>
      
      [WinEventLog://Security]</br>
      index = endpoint</br>
      disabled = false</br></br>
      
      [WinEventLog://System]</br>
      index = endpoint</br>
      disabled = false</br></br>
      
      [WinEventLog://Microsoft-Windows-Sysmon/Operational]</br>
      index = endpoint</br>
      disabled = false</br>
      renderXml = true</br>
      source = XmlWinEventLog:Microsoft-Windows-Sysmon/Operational</br></br>
    - Click File > Save As and select the 'Save As Type' as All Files and enter the 'File Name' as inputs.conf then click Save
    - After creating or updating our inputs.conf configuration file, we should restart our universal forwarder in order for the changes to take effect
    - Click Search on the taskbar, search and open Services
    - Scroll down until you find SplunkForwarder
      ![Screenshot from 2025-03-15 04-39-40](https://github.com/user-attachments/assets/d1abad37-10fe-4bce-9ed2-03ff7c8b3923)</br>
      REF 7 - SplunkForwarder in Services
    - Check it's Log On As Column in Splunk Forwarder
      ![Screenshot from 2025-03-15 04-39-40](https://github.com/user-attachments/assets/79e0efbe-cb17-45e2-b008-26d439262f8f)</br>
      REF 8 - Splunk Forwarder logged in as NT SERVICE\SplunkForwarder
    - If 'Log On As' is NT SERVICE\SplunkForwarder, the forwarder might not be able to collect the logs due to some of the permissions
    - To fix this, change the 'Log On As' option to Local System
    - Right Click on SplunkForwarder and click properties
    - Click on Log On tab and select Local System account and click Apply and OK
    - Restart the service </br>

Now we have our splunk forwarder and sysmon installed along with it's configuration on Target-PC

#### Configuring Splunk Server to receive the logs
- <b> Creating 'endpoint' index in the splunk server to receive the logs from 'Target-PC' </b>
  - In inputs.conf file, we we have mentioned in our configuration 'index=endpoint'
  - Let's create an index endpoint in Splunk Server to receive the logs as the data from Target-PC will go to that index
  - Log in to the Splunk Web Interface (http://43.204.233.77:8000)
  - Click on Settings > Indexes
  - Click on New Index
    ![Screenshot from 2025-03-15 06-04-46](https://github.com/user-attachments/assets/ac45c0fc-a375-4664-a0a9-9063839a99a2)</br>
    REF 9 - Creating New Index Splunk Server
  - Enter the Index Name as 'endpoint' and click on Save
    Index 'endpoint' is now created in our splunk server
    ![Screenshot from 2025-03-15 06-10-00](https://github.com/user-attachments/assets/42c64552-d77f-4941-b008-044d83667094)</br>
    REF 10 - endpoint Index Created
- <b> Configure our Splunk Server to receive the data </b>
  - Go to Settings > Forwarding and receiving
  - Click on Add New under Receive Data
    ![Screenshot from 2025-03-15 06-14-39](https://github.com/user-attachments/assets/021768f8-fe35-4d1c-a9cc-baa691195e18)</br>
  - In 'Listen on this port' field, enter 9997
  - Click Save
- <b> Searching on Splunk Server to verify if we are receiving the logs from Target-PC </b>
  - Click Apps on Splunk Web and click on Searching and Reporting
  - Search index="endpoint"
  - You will see the logs from our Target-PC which verifies that we are receiving the logs
    ![image](https://github.com/user-attachments/assets/bc6a979d-dc3f-4a39-b44d-fb5a99e08405)</br>
    REF 11- Received logs from our Target-PC 

#### Installing Splunk Forwarder and Sysmon on Windows Server (Active Directory - ADDC01)
- <b> RDP into the other windows server and change the PC name to ADDC01 </b>
    - Open any RDP client and gain the remote access of the windows server (Target-PC)
    - Click on search on the taskbar and type "View your PC Name"
    - Click on View your PC Name
    - Click Rename this PC
    - Type "ADDC01" and click Next
    - Click Restart Now (The remote desktop connection will also be lost)
    - RDP into the server again and go to View your PC Name and you will see the PC name is successfully changed to "ADDC01"
      ![image](https://github.com/user-attachments/assets/c95f159b-724b-4f83-8a3e-ec1ad4b27fa9)</br>
      REF 12 - PC name changed to "ADDC01"
- <b> Install and Configure Splunk Universal Forwarder and Sysmon on (ADDC01) </b>
    - Repeat the same steps we did to install and configure Splunk Universal Forwarder and Sysmon on Target-PC
    - Once everything is done successfully you will see 2 hosts when searching in the endpoint index on our Splunk Server
      ![Screenshot from 2025-03-15 06-55-55](https://github.com/user-attachments/assets/24e19498-354d-4efd-97f6-2f38c404f51c)</br>
      REF 13 - Receiving logs from the 2 hosts Target-PC and ADDC01

Both Target-PC and ADDC01 have sysmon and splunk forwarder installed and they are configured to forward the logs to our splunk server. From the above, we can say that we are receiving the logs from both the hosts successfully.

#### Installing and Configuring Active Directory on ADDC01
- <b> Install Active Directory Domain Service </b>
   - Go to search and search 'Server Manager'
   - Click on Manage > Add Roles and Features
     ![Screenshot from 2025-03-15 07-07-23](https://github.com/user-attachments/assets/c98c62d9-3213-4555-84db-6a1604091318)</br>
     REF 14 - Adding Roles and Features
   - Click Next
   - Select Role-based or featured-based installation
   - Your default server will be selected. Click Next
   - From the list, click on Active Directory Domain Services
     ![Screenshot from 2025-03-15 07-17-01](https://github.com/user-attachments/assets/5c21dfcd-c7ac-42fd-ab0d-02e7e8c31858)</br>
     REF 15 - Adding server role - Active Directory Domain Services
   - Click on Add Features
   - Keep on clicking next until you get Install button
   - Click on Install and wait for it to get installed untill you see "Configuration required. Installation succeeded on ADDC01."
     ![Screenshot from 2025-03-15 07-26-50](https://github.com/user-attachments/assets/781cd855-e804-4013-a8d8-dbffa5e2ac6c)</br>
     REF 16 - Successfull installation of Active Directory Domain Service
   - Click Close
- <b> Promoting the Server to Domain Controller </b>
   - Click on the Flag icon on top and click on "Promote this server to a Domain Controller"
   - Click "Add a new forest" and enter the Root domain name as "adproject.local"
     ![image](https://github.com/user-attachments/assets/95b680d4-0107-4e1e-b4a9-dc45a97031e1)</br>
     REF 17 - Adding a new forest and configuring the root domain name
   - Click Next
   - Leave everything default and put in a password
   - Keep on clicking Next until you see the Paths tab
     ![image](https://github.com/user-attachments/assets/d9c39a06-6303-4110-903e-e9e5edbb2077)</br>
     REF 18 - Database DIT Path
   - These will be the paths used to store our database files named NTDS.DIT</br>
     <b>FYI</b> - Attackers love to target domain controllers because it has access to everything inluding the NTDS.DIT database file which contains everything related to active directory including password hashes.</br>
     If you notice any unauthorized activity on this file, you can assume that your entire domain has been <b> compromised</b>.
   - Keep Clicking Next until you see Install button then click Install
   - Once the installation is successfull you will be restarted
- <b> Creating users in our Active Directory </b>
   - We will create two objects (Organizational Units) and create one user in each organization
   - Once the system is restarted, go to Server Manager
   - Click on Tools > Active Directory Users and Computers
     ![image](https://github.com/user-attachments/assets/0fe75ff9-9e89-48ee-bb75-45442d2570db)</br>
     REF 19 - Active Directory Users and Computers
   - Right Click on adproject.local > New and Select Organizational Unit
     ![image](https://github.com/user-attachments/assets/4921eb72-a652-40ce-9fc4-22b4317eda4d)</br>
     REF 20 - Adding a new Object - Organizational Unit
   - Enter the name as "IT" and click OK
   - Right Click on IT > New and select User
   - Enter the name as Jenny Smith and the User Log On as jsmith
   - Click Next
   - Enter a password and uncheck "User must change password at next logon" and click Next and Finish
     ![image](https://github.com/user-attachments/assets/67165819-3662-4a91-b699-cbad0b117ca6)</br>
     REF 21 - A new user Jenny Smith in IT unit has now been created
   - Follow the same process and create a new Organizational Unit as "HR" and create a new user "Terry Smith" in it
     ![image](https://github.com/user-attachments/assets/2cfbea54-e211-4540-a914-1bc202189a4e)</br>
     REF 22 - A new user Terry Smith in HR unit has now been created

#### Configuring Target-PC to join adproject.local domain (Active Directory domain)
- <b> Configuration on AWS to configure the Target-PC DNS  </b>
   - Go to AWS and search DHCP options set and open it
   - Click on Create DHCP option set
   - Keep the name as my-dhcp
   - Enter the Domain Name as adproject.local (Your domain name) and Domain name servers as 172.31.10.42 (Private IP address of your Active Directory Server)
     ![image](https://github.com/user-attachments/assets/c453ae6d-eb91-4907-97a8-d6a7e1961aef)</br>
     REF 23 - Creating a DHCP option set
   - Scroll down and click on create DHCP option set
   - Now we need to attach this DHCP option set to our VPC
   - Click on Your VPCs
   - Select your VPC and click Actions > Edit VPC Settings
   - Click on DHCP option set and select the option set you have just created from the drop down menu
     ![image](https://github.com/user-attachments/assets/50d2f289-b943-4ae1-9874-d6f5b3f1dce3)</br>
     REF 24 - Editing VPC Settings
   - Click Save
- <b> Configuring Target-PC DNS </b>
   - RDP into Target-PC and go to Network and Internet Settings
   - Click on Ethernet
   - Scroll down and Click edit for DNS Server assignment
   - Select Manual and enter the preferred DNS as 172.31.10.42 (Private IP address of the Active Directory Server)
     ![image](https://github.com/user-attachments/assets/e12db80c-4386-4685-b34e-04f6c3281922)</br>
     REF 25 - Changing the DNS server to our Active Directory Server
   - Click Save
- <b> Joining the adproject.local domain </b>
   - Go to View My PC Name
   - Click on Domain or workgroup
     ![Screenshot from 2025-03-15 09-36-04](https://github.com/user-attachments/assets/442c7bcd-f125-4fb1-81de-d24197285389)</br>
     REF 26 - Clicking on Domain or workgroup
   - Click Change
   - Click Domain and type the domain name "adproject.local"
     ![image](https://github.com/user-attachments/assets/0d913921-026b-4e4d-aa56-fed9513f73ca)</br>
     REF 27 - Changing the domain to our Active Directory domain
   - Click OK
   - If the connection to our domain is successfull, it will ask for Username and Password
   - Enter the user name and password for ADDC01 Administrator (It should be the username and password which you used to RDP into the ADDC01 server)
   - Target-PC is added to the adproject.domain successfully.</br>
     ![image](https://github.com/user-attachments/assets/633a51e2-8af9-4d4e-858d-efa3f961edda)</br>
     REF 28 - Target-PC successfully joined our Active Directory domain
   - Restart the server for the changes to be applied
- <b> Logging in with the user we created Jenny Smith </b>
   - RDP into Target-PC as Administrator
   - We need to enable Remote Desktop access to the user Jenny Smith
   - Go to Settings > System and click on Remote Desktop
   - Click Remote Desktop Users
   - Click Add and enter Jenny Smith's user name (jsmith) in the object names field box
   - Click Check Names and click OK</br>
     ![image](https://github.com/user-attachments/assets/cbe250b7-66fe-43e1-9760-a29af6cefd24)</br>
     REF 29 - Adding the users for remote access
   - RDP into the Target-PC using the user name jsmith and use the password which you have created for this user
   - Now you have successfully logged in as the user jsmith
     ![Screenshot from 2025-03-15 10-16-39](https://github.com/user-attachments/assets/487d195d-5637-4b83-ba9a-0a2769a6b177)</br>
     REF 30 - Successfully logged in as the user jsmith

### Launching Brute Force Attacks through Kali Linux (Hydra)
In this section, we will launch brute force attacks in Kali Linux to generate telemetry through Hydra tool. Hydra (or THC Hydra) is a parallelized network login cracker built into various operating systems like Kali Linux, Parrot and other major penetration testing environments. It was created as a proof of concept tool, for security researchers to demonstrate how easy it can be to crack logins. Hydra works by using different approaches, such as brute-force attacks and dictionary attacks, in order to guess the right username and password combination. 

#### Updating Kali Linux and Installing Hydra
- <b> SSH into Kali Linux instance and enter the following commands to upgrade and to install Hydra </b>
  - sudo apt-get update && sudo apt-get upgrade
  - sudo apt install hydra
  - You can find more information on Hydra tool on this page: https://www.kali.org/tools/hydra/
#### Launching RDP Brute Force attacks to Target-PC through Hydra
- <b> Create a folder "ad-project" and create a passwords.txt file </b>
   - mkdir ad-project &nbsp; &nbsp;&nbsp; &nbsp; -- <i> (Making a foldeer called ad-project) </i>
   - cd /usr/share/wordlist/ &nbsp;&nbsp; &nbsp; &nbsp; -- <i> (Going to the folder wordlist which contains rockyou file for passwords) </i>
   - sudo gunzip rockyou.txt.gz &nbsp; &nbsp;&nbsp; &nbsp; -- <i> (Unzipping rockyou.tzt.gz to get the rockyou.txt file) </i>
   - cp rockyou.txt ~/ad-project &nbsp; &nbsp;&nbsp; &nbsp; -- <i> (Copying rockyou.txt to our project's folder) </i>
   - cd /ad-project &nbsp; &nbsp;&nbsp; &nbsp; -- <i> (Go to ad-project directory) </i>
   - head -n 20 rockyou.txt > passwords.txt &nbsp; &nbsp;&nbsp; &nbsp; -- <i> (To use the first 20 passwords and input it into a new file "passwords.txt") </i>
   - In our scenario, we want to target a specific password for specific users. So let's add in the correct password for the user jsmith in passwords.txt file.
   - vi passwords.txt
   - Add in the password which you have created for jsmith at the end (after 20 lines) and save the file
- <b> Launching the attack to our Target-PC using hydra tool </b>
   - Here is the command to launch the attack to our Target-PC
   - hydra -l jsmith  -P passwords.txt -f rdp://172.31.40.34 -v   </br> <i> hydra </i> is the tool </br> <i> -l </i> is to use a single username </br> <i> jsmith </i> is the username </br> <i> -P </i> is to use a password file </br> <i>-f</i> implies to stop this attack once it finds the correct password </br> <i>rdp://172.31.40.34</i> is to target the rdp port of the private ip of the Target-PC </br> <i>-v</i> is to run it in verbose mode.
   - Here is how it generates the attack and finds the password
     ![Screencastfrom2025-03-1623-11-31-ezgif com-optimize](https://github.com/user-attachments/assets/abf70d7c-33ff-4414-a178-7c8cabbee118)</br>
     REF 31 - Brute Force Attack in Action to our Target-PC 
   - After a successfull attack, it finds the correct password and will display it as below
     ![image](https://github.com/user-attachments/assets/4ecea2ce-5647-4c13-89ef-23390d3521c0)</br>
     REF 32 - Successfull Attack - Found the password to user name jsmith
 - <b> Searching on Splunk For the Logs Associated to This Attack </b>
   - Go to Apps > Search and Reporting on Splunk Web
   - Enter on Search Bar the following selecting the Last 15 minutes: </br>
     index="endpoint" EventCode=4625  &nbsp; &nbsp;&nbsp; &nbsp; -- <i> Event ID 4625 in Windows Event Viewer indicates a failed logon attempt, meaning an </br> &nbsp; &nbsp;&nbsp; &nbsp; account failed to log on to a computer, and it's a crucial security event for monitoring unauthorized access attempts </i>
   - We can see the search results as below --
     ![image](https://github.com/user-attachments/assets/302d29c4-ca9c-43a4-83af-9e087b03450c)</br>
     REF 33 - Searching all the logs related to EventCode 4625 from the last 15 minutes
   - From the above can see we got 23 log events (failed log in events)
   - If we scroll down and check the "user" field from the Interesting Fields we can the user for all 23 log in is jsmith
     ![image](https://github.com/user-attachments/assets/fbadf883-26e9-4e68-9dd5-9b5233d4038e)</br>
     REF 34 - User is jsmith
   - All the events in the search are recorded almost at the same time which indicates a brute force attack
     ![Screenshot from 2025-03-20 07-30-37](https://github.com/user-attachments/assets/c22f1dee-57f0-49d1-8e91-4cab242b2754)</br>
     REF 35 - All events (failed log ins) are generated at the same time
   - Let's open one of the event to gather more information
   - Click on "Show all 61 lines" on any of the event
     ![Screenshot from 2025-03-20 07-38-03](https://github.com/user-attachments/assets/0b1aed03-6e96-452c-8722-41306d27ac34)</br>
     REF 36 - Opening the event for more information
   - From the above we can gather the below information of the attack --
       - EventCode=4625 &nbsp; &nbsp; <i> Event ID for failed log in event </i>
       - ComputerName=Target-PC.adproject.local &nbsp; &nbsp; <i> The host from which this event is generated from i.e. Target-PC and the attack is directed to this machine </i>
       - Account Name: jsmith &nbsp; &nbsp; <i> The username which the attacker used to brute force </i>
       - Worksation Name: kali &nbsp; &nbsp; <i> The operating system of the attacker </i>
       - Source Network Address:	172.31.43.162 &nbsp; &nbsp; <i> The IP address of the attacker machine. (That is the IP address of our Kali Linux instance in AWS) </i>
       - Source Port: 0 &nbsp; &nbsp; <i> The source port used for the attack </i>
   - Therefore we are able to successfully detect the brute force attack through searching on Splunk for the respective logs
   - To enhance our detection capabilites, we can create dashboards and alerts on Splunk.
</br></br>
### Login Activity and Threat Monitoring Dashboard in Splunk
The Login Activity and Threat Monitoring Dashboard which I created in Splunk is designed to provide insights into both successful and failed login attempts, helping detect potential security threats. It includes a time chart for Failed Logins and another for Successful Logins, as well as a pie chart that shows the ratio of Successful vs. Failed Logins. Additionally, the dashboard features a table listing the Top 10 Source IPs for Failed Logins, allowing for easy identification of suspicious activity.</br></br>

By correlating the time of failed logins with successful logins, the dashboard enables the detection of login patterns that may suggest security concerns, such as brute-force attacks or compromised accounts. For example, if a user successfully logs in after several failed attempts, it may indicate an attacker trying various credentials. This correlation allows for proactive investigation and response to potential unauthorized access or account breaches.</br></br>
![image](https://github.com/user-attachments/assets/46f5c330-0710-4627-9e5c-d889b3745ca0)</br>
REF 37 - Login Activity and Threat Monitoring Dahsboard
- <b> Key Visualizations: </b>
   - <b>Failed Logins:</b>
     ![image](https://github.com/user-attachments/assets/0c94eade-a6ea-4755-afe4-ad836db9518f)</br>
     REF 38 - This visualization tracks the number of failed login attempts over the past 24 hours in real-time.</br>
     <b> Key Points to Consider: </b></br>
        - <b>Purpose:</b> The timechart helps monitor potentially malicious activities by tracking when and how many failed login attempts occur over a given period. A surge in failed login attempts could indicate a brute force attack or unauthorized access attempts.
        - <b> X-Axis (Time): </b>The horizontal axis represents time, typically broken down into intervals like minutes or hours.
        - <b> Y-Axis (Count of Failed Logins): </b>The vertical axis shows the count or frequency of failed login attempts. A spike in this metric could highlight an anomaly.
        - <b> Real-Time Search: </b>The data is updated continuously or at short intervals, ensuring the dashboard reflects the most current status of login attempts. This can be crucial for immediate response in case of a security threat.</br>
        - <b> Use Case: </b>Detecting unusual login failures could help identify suspicious behavior, such as brute-force attempts or the use of incorrect credentials by unauthorized users.
  </br></br>
   - <b>Successful Logins:</b>
     ![image](https://github.com/user-attachments/assets/1fe6b500-d526-4878-8885-35aa0e67f6b1)</br>
     REF 39 - This visualization tracks the number of successful login attempts over the past 24 hours in real time.</br>
      <b> Key Points to Consider: </b></br>
        - <b> Purpose: </b>The timechart for successful logins shows how often users are able to log into the system successfully. This helps establish baseline patterns for normal login activity, and any drastic deviation (e.g., spikes in successful logins) could indicate either a positive or suspicious change, depending on the context.
        - <b> Additional Context:</b>As both Failed Logins and Successful Logins are represented as timecharts and the Failed Logins chart is placed above the Successful Logins panel in the dashboard, we can detect a successful login from a specific user after many failed attempts. By checking the time of failed logins and the time of successful login for the same user, we can correlate the patterns and investigate whether a legitimate user managed to log in after repeated failures, or if this could indicate a compromised account or attack scenario.
        - <b> X-Axis (Time): </b>The horizontal axis represents time, typically broken down into intervals like minutes or hours.
        - <b> Y-Axis (Count of Successful Logins): </b>This axis shows the frequency of successful logins within the time frame. This helps the team understand regular usage patterns, and abnormal spikes can be investigated further.
        - <b> Real-Time Search: </b>Just like the failed logins chart, the data is updated in real-time, allowing for continuous monitoring of login activity.</br>
        - <b> Use Case: </b></br>
             <b>Tracking successful logins </b>can help identify irregular patterns such as logins from unexpected locations or devices, which could suggest account compromise or unauthorized access.</br>
             <b>Correlating Failed and Successful Logins: </b>By comparing the times of failed login attempts (tracked in the chart above) and successful logins, you can identify if a specific user successfully logs in after multiple failed attempts. This can help detect scenarios where attackers may have repeatedly tried different credentials until they succeeded, which could indicate a brute-force attack or unauthorized account access.
   </br></br>       
   - <b>Successful VS Failed Logins:</b>
   ![image](https://github.com/user-attachments/assets/76da4aee-e6f1-40c3-8fe8-377864b0d6d6)</br>
   REF 40 - This visualization provides a quick, high-level overview of the proportion of failed versus successful login attempts over a specific time period - the past 24 hours.</br>
    <b> Key Points to Consider: </b></br>
      - <b> Purpose: </b>The pie chart helps easily compare the relative number of successful logins against failed logins in a given time frame. This is crucial for identifying whether login attempts are mostly legitimate or if there’s a significant amount of failed logins that might indicate a security issue.
      - <b> Segments: </b></br>
            One segment will represent the failed login attempts.</br>
            The other segment will represent the successful login attempts.</br>
      - <b> Data Source: </b>The data is pulled from both successful and failed login events. It gives an overall picture of login health — if the failed logins take up a significant portion of the chart, this can be an immediate indicator that something needs investigation (e.g., brute force attacks, invalid credentials, or misconfigured systems).
      - <b> Use Case: </b></br>
            <b>Proactive Security Monitoring:</b> If the pie chart shows a disproportionate amount of failed logins (for example, 70% failed vs 30% successful), this could trigger an immediate investigation into potential attacks, misconfigurations, or suspicious activities.</br>
            <b>Comparing Trends:</b> Over time, the chart can show trends in the balance between successful and failed logins, highlighting shifts in login behavior that require attention.</br>


   - <b> Top 10 Source IPs for Failed Logins:</b>
   ![image](https://github.com/user-attachments/assets/d7d4334c-99a2-40f1-977c-9a1bacede5cf)</br>
   Ref 41 - Top 10 Source IPs for Failed Logins from the past 24 hours</br>
   <b> Key Points to Consider: </b></br>
      - <b> Purpose: </b>This visualization is designed to identify and track the IP addresses that have made the most failed login attempts within a specified time frame. Monitoring this information helps in identifying potential security threats, such as brute-force attacks or unauthorized access attempts, which could compromise your system.
      - <b> Use Case: </b></br>
           <b>Detect Suspicious Activity:</b> If an IP address is repeatedly attempting to log in and failing, it may indicate an attacker trying to guess passwords (brute-force attack).</br>
           <b>Track Malicious Sources:</b> Multiple failed login attempts from the same or similar IP addresses could help pinpoint malicious actors targeting your system.</br>
           <b>Enhance Security Monitoring:</b> By regularly reviewing the top source IPs with failed logins, you can proactively respond to security risks, block harmful IPs, or strengthen login security measures.</br>

      
  

## Conclusion: 
This project provided valuable hands-on experience in building and managing a security lab within AWS, simulating real-world attack scenarios, and gaining deep insights into network and domain security. By integrating tools like Splunk, Kali Linux, and Atomic Red Team, the project not only deepened understanding of Active Directory but also significantly enhanced the ability to detect and respond to modern cyber threats. The ability to monitor and analyze security events within a simulated attack environment has reinforced key skills in security operations, threat detection, and incident response, which will be crucial for securing networks and systems in real-world scenarios.
