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

