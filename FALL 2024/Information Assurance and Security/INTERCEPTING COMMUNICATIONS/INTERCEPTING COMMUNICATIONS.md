1. Mail goes through multiple intermediate steps to finally reach destination

![[image 24.png|image 24.png]]

1. It can be intercepted in any of these intermediate transfers

  

1. We need to know how the mailing protocol work

  

1. Changing the destination address:
    
    ![[image 1 5.png|image 1 5.png]]
    

  

1. This mail example translates to TCP/IP networking

![[image 2 6.png|image 2 6.png]]

Things change in the large network which is why it is called dynamic.

![[image 3 6.png|image 3 6.png]]

  

  

1. MAC addresses describe the physical location of machines on the ethernet layer:  
      
    

![[image 4 4.png|image 4 4.png]]

  

[](https://www.notion.soundefined)

  

1. Data goes from host A to B as follows:
    - eith0 interface
    - vethA
    - bridge0
    - vethB (these are all virtual ports)
    - Bridge knows how to divert packets

![[image 5 4.png|image 5 4.png]]

  

  

1. Ethernet Packet structure:
    
    ![[image 6 4.png|image 6 4.png]]
    

  

1. Broadcast on all ports is possible
    
    ![[image 7 3.png|image 7 3.png]]
    

  

1. We won’t be able to use MAC addresses in a bigger network with thousands and thousands of machines. This is when _**IP routing scheme**_ comes in. (Mail going from one postal office to another to another to another and finally at a home)

  

1. For IP the type bytes are : 08 00
    
    ![[image 8 3.png|image 8 3.png]]
    

![[image 9 3.png|image 9 3.png]]

  

  

  

1. We are slowly building on top of each layer. The red is the ethernet layer, the yellow is IP and now the blue is TCP packet structure:
    
      
    
    ![[image 10 3.png|image 10 3.png]]
    

  

- Ethernet for directly linked devices
- IP for routing capabilities going to a more complex network of hosts
- TCP enables a stateful interaction between two hosts. Enables a concept of order that the data comes in. Handles the ports.
- Sequence number: used for ordering, declare where in the conversation we are currently
- Acknowledgement number: I understand that this much has been sent to me.
- Data offset: after this the data starts, header data
- Reserved: unused as of now
- Flags: operations in TCP, declare different operations related to the sequence numbers, acknowedgement etc.
- Window Size: when you send me data, dont send me bigger than this size.
- Checksum: Verifies no data was corrupted during transmission by checking everything
- Urgent pointer: What part of data is very urgent
- ?? Options: timestamp, etc.

  

1. Flags:

![[image 11 3.png|image 11 3.png]]

  

we want to do this:

![[image 12 3.png|image 12 3.png]]

1. TCP handshake:

![[image 13 3.png|image 13 3.png]]

- need to synch sequence numbers, acknowledgement number

![[image 14 3.png|image 14 3.png]]

- A sends an initial TCP providing its Initial sequence number (ISN) SYN

  

![[image 15 3.png|image 15 3.png]]

- B responds, with It’s sequence number and acknowledges A’s acknolwdge number(A + 1)

![[image 16 2.png|image 16 2.png]]

- A gets back with acknowledging both the numbers. (seq = A + 1).

  

- This is a handshake completed.

  

- Now we can send data
    
    ![[image 17 2.png|image 17 2.png]]
    

  

- B will acknowledge the data received:
    
    ![[image 18 2.png|image 18 2.png]]
    

- 13 bytes of data received so A + 14
- This could have been 10 if the data got split

  

![[image 19 2.png|image 19 2.png]]

- PSH tells B to start working on the buffered data ASAP and not wait for more data ( not important)

  

- Final goodbye

![[image 20 2.png|image 20 2.png]]

![[image 21 2.png|image 21 2.png]]

- B acknowledges
- A will acknowedge final:
    
    ![[image 22 2.png|image 22 2.png]]
    

  

  

  

## Adress Resolution Protocol:

  

1. Used to make sense of the ethernet layer MAC address on an IP connection

![[image 23 2.png|image 23 2.png]]

1. ARP is done before every initial connection to every machine even our routers.

![[image 24 2.png|image 24 2.png]]

  

**SYN flag:**

The purpose of the SYN flag is to:

1. **Synchronize sequence numbers**: When the client sends a SYN packet, it includes an initial sequence number (ISN). This sequence number is used to keep track of the order of bytes transmitted over the connection.
2. **Initiate the handshake**: The SYN flag is the first step of the three-way handshake, a process used to establish a reliable connection. The full process works as follows:
    - **Step 1**: The client sends a SYN packet to the server.
    - **Step 2**: The server responds with a SYN-ACK packet, acknowledging the client's SYN and sending its own SYN to establish its own sequence numbers.
    - **Step 3**: The client responds with an ACK (acknowledgment) packet, completing the handshake and establishing the connection.

  

### **How connecting to the internet works:**

1. **Scanning**: Your device scans for Wi-Fi networks (SSIDs).
2. **Selecting**: You choose a network.
3. **Authentication**: (If required) Your device provides a password to join a secured network.
4. **Association**: Your device associates with the access point.
5. **DHCP**: Your device obtains an IP address.
6. **4-Way Handshake**: Encryption keys are exchanged (for secure networks).
7. **ARP**: The device learns the MAC address of the router.
8. **Connected**: Your device is now connected to the network, and possibly the internet.

  

### How a wifi network can be replicated:

Yes, in theory, if we can replicate Wi-Fi signals and send beacon frames with the right structure, we could trick a device into recognizing them as a Wi-Fi network. However, this is **complex** and requires specialized hardware like Software Defined Radios (SDRs), a deep understanding of Wi-Fi protocols, and handling the technical challenges of frame construction and signal quality.

Be mindful that replicating or spoofing Wi-Fi signals without authorization can be illegal, as it interferes with legitimate communications. It’s important to study these techniques in controlled environments (like penetration testing labs) where it is ethical and legal to explore the vulnerabilities of Wi-Fi systems.

  

**Modulation and SSID:**

- **Modulation**: This is how the Wi-Fi signal (radio waves) is altered to carry data. Your data is turned into changes in amplitude, frequency, or phase of the wave.
- **Modulation Scheme**: The specific technique Wi-Fi uses to modulate the signal, like OFDM or DSSS, which affects how efficiently data is transmitted.
- **SSID**: The network name that’s transmitted in beacon frames over the modulated radio signal, allowing devices to identify the Wi-Fi network they might want to connect to.

  

### Virtual Network Summary

1. **Definition**: A virtual network is a logical network that exists on top of a physical network, created using software to simulate traditional network behavior.
2. **Key Characteristics**:
    - **Isolation**: Segments different environments for security and testing.
    - **Flexibility**: Easily created, modified, or deleted without hardware changes.
    - **Logical Configuration**: Includes virtual switches, routers, and firewalls.
3. **Types of Virtual Networks**:
    - **VPNs (Virtual Private Networks)**: Secure remote access to private networks.
    - **VLANs (Virtual Local Area Networks)**: Segregate traffic within the same physical network.
    - **SDN (Software-Defined Networking)**: Software-based control of network hardware.
    - **Cloud Virtual Networks**: Isolated environments for cloud resources.
4. **Cybersecurity Use Cases**:
    - **Simulate Attacks**: Test security measures in a controlled environment.
    - **Segmentation**: Isolate critical resources to enhance security.
    - **Testing Environments**: Safely test new configurations or applications.

  

## Challenge Notes:

1. Level 5: We use wireshark to monitor network traffic. It shows all the packets that are being sent/received with the data in them.

  

1. Level 6: IMPORTANT. GET AN EXPLAINATION.
2. Level 7: Change your IP to where info is being sent then listen with nc. Todo:
    1. **Research why there cannot be two /16 ip routes and what that even mean:**
        
        1. **/16**:
            - This means that the first **16 bits** (or the first two numbers) are used for the **network** part.
            - Example: For `192.168.0.0/16`, the network is `192.168`, and you can have a lot of devices (up to **65,536** addresses) within this network, from `192.168.0.0` to `192.168.255.255`.
        2. **/24**:
            - This means that the first **24 bits** (or the first three numbers) are for the **network** part.
            - Example: For `192.168.1.0/24`, the network is `192.168.1`, and you can have up to **256** addresses (from `192.168.1.0` to `192.168.1.255`) in this network.
        3. **/32**:
            - This means that all **32 bits** (the entire address) are for one specific device.
            - Example: `192.168.1.1/32` refers to a single IP address, which means it’s just for one device, like your computer or a server.
        4. The question can be answered as: it creates a conflict in the routing table because when you add an IP address to an interface, it automatically sets up a route for that subnet. For example:
        
        - Adding `**10.0.0.3/16**` to an interface will create a route like:
            
            ```Plain
            10.0.0.0/16
            ```
            
        
        ### Summary
        
        - **/16**: Big network, many devices (up to 65,536).
        - **/24**: Smaller network, fewer devices (up to 256).
        - **/32**: Just one device, very specific.
        
          
        

  

1. The `ip link show` command displays the status of network interfaces in Linux. Key points:

- `**eth0**`: First Ethernet interface (others may be `eth1`, etc.).
- **Flags**: Indicate interface capabilities and status, like:
    - `**UP**`: Interface is active.
    - `**BROADCAST**`: Supports broadcast communication.
    - `**MULTICAST**`: Supports multicast.
    - `**LOWER_UP**`: Physical link is up.
- `**mtu 1500**`: Maximum packet size for the interface (default is 1500 bytes).
- `**MAC address**`: Hardware address (shown as `link/ether`).
- `**state UP**`: Interface is operational.
- **Other interfaces**: Includes `lo` (loopback interface) for internal communication.