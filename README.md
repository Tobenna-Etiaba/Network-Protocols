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
 
 - I started out by creating two virtual machines on Azure one for Windows and the other for the Ubuntu server.

<img width="1248" alt="Screenshot 2025-02-04 at 23 33 35" src="https://github.com/user-attachments/assets/4832d73c-b7b4-4776-abd1-ae31297de586" />
 

- To ensure that the Windows VM could successfully connect to the Ubuntu server, I had to ensure that both VMs were within the same resource group and network group.
<img width="1062" alt="Screenshot 2025-02-04 at 23 35 06" src="https://github.com/user-attachments/assets/fd6a02ac-ee6f-4cdb-99ff-d3c74d5fddae" />

<img width="854" alt="Screenshot 2025-02-04 at 23 36 56" src="https://github.com/user-attachments/assets/357f8261-62f2-4590-a6ff-2c408e667452" />

- And with that, the 2 VMs are ready to be used.

<img width="1727" alt="Screenshot 2025-02-04 at 23 45 40" src="https://github.com/user-attachments/assets/5600f441-e942-455f-a075-82398b3fe822" />

- After creating the VMs, I took the public IP address of the Windows VM and used it to log in.
<img width="1148" alt="Screenshot 2025-02-04 at 23 46 11" src="https://github.com/user-attachments/assets/e3626a5b-e2d2-49ee-b5ec-17b718e70952" />

- Once I logged in to the Windows VM I downloaded and installed wireshark.
<img width="1688" alt="Screenshot 2025-02-04 at 23 49 11" src="https://github.com/user-attachments/assets/98e7caf8-828c-436b-bc03-b94d671acc54" />

<img width="498" alt="Screenshot 2025-02-04 at 23 50 04" src="https://github.com/user-attachments/assets/b072f549-b8c4-4dfb-935d-60169ebe732b" />

<img width="750" alt="Screenshot 2025-02-04 at 23 52 50" src="https://github.com/user-attachments/assets/0ad1339d-fa81-4e0a-8f1e-7473c97da453" />

<h2>Configuring a Firewall</h2>

- Once Wireshark is opened you can select ethernet then click on the blue sharkfin icon at the top left in the image below to start observing traffic.

<img width="750" alt="Screenshot 2025-02-04 at 23 53 03" src="https://github.com/user-attachments/assets/3a496b3a-37c5-4cfb-8f59-a3b01e88a4d4" />


<img width="1719" alt="Screenshot 2025-02-04 at 23 54 22" src="https://github.com/user-attachments/assets/2c24c47b-354a-427a-acdc-f8bb63237eda" />

- Once Wireshark and PowerShell have been opened you can then filter for ICMP traffic

<img width="857" alt="Screenshot 2025-02-04 at 23 55 26" src="https://github.com/user-attachments/assets/e166f96c-8883-430a-9971-022bc5879d75" />

- As you can see there is nothing there yet.

- From here we can connect to the Ubuntu server via the Windows VM by retrieving the private IP address of the Ubuntu server and pinging it from within Wireshark.

<img width="1413" alt="Screenshot 2025-02-04 at 23 56 17" src="https://github.com/user-attachments/assets/7ba778e6-d7af-4dd7-b741-de5db30e58b6" />

<img width="858" alt="Screenshot 2025-02-04 at 23 56 38" src="https://github.com/user-attachments/assets/50736a86-e092-498c-91ce-21c70ad95908" />

<img width="856" alt="Screenshot 2025-02-04 at 23 56 58" src="https://github.com/user-attachments/assets/5629de63-0d61-475e-b4ce-13e35e970f3e" />


- In the image just right above, you can see the ICMP traffic showing up in Wireshark. What's going on is the Windows VM which has a private IP address of 10.0.0.4 is trying to send a message to the Ubuntu server with the aim of confirming whether or not it is reachable on the network. In Wireshark it shows request and reply, request is the Windows VM reaching out while reply is the Ubuntu server confirming that it is indeed reachable on the network.

- We can see a similar example below with the Windows VM reaching out to Google.

<img width="1718" alt="Screenshot 2025-02-04 at 23 59 01" src="https://github.com/user-attachments/assets/63ce924c-ac63-487c-9008-1eb4140f5aa7" />

- We can have the Windows VM communicate with the Ubuntu server endlessly through the use of a perpetual ping (ping *private ip address* -t).

<img width="1718" alt="Screenshot 2025-02-05 at 00 02 12" src="https://github.com/user-attachments/assets/694326d9-2cac-4856-8419-e91be619c25c" />

- It's possible to block all the incoming requests from the Windows VM to the Ubuntu server in Azure by creating a new inbound security rule provided that its priority value is set to a lower value than the current rule as Azure prioritises the rules with lower values.

<img width="1409" alt="Screenshot 2025-02-05 at 00 03 54" src="https://github.com/user-attachments/assets/2100a14c-be90-4e7b-929d-f4fd1545b90d" />

<img width="586" alt="Screenshot 2025-02-05 at 00 04 46" src="https://github.com/user-attachments/assets/cc6bb709-6b01-4caf-8437-d020212d7aae" />

- If you look at the image right above you can see that Destination Port Ranges were not given a value and that's because ICMP(Internet Control Message Protocol) does not use any ports.

- Looking at the image below you can see that in Powershell the request has been timed out due to the fact that the new inbound security rule on the Ubuntu server was set to ignore incoming traffic and in wireshark we can see that the Windows VM is sending requests continually but is not getting a response from the Ubuntu server anymore.

<img width="1717" alt="Screenshot 2025-02-05 at 00 09 25" src="https://github.com/user-attachments/assets/beb7acbf-312b-4c53-8e20-e9c9fbe9f0c3" />

- We can go back to Azure and change the inbound security rule from deny back to allow so that the ping requests can go through again.

<img width="586" alt="Screenshot 2025-02-05 at 00 10 10" src="https://github.com/user-attachments/assets/9349c5be-fc49-4fd9-876b-644e582267c3" />

<img width="1717" alt="Screenshot 2025-02-05 at 00 10 27" src="https://github.com/user-attachments/assets/96fde8aa-98aa-46f2-ae14-9919f50ceb12" />

<h2>Filtering SSH Traffic</h2>

- We can filter for SSH traffic in Wireshark by typing ssh or tcp.port == 22(SSH uses Transmission Control Protocol to transmit data)

<img width="858" alt="Screenshot 2025-02-05 at 00 23 26" src="https://github.com/user-attachments/assets/e96d5072-6314-40b1-b65c-ca20cd261e41" />

- In the image below we can observe SSH traffic in Wireshark, by typing the command:
   - ssh *username@ubuntuserversprivateIPaddress*

<img width="1718" alt="Screenshot 2025-02-05 at 00 24 34" src="https://github.com/user-attachments/assets/6d501c78-d56e-4eb5-81f4-01e365f19431" />

- In the image right above we can see the ssh traffic in Wireshark. What's happening in the image is a successful SSH establishment between the Windows VM and the Ubuntu server. The session includes:
  - a protocol exchange
  - an Elliptic Curve Diffie-Hellman (ECDH) key exchange and;
  - encrypted communication.

<h2>Filtering DHCP Traffic</h2>

- We can filter for DHCP traffic in Wireshark by typing dhcp or udp.port == 67 || udp.port == 68(DHCP uses User Datagram Protocol to transmit data)

<img width="858" alt="Screenshot 2025-02-05 at 00 26 33" src="https://github.com/user-attachments/assets/dc3058d8-8079-4555-a9ac-fc8b33843158" />

- DHCP(Dynamic Host Configuration Protocol) is the protocol used to assign an IP address to devices when they are first connected to the network. And as a result, we can use ipconfig /renew to try and get a new IP address.

<img width="1718" alt="Screenshot 2025-02-05 at 00 27 55" src="https://github.com/user-attachments/assets/38299653-1cc5-4ded-bc5c-eb6feacd69ed" />

- In the image right above we are unable to get a new IP address by running ipconfig /renew due to the fact that the original IP address needs to be ditched. In order to do that you can use ipconfig /release and from there use ipconfig /renew, however the moment you type ipconfig /release the Windows VM will be disconnected so to have the two commands run successively you can type them in a textfile(notepad) and save it somewhere on the VM.

- You can type cd in Powershell to change directory, and from there use ls(to show lists) and then type; ".\\*the name of the save textfile*.bat" (to run the commands in the saved text file)

<img width="1278" alt="Screenshot 2025-02-05 at 00 36 08" src="https://github.com/user-attachments/assets/20184a99-d2ff-4cdc-b6a6-952d5053d8ed" />

<img width="859" alt="Screenshot 2025-02-05 at 00 37 43" src="https://github.com/user-attachments/assets/9778ae8d-d477-40a2-a5d2-be10004b8bc5" />

<img width="1722" alt="Screenshot 2025-02-05 at 00 38 18" src="https://github.com/user-attachments/assets/81154c71-7bc6-4ead-8568-b6a4ced11ed7" />

- In the image right above we can see from the traffic in wireshark that we were able to successfully request for a new IP address. 

- What's going on in Wireshark from the image above is the Windows VM is communicating to the network concerning it no longer using its current IP address. The Windows VM(no longer 10.0.0.4 but now 0.0.0.0) communicates to the DHCP server so that it can have a new IP address. The DHCP server communicates with the device and offers it a new IP address, and as a result, the Windows VM is now able to acquire a new IP address.

<h2>Filtering DNS Traffic</h2>

- We can filter for DNS traffic in Wireshark by typing DNS or udp.port == 53 || tcp.port == 53(DNS uses both Transmission Control Protocol & User Datagram Protocol to transmit data)

<img width="859" alt="Screenshot 2025-02-05 at 00 42 57" src="https://github.com/user-attachments/assets/7ae19b7a-7c89-408a-97e0-cc120f562739" />

- DNS(Domain Name System) is used to convert human-readable domain names to an IP address that a computer can use and understand. This can be achieved by typing the command nslookup. In the image below we can take; www.google.com & www.disney.com and get their IP addresses which can then be understood and used by our VM(nslookup is used when you want to figure out a machines IP address if you know the name or vice versa).

<img width="859" alt="Screenshot 2025-02-05 at 00 45 46" src="https://github.com/user-attachments/assets/1ad148f3-f8a3-457b-b4aa-3eacbdbde8f7" />

<h2>Filtering RDP Traffic</h2>

- We can filter for RDP traffic in Wireshark or by typing rdp or tcp.port == 53(RDP uses Transmission Control Protocol).

<img width="858" alt="Screenshot 2025-02-05 at 00 46 15" src="https://github.com/user-attachments/assets/48bd5e75-08e6-454a-85eb-679b97fa5cbd" />

- Unlike the other protocols where commands needed to be typed in PowerShell in order to see the traffic in Wireshark, no further action is required here as RDP(Remote Desktop Protocol) is the protocol used to connect from one computer to another to gain remote desktop GUI and as a result, traffic is always being transmitted.
