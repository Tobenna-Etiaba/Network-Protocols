<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

# Network Security Groups & Protocol Inspection

Here I will demonstrate how to use a virtual machine to reach/connect to another virtual machine and experiment with various network protocols using Wireshark and PowerShell.

<h2>Tools Used</h2>

- Microsoft Azure
- Remote Desktop
- Wireshark
- Powershell

<h2>Operating Systems Used</h2>
 
 - Windows 10 Pro (22H2) 
 - Ubuntu Server 20.04

<h2>Set up</h2>
 
 - I started out by creating two virtual machines in Azure one for Windows and the other for the Ubuntu server.

<img width="1248" alt="Screenshot 2025-02-04 at 23 33 35" src="https://github.com/user-attachments/assets/4832d73c-b7b4-4776-abd1-ae31297de586" />
 

- In order to ensure that the Windows VM could successfully connect to the Ubuntu server I had to make sure that both VMs had the same resource group and the same network group.
<img width="1062" alt="Screenshot 2025-02-04 at 23 35 06" src="https://github.com/user-attachments/assets/fd6a02ac-ee6f-4cdb-99ff-d3c74d5fddae" />

<img width="854" alt="Screenshot 2025-02-04 at 23 36 56" src="https://github.com/user-attachments/assets/357f8261-62f2-4590-a6ff-2c408e667452" />

- And with that, the 2 VMs are ready to be used.

<img width="1727" alt="Screenshot 2025-02-04 at 23 45 40" src="https://github.com/user-attachments/assets/5600f441-e942-455f-a075-82398b3fe822" />

- After creating the VMs I then took the IP address of the windows VM and used it to log in.
<img width="1148" alt="Screenshot 2025-02-04 at 23 46 11" src="https://github.com/user-attachments/assets/e3626a5b-e2d2-49ee-b5ec-17b718e70952" />

- Once I logged in the windows VM I downloaded and installed wireshark.
<img width="1688" alt="Screenshot 2025-02-04 at 23 49 11" src="https://github.com/user-attachments/assets/98e7caf8-828c-436b-bc03-b94d671acc54" />

<img width="498" alt="Screenshot 2025-02-04 at 23 50 04" src="https://github.com/user-attachments/assets/b072f549-b8c4-4dfb-935d-60169ebe732b" />

<img width="750" alt="Screenshot 2025-02-04 at 23 52 50" src="https://github.com/user-attachments/assets/0ad1339d-fa81-4e0a-8f1e-7473c97da453" />

<h2>Configuring a Firewall</h2>

- Once wireshark is opened you can select ethernet then click on the blue sharkfin icon at the top left to start observing traffic.

<img width="1719" alt="Screenshot 2025-02-04 at 23 54 22" src="https://github.com/user-attachments/assets/2c24c47b-354a-427a-acdc-f8bb63237eda" />

- Once wireshark and powershell have been opened you can then filter for ICMP traffic

<img width="857" alt="Screenshot 2025-02-04 at 23 55 26" src="https://github.com/user-attachments/assets/e166f96c-8883-430a-9971-022bc5879d75" />

- As you can see there is nothing there yet.

- From here we can connect to the Ubuntu server via the windows VM by retrieving the private IP address of the Ubuntu server and pinging it from within wireshark.

<img width="1413" alt="Screenshot 2025-02-04 at 23 56 17" src="https://github.com/user-attachments/assets/7ba778e6-d7af-4dd7-b741-de5db30e58b6" />

<img width="858" alt="Screenshot 2025-02-04 at 23 56 38" src="https://github.com/user-attachments/assets/50736a86-e092-498c-91ce-21c70ad95908" />

<img width="856" alt="Screenshot 2025-02-04 at 23 56 58" src="https://github.com/user-attachments/assets/5629de63-0d61-475e-b4ce-13e35e970f3e" />


- In the image just right above, you can see the ICMP traffic showing up in wireshark. What's going is the windows VM which has a private IP address of 10.0.0.4 is trying to send a message to the Ubuntu server with the aim of confirming weither or not it is reachable on the network. In wireshark it shows request and reply, request is the windows VM reaching out while reply is the Ubuntu server confirming that it is indeed reachable on the network.

- We can see a similar example below with the windows VM reaching out to google.

<img width="1718" alt="Screenshot 2025-02-04 at 23 59 01" src="https://github.com/user-attachments/assets/63ce924c-ac63-487c-9008-1eb4140f5aa7" />

- We can have the windows VM communicate with the Ubuntu server endlessly through the use of a perpetual ping (ping *private ip address* -t).

<img width="1718" alt="Screenshot 2025-02-05 at 00 02 12" src="https://github.com/user-attachments/assets/694326d9-2cac-4856-8419-e91be619c25c" />

- It's possible to block all the incoming request from the windows VM to the Ubuntu server in Azure by creating a new inbound security rule provided that it's priority value is set to a lower value than the current rule as Azure prioritises the rules with lower values.

<img width="1409" alt="Screenshot 2025-02-05 at 00 03 54" src="https://github.com/user-attachments/assets/2100a14c-be90-4e7b-929d-f4fd1545b90d" />

<img width="586" alt="Screenshot 2025-02-05 at 00 04 46" src="https://github.com/user-attachments/assets/cc6bb709-6b01-4caf-8437-d020212d7aae" />

- If you look at the image right above you can see that Destination Port Ranges was not given a value and thats beacuse ICMP(Internet Control Message Protocol) does not use any ports.

- Looking at the image below you can see that in powershell the request has been timed out due to the fact that the new inbound security rule on the Ubuntu server was set to ignore incoming traffic and in wireshark we can see that the windows VM is sending requests continually but is not getting a response from the Ubuntu server anymore.

<img width="1717" alt="Screenshot 2025-02-05 at 00 09 25" src="https://github.com/user-attachments/assets/beb7acbf-312b-4c53-8e20-e9c9fbe9f0c3" />

- We can go back to Azure and change the inbound security rule from deny back to allow so that the ping requests can go through again.

<img width="586" alt="Screenshot 2025-02-05 at 00 10 10" src="https://github.com/user-attachments/assets/9349c5be-fc49-4fd9-876b-644e582267c3" />

<img width="1717" alt="Screenshot 2025-02-05 at 00 10 27" src="https://github.com/user-attachments/assets/96fde8aa-98aa-46f2-ae14-9919f50ceb12" />

<h2>Filtering SSH Traffic</h2>

- We can filter for SSH traffic in wireshark or by typing; tcp.port == 22(SSH uses Transmission Control Protocol to transmit data)

<img width="858" alt="Screenshot 2025-02-05 at 00 23 26" src="https://github.com/user-attachments/assets/e96d5072-6314-40b1-b65c-ca20cd261e41" />

- In the image below we can observe SSH traffic in wireshark, by typing the command:
  - ssh *username@ubuntuserversprivateIPaddress*

<img width="1718" alt="Screenshot 2025-02-05 at 00 24 34" src="https://github.com/user-attachments/assets/6d501c78-d56e-4eb5-81f4-01e365f19431" />

- 
