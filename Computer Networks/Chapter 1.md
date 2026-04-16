# What is the Internet?

## A nuts and bolts definition
1. Computer network that connects billions of computing devices throughout the world.

2. Computer networks are not limited to "Computer" devices anymore.

3. Connected devices are called **hosts** or **end systems**. 
![[Pasted image 20250823011207.png]]

4. End systems are connected by **Communication links** and **Packet switches**.
5. Communication links are of many types, made up of different types of physical media: cable, copper wire, optical fiber, and even the radio spectrum. 

6. Different types of links transmit data at different rates
7. **Transmission rate** is measured in *bits/second*.
8. When one end system sends data to another end system, it segments that data and adds header bytes to each segment. 

9. The resulting packages are known as **Packets**, which are sent to the destination end system through the network, where they are reassembled into the original data.

10. A *packet switch* takes a packet that comes on its incoming comm link and forwards that packet on one of its outgoing comm. links.
11. These switches are of many types but 2 main ones are:
	1. **Routers**
	2. **Link layer switches**
12. Both of these forward packets toward their ultimate destinations.

13. Link layer switches are used in access networks while routers are mainly used in the network core.
	(**Access networks:** is the network subset that is closer to end users (PCs, Mobile phones, IOT, etc.)
	(**Network cores**: is the backbone part of the network where high speed data travels between different sub nets or even ISPs)

14. Sequence of comm links and packet switches traversed by a packet from sender to receiver is called a **Route or Path** through the network.

15. Packet switched networks (which transport packets), are analogous to roadway transportation (highways, roads, intersections.). 

### Example
A factory needs to move a large amount of cargo to some destination warehouse located thousands of kilometers away. At the factory, the cargo is segmented and loaded into a fleet of trucks. Each of the trucks then independently travels through the network of highways, roads, and intersections to the destination warehouse. At the destination warehouse, the cargo is unloaded and grouped with the rest of the cargo arriving from the same shipment. Thus, in many ways, packets are analogous to trucks, communication links are analogous to highways and roads, packet switches are analogous to intersections, and end systems are analogous to buildings. Just as a truck takes a path through the transportation network, a packet takes a path through a computer network.


16. End systems access internet through ISPs.
17. Each ISP in itself is a network of packet switches and comm links.
18. ISPs provide many types of network access to the end systems including residential broadband access (cable, modem) or DSL, high speed LAN access, and mobile wireless access. They also provide internet access to content providers, so web sites and video servers are connected directly to the internet.

19. These ISPs need to be interconnected as well. 
20. Low tier ISPs are interconnected through national and international upper-tier ISPs such as Level 3 communications, AT&T, Sprint, and NTT.

21. Upper tier ISP consists of high speed routers connected with high speed fiber optics links. 

22. Each of these ISPs (both lowe and upper tier), are managed independently, runs the IP protocol, and conforms to certain naming and address conventions. 

23. End systems, packet switches and other pieces of the internet operate on **protocols** that control the sending and receiving of data.

24. The 2 most important protocols in the internet are:
	1. **TCP** Transmission control protocol
	2. **IP** Internet Protocol

25. The IP protocol specifies the format of the packets that are sent and received among routers and end systems. 
26. These principle protocols are collectively known as *TCP/IP*.

27. Standards are what protocols people agree on.
28. Internet standards are specified by the *Internet Engineering Task Force* IETF.

29. IETF standards documents are called requests for comments (RFCs). 
30. These **RFCs** are technical documents that define protocols such as TCP, IP, HTTP, and SMTP for email. There are currently more than 7000 RFCs. 

31. There are other bodies that specify standards for other network components (e.g, **IEEE802 LAN/MAN** specifies the Ethernet and wifi standards.)


## A Services Definition
1. We can also look at the internet as an infrastructure that provides services to applications. 
2. This includes all the applications we see today (mobile apps, etc.)

3. Applications are said to be **distributed applications**, since they involve multiple end systems that exchange data with each other.

4. These applications run on end systems and not in the packet switches in the network core. 

5. Programs running on one end system needs to instruct the internet to send data to a program running on a different end system.

6. End systems attached to the internet provide a **socket interface** to the programs running on it. This interface is a set of rules that the sending program must follow so that the internet can deliver the data to the destination program.

*Analogy Example:*
Suppose Alice wants to send a letter to Bob using the postal service. Alice, of course, can’t just write the letter (the data) and drop the letter out her window. Instead, the postal service requires that Alice put the letter in an envelope; write Bob’s full name, address, and zip code in the center of the envelope; seal the envelope; put a stamp in the upper-right-hand corner of the envelope; and finally, drop the envelope into an official postal service mailbox. Thus, the postal service has its own “postal service interface,” or set of rules, that Alice must follow to have the postal service deliver her letter to Bob. In a similar manner, the Internet has a socket interface that the program sending data must follow to have the Internet deliver the data to the program that will receive the data

The postal service, of course, provides more than one service to its customers. It provides express delivery, reception confirmation, ordinary use, and many more services. In a similar manner, the Internet provides multiple services to its applications. When you develop an Internet application, you too must choose one of the Internet’s services for your application. We’ll describe the Internet’s services in



## What is a protocol?
1. A protocol for computer networking is analogous to a protocol in normal human conversation (initialization (hi, hi)) and then *there are specific messages we send, and specific actions we take in response to the received reply messages or other events (such as no reply within some given amount of time).*

![[Pasted image 20250909172024.png]]

2. Similar to human interaction, it takes 2 end systems running the same communication protocol in order to accomplish a task.

### Network Protocols
1. Similar to human protocol but the entities taking actions are hardware and software components of a device.

2. All activity bw 2 or more systems that are remote is governed by a protocol.

3. For example:
- *hardware-implemented protocols* in two physically connected computers control the flow of bits on the “wire” between the two network interface cards
- *congestion-control protocols* in end systems control the rate at which packets are transmitted between sender and receiver
- *protocols in routers* determine a packet’s path from source to destination.

>[!Important] 
>"A protocol defines the format and the order of messages exchanged between two or more communicating entities, as well as the actions taken on the transmission and/or receipt of a message or other event."

# 1.2 The network Edge
1. The Internet’s end systems include desktop computers (e.g., desktop PCs, Macs, and Linux boxes), servers (e.g., Web and e-mail servers), and mobile devices (e.g., laptops, smartphones, and tablets) and more non traditional IOT devices are being attached to the internet.

2. These end systems are also called *hosts* as mentioned previously because they host, or run web programs such as a server program, email client program or an email service or a browser, etc.

3. Hosts are divided into 2 categories:
	- clients: desktop, mobile, pcs, smartphones
	- servers: more powerful machines that store and distribute web pages, stream video, relay email, etc.

4. Google has 50-100 data centers, including about 15 large centers, each with more than 100,000 servers.

## Access Networks
1. After looking at end systems, the first thing is the access network the network that physically connects an end system to the first router **Edge router**. 

### Home Access: DSL, CABLE, FTTH, DIAL up and Satellite
1. Two most common home broadband access are:
- **Digital Subscriber Line (DSL)**
- and **Cable**

#### **DSL**
1.  DSL is usually provided by the same local telephone company that provides its wired local phone access. So when DSL is used, user's telco is also his ISP

![[Pasted image 20250909182720.png]]

- Each customer's DSL modem uses the existing telephone line (twisted pari, copper wire), to exchange data with a DSLAM (DSL Access Multiplexer) located in the telco's local central office (CO). 
- The home's DSL modem takes digital data and translates it to high frequency signals for transmission over telephone wires to the CO. These analog signals from many such houses are translated back into digital format at the DSLAM.

2. The telephone line carries both data and traditional telephone signals simultaneously which are encoded at different frequencies:
   - A high speed downstream channel: 50 kHz to 1 MHz band
   - A medium speed upstream channel: 4 kHz to 50 KHz band
   - A 2 way telephone channel in the 0 to 4 KHz band.

2. This makes a DSL link look like it is 3 different links so that a telephone call and an interenet connection can share the dsl link at the same time.

>[!important]
>### 1. What a filter **really is**
>A filter is an **electrical circuit** made of components like **resistors (R), capacitors (C), and inductors (L)**. These components respond differently to different frequencies of voltage and current:
>
>- **Capacitor:** Passes high frequencies easily, blocks low frequencies.
   > 
>- **Inductor:** Passes low frequencies easily, blocks high frequencies.
   > 
>- **Resistors:** Control how the signal is divided or attenuated.
>By combining these, engineers create **low-pass, high-pass, and band-pass filters**.

3. This filter helps separate the frequencies.
4. On the home side, splitter splits the data and telephone signals and forwards data sig to the modem. This is done by the DSLAM on the CO side.

5. Single DSLAM connects to 100-1000 houses.

6. The DSL standards define multiple transmission rates (12mbps ↓/1.8mbps ↑) and (55mbps ↓/ 15mbps ↑). 
7. Because the two direction has different speeds, access is said to be asymmetric.

8. Actual trans rates may differ depending on the provider limiting a residential rate (different prices, etc.).
9. Distance also limits max rate.
10. The gauge of the twisted pair line also limits max rate.
11. Electrical interference also limits max rate. 

12. DSL is useless outside the range of 5 - 10 miles.

#### **Cable**

1. Cable uses existing television line instead of telephone
![[Pasted image 20250909200311.png]]
Fiber optics connect cable head end to neighborhood junctions from which coaxial cables come out and connect homes. Also called hybrid fiber coax (HFC).

2. Special Modem is used called cable modem, similar to DSLAM, CMTS is used to convert analog signals to digital at the cable end. 
3. Modem splits the network into 2 channels a down stream and upstream, access is asymmetric also.
4. Same as DSL, max rate may not be achieved either due to manual limits or media impairments.

5. The upstream and downstream are shared among the users, cable is a shared broadcast medium. Anything that goes downstream from CMTS goes to all links and all up stream transmissions come on the same link to CMTS, so if too many users, rate will slow down.

6. A new method called **Fiber to the Home (FTTH)** is now replacing the above two technologies. Basically an optical fiber path from the CO directly to the home. 

7. These fiber come as close as possible to the homes and then split. There are two types of optical distribute network architectures that perform this splitting:
- Active optical networks (AONs) (Basically switched ethernet)
- Passive optical networks (PONs) (used in Verizon's FIOS service)


