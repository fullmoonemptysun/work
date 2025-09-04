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

5. 