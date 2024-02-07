<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />



<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

<h2>Actions and Observations</h2>

Welcome to the project! First you will need to create (2) VMs on Azure. One machine will be a Linux machine and the other will be a Windows 10 machine. Both will have (2) CPUs and they must be on the same VNET. Once that is done go on the Windows machine and download Wireshark. I will attatch a link to the wireshark download. https://www.wireshark.org/download.html. Once installed, open Wireshark and filter for ICMP Traffic only. ICMP is a network layer protocol that relays messages concerning network connection issues. The "ping" command uses this protocol and is used to test connectivity between hosts. When we filter Wireshark to only capture ICMP packets and ping the private IP address of our Linux machine, we can visually see the packets on Wireshark.

<img width="1318" alt="image" src="https://github.com/nuhsuleiban/azure-network-protocols/assets/153963865/2ad81083-b0ed-4372-8eb0-7a0dd26fd841">

We can inspect each individual packet and see the actual data that is being sent in each ping, as demonstrated in the picture below.

<img width="1318" alt="image" src="https://github.com/nuhsuleiban/azure-network-protocols/assets/153963865/2e45a2a9-2926-484c-9e43-3d4d0d53f983">

Next, we will perpetually ping the Linux machine with the command 'ping -t'. This will continually ping the machine until we decide to stop it. While the Windows machine is pinging the Linux machine we will go to the Linux machine and block inbound ICMP traffic on it's firewall. Once we do that we will stop receiving echo replies from the Linux machine. We will block ICMP by creating a new Network Security Group on the Linux machine that will be set to block ICMP. We can then re-allow the traffic by allowing ICMP in the NSG.

<img width="1318" alt="image" src="https://github.com/nuhsuleiban/azure-network-protocols/assets/153963865/49444380-8acf-4309-89a4-947fb565461d">

<img width="1318" alt="image" src="https://github.com/nuhsuleiban/azure-network-protocols/assets/153963865/6aabee28-aa62-4d26-9912-405180fb72bc">

Next we will use our Windows machine to SSH into the Linux machine. SSH will give us access to the machine's CLI. We will also set the Wireshark filter to capture SSH packets only. When we SSH into the Linux machine with the command "ssh labuser@10.0.0.5" (private IP address) we can see that Wireshark starts to immediately capture SSH packets, as shown in the picture below.

<img width="1318" alt="image" src="https://github.com/nuhsuleiban/azure-network-protocols/assets/153963865/a385461b-6ca8-4b6a-b4e9-0e7d5066ee0a">

Now we will use Wireshark to filter for the Dynamic Host Configuration Protocol (DHCP), which operates on UDP ports 67 and 68. It is used to assign IP addresses to client machines. We will request a new IP address with the command "ipconfig /renew". You can see this traffic pictured below.

<img width="1318" alt="image" src="https://github.com/nuhsuleiban/azure-network-protocols/assets/153963865/f70455e6-d56d-4960-9654-0b4c0bf572d1">

Now we'll analyze DNS (Domain Name Server) traffic by filtering it on Wireshark the same way. We will initiate DNS traffic by typing in the command "nslookup www.google.com." This command essentially asks our DNS server what is google's IP address. DNS traffic utilizes port number 53.

<img width="1318" alt="image" src="https://github.com/nuhsuleiban/azure-network-protocols/assets/153963865/dba31c82-7d65-49cb-a9a4-d027f84f3318">

Lastly we will filter for RDP (Remote Desktop Protocol) traffic, which operates on port 3389. This one should be apparent since we are using Remote Desktop Protocol to connect from our desktop to our Virtual Machines in Azure.

<img width="1318" alt="image" src="https://github.com/nuhsuleiban/azure-network-protocols/assets/153963865/b51abe55-0340-4981-8552-fb728041633e">

