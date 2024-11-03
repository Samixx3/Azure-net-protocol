<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />




<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>Actions and Observations</h2>

<p>
</p>
<p>
Welcome to this tutorial on Network Security Groups and Network Protocol Inspection. In this session, we’ll set up two virtual machines (VMs) on Microsoft Azure: one running Linux and the other running Windows 10. Both VMs will be configured with two CPUs and should be connected within the same Virtual Network (VNet).

### Step 1: Set Up VMs
1. In Azure, create the two VMs, making sure each has two CPUs.
2. Assign both VMs to the same VNet for seamless network connectivity between them.

### Step 2: Install Wireshark on the Windows VM
Once your VMs are set up, connect to the Windows machine and download Wireshark. You can find the installer at [Wireshark's official download page](https://www.wireshark.org/download.html). After downloading, install Wireshark.

### Step 3: Filter for ICMP Traffic in Wireshark
After installation, open Wireshark and set the filter to capture **ICMP traffic only**. ICMP, or Internet Control Message Protocol, operates at the network layer and is used for network diagnostics. The **ping** command utilizes ICMP to test connectivity between hosts.

### Step 4: Test Connectivity and Observe ICMP Packets
With Wireshark open and the ICMP filter enabled, try pinging the Linux VM’s private IP address from the Windows VM. You should now be able to see ICMP packets in real-time in Wireshark, allowing you to observe the network traffic as the two VMs communicate.
</p>
<br />
<p>
<img src="https://i.imgur.com/IIUShxp.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
In Wireshark, each packet can be inspected individually to reveal detailed information about the data being transmitted. When you ping the Linux VM from the Windows VM, you can select each ICMP packet in Wireshark to view the packet details pane. This pane shows you everything from the packet headers to the payload data, illustrating exactly what information is being sent with each ping request and response.
</p>
<br />
<p>
<img src="https://i.imgur.com/GLxSIG3.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
In the next part of the lab, we’ll set up a continuous ping from the Windows VM to the Linux VM. Using the command `ping -t [Linux_IP]`, we can initiate an ongoing ping that will continue indefinitely until manually stopped. While this ping is running, we’ll move to the Linux VM to configure its firewall settings and observe the impact.

### Steps:
1. **Start Continuous Ping**: On the Windows VM, open a command prompt and enter `ping -t [Linux_IP]`, where `[Linux_IP]` is the private IP address of the Linux VM. This will continuously send ICMP Echo Requests to the Linux VM.

2. **Block ICMP on Linux VM**: On the Linux VM, we’ll configure the firewall to block ICMP traffic. This can be achieved by creating a new **Network Security Group (NSG)** on Azure:
   - Go to the **Network Security Groups** section for the Linux VM in the Azure portal.
   - Create a new inbound security rule that denies ICMP traffic.
   - Save and apply the changes.

3. **Observe the Impact**: After setting the rule to block ICMP, you should notice that the continuous ping on the Windows VM no longer receives Echo Replies from the Linux VM. This indicates that ICMP traffic is effectively blocked.

4. **Re-allow ICMP Traffic**: To re-establish connectivity, go back to the **Network Security Group** settings for the Linux VM and modify the rule to allow ICMP traffic. Once saved, the Windows VM should start receiving Echo Replies again, confirming that ICMP traffic is permitted.

This step demonstrates how blocking and allowing ICMP traffic on the firewall impacts network connectivity and highlights the role of Network Security Groups in managing traffic flow between virtual machines.
</p>
<br />
<img src="https://i.imgur.com/5vXO75R.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<img src="https://i.imgur.com/Asl80tN.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
In the following part of the lab, we’ll establish an SSH connection from the Windows VM to the Linux VM to observe SSH traffic in Wireshark. SSH (Secure Shell) provides command-line access to remote machines, enabling secure data exchange, but it does not have a graphical interface.

### Steps:
1. **Set Wireshark Filter**: On the Windows VM, open Wireshark and set the filter to capture **SSH traffic only**. This will allow you to focus solely on packets related to the SSH connection.

2. **Establish SSH Connection**: Open a command prompt on the Windows VM and initiate an SSH session to the Linux VM using the following command:
   
   ssh labuser@10.0.0.5
   ```


3. **Observe SSH Packets in Wireshark**: Once the SSH session starts, you’ll see Wireshark capturing packets labeled with the SSH protocol. This allows you to monitor the encrypted communication between the Windows VM and Linux VM in real time.

Since SSH traffic is encrypted, Wireshark will show the packet headers and metadata but won’t be able to display the actual content of the commands entered. However, this demonstrates how SSH packets appear in network traffic and the security of SSH, as the command data is not visible.
</p>
<br />
<img src="https://i.imgur.com/zteR41r.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

Next, we’ll use Wireshark to observe DHCP traffic, which is used to dynamically assign IP addresses to devices on a network. DHCP operates over ports 67 and 68.

Steps:
Set Wireshark Filter: In Wireshark, set the filter to capture DHCP traffic only. You can use the filter udp.port == 67 || udp.port == 68 to focus on DHCP packets.

Request a New IP Address: On the Windows VM, open the command prompt and enter the command:

ipconfig /renew
This command requests a new IP address from the DHCP server, prompting DHCP packets to be exchanged between your machine and the server.

Observe DHCP Packets in Wireshark: After running the command, you should see DHCP packets appear in Wireshark. The packets typically include:

DHCP Discover: The client’s request for network information.
DHCP Offer: The server’s offer of an IP address and other settings.
DHCP Request: The client’s request to use the offered IP.
DHCP Acknowledgment: The server’s confirmation of the IP assignment.
This process highlights the role of DHCP in network configuration and demonstrates how Wireshark can capture the specific steps in IP address allocation.

</p>
<br />
<img src="https://i.imgur.com/vU8fpQf.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Time to filter DNS traffic. We will set wireshark to filter DNS traffic. We will initiate DNS traffic by typing in the command "nslookup www.google.com" this command essentially asks our DNS server what is google's IP address.
</p>
<br />
<img src="https://i.imgur.com/VMcwmsO.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Finally, let’s filter Wireshark to capture **RDP traffic**, which uses **TCP port 3389**. RDP allows us to connect to our virtual machine with a graphical interface, and once connected, it continuously sends data.

### Steps:
1. **Set the RDP Filter in Wireshark**: In Wireshark, type `tcp.port == 3389` in the filter bar to show only RDP traffic.

2. **Observe RDP Traffic**: You’ll notice Wireshark capturing a lot of RDP packets nonstop. This happens because RDP is constantly sending data to keep the remote session updated, including screen changes, mouse movements, and keyboard inputs.

3. **Understand the Traffic**: The large number of packets reflects how RDP continuously updates your remote session. 

This example shows how much traffic RDP generates and how Wireshark can capture it all in real time.
</p>
<br />
<img src="https://i.imgur.com/VxXGv6X.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
