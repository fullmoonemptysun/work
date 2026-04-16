# **Chapter 1: Intro to Distributed Service Oriented Computing**

  

![[CSE445_M1_L1_Architecture.pdf]]

  

1. Multitenancy/loadbalancers: Tenants are just users, organizations that use the same instance of a server software. Loadbalancers are A **Load Balancer** is a system or device that distributes network or application traffic across multiple servers or resources to ensure that no single server becomes overwhelmed with traffic. It helps in achieving **scalability**, **availability**, and **fault tolerance**.
2. Architectures discussed:
    1. Pipeline
    2. Blackboard
    3. 2 tier architecture: Client + Server
    4. Thin client
    5. Thick client
    6. 3 tier
    7. 4 tier service based
    8. 4 tier java

  

  
  
  

![[CSE445_M1_L2_Distributed_Object_Architecture.pdf]]

  

1. IDLs: The **IDL** defines the **interface** that the client can use to call the server's functions, and it doesn't matter what programming language the client or server is written in. Here's how it works:

- The **IDL** acts like a **contract** that specifies which **functions** are available on the server, the **parameters** they accept, and the **return type**.
- The **client** can then call these functions by name (as defined in the IDL) without needing to know the details of how they are implemented on the server side or what language the server is written in.
    
    The IDL essentially provides a **common language** or **understanding** for both the client and the server, allowing them to communicate even if they’re in different programming languages.
    

  

1. Stub?? it is a piece of code that is generated for a client according to the IDL, that acts as proxy for a function call on the server side.

```C
//IDL
interface Calculator {
  long add(in long a, in long b);
};

//STUB
public class CalculatorStub {
    public long add(long a, long b) {
        // Code to send the request to the remote server, receive the response, and return it.
    }
}
```

  

> [!important]
> 
> 1. Description of how Distributed object arch works with ORBs
> 
> ### 1. **ORB (Object Request Broker)**:
> 
> - **ORB** is responsible for handling communications between distributed objects in a network. It acts as a middleware that manages the interaction between clients (calling objects) and servers (called objects) in a distributed system.
> 
> **Example**:
> 
> - You have a **client** (calling object) and a **server** (providing the service) running on different machines.
> - The **ORB** facilitates communication between these two distributed objects.
> 
> ### 2. **Service Provider & Stubs**:
> 
> - A **service provider** (server) exposes its services to other objects (clients) through **stubs**. The **stub** is a proxy for the remote object, allowing the client to call methods as though they are local methods.
> 
> **Example**:
> 
> - The **service provider** (server) implements a service called `add` (e.g., summing two numbers).
> - The **stub** is generated from the **IDL** and is used by the **client** to interact with the service.
> 
> ### 3. **Binding the IDL Stub**:
> 
> - The **client** binds to the **IDL stub** that defines the interface of the service it wants to call. The **IDL stub** provides a way for the client to make remote calls as if they were local.
> 
> **Example**:
> 
> - The client has the **stub** that provides a method `add(long a, long b)`. It doesn't need to worry about how this method is executed on the server side. The stub manages communication with the ORB.
> 
> ### 4. **ORB Handling the Call**:
> 
> - When the **client** calls the method on the stub, it doesn't directly call the service on the server. Instead, the call goes to the **ORB**.
> - The **ORB** handles all the network communication, invoking the required method on the actual server-side object (service provider) via a **published IDL skeleton**.
> 
> **Example**:
> 
> - The client calls `add(5, 3)` on the stub.
> - The call is forwarded by the **ORB** to the server, which executes the method and returns the result.
> 
> ### 5. **IDL Skeleton**:
> 
> - The **IDL skeleton** is the counterpart to the **stub** on the server side. It links the remote method invocation from the client to the actual service implementation on the server.
> 
> **Example**:
> 
> - The **IDL skeleton** on the server listens for the request from the **ORB** and calls the real implementation of the `add` method.
> - The server executes the method and sends the result back via the **ORB** to the **client**.

## What is Enterprise Service Bus?

The enterprise service bus (ESB) is a software  
architectural pattern that supports real-time data exchange between  
disparate applications. Large organizations have multiple applications  
that perform various functions using diverse data models, protocols, and  
security restrictions. The ESB makes application integration easier by  
performing operations like data transformation, protocol conversion,  
message routing. Applications pass relevant data to the ESB, and it  
converts and forwards the data to other applications that need it.  

  

  

  

![[CSE445_M1_L3_Design_Patterns.pdf]]

  

![[CSE445_M1_L4_SOA_Concepts.pdf]]

### **How the System Works**

1. **Organizations (X, Y, Z) register their components** with the **Service Broker**.
2. The **Service Broker maintains a directory** of available services.
3. When an **application needs a service**, it searches for it using the **broker**.
4. The **broker finds the matching service** and returns a reference.
5. The **application uses a proxy** to interact with the service without directly knowing its provider.

---

### **4. Benefits of This Approach**

✔ **Loose Coupling** → The application doesn't directly depend on any specific service provider.

✔ **Reusability** → Services from different organizations can be reused across multiple applications.

✔ **Scalability** → New services can be added dynamically without modifying existing applications.

✔ **Flexibility** → Services can be swapped out without affecting the core application.

## 1.1 Computer Architecture and Paradigms

### **1.1.1 Computer Architecture:**

1. Majorly of 4 types:
    
    1. Single Instruction and Single data (SISD): Single simple processor SYSTEMS (older single core processors, intel 8086
    2. Single Instruction and Multiple Data streams (SIMD): Vector processors-GPUs, where a single operation processes many pixels simultaneously.
    3. Multiple instructions on the same data strem (MISD): Fault tolerant systems that do redundant computations (rare)
    4. Multi Instuction stream Multi Data stream MIMD: Systems with standalone computer systems with their own memory and control, ALU and I/O, modern multi core CPUs (i7)
    
      
    
2. **MIMD** architecture are distributed systems.
3. **Distrubuted Computing Idea:** Expression of computing in a parallel or distributed manner.
4. **Dist. software architecture vs. Network Architecture:**
    1. Dist. Software arch deals with the organization and interface of the different software components
    2. Network Architecture studies the connectivity of network nodes, etc. (protocols, data packets)
    3. Distributed systems have coherent OS while a seto of network nodes has independent operation systems.

  

![[image 59.png|image 59.png]]

  

### 1.1.2: Software Architecture (early design decisions)

1. Structure that comprises software components their properties and relationships between them.
2. A model for the software being created
3. Must happen before design of algorithms and actual implementations.
4. Main focus: **Service oriented architecture (SOA)**
5. **SOA** has 3 parties: Service providers, service brokers, and service requesters.
6. Coding and implementation is done by the parties independently however by these 3.

  

### 1.1.3: Computing Paradigms:(from cse240):

1. **Imperative/Procedural:** C (stored program concept)
2. **Object oriented**: Java, C++ (objects)
3. **Functional**: Scheme/Lisp (computation in terms of functions (no memory))
4. **Logical**: Prolog
5. **Component Based Computing:** Large applications based on preprogrammed, precompiled program modules/units. Just link and execute (OOP is actually this)
6. CBC can have any components however, which makes the difference between OOP and CBC. Components don’t only need to be classes, they can be standalone applications themselves and Even Services and thus, **SOC is also CBC.**
7. **Distributed Computing:** Computations executed on more than one logical or physical processor or computer become one integral aplication. Dist. units can differ and have variations. Important concerns: Concurrency, concurrent computing, resource sharing, synchronization, communication.
8. Example: **Multithreading** is a common DC technique to execute different function concurrently. If the units are at the object level, it is **OO distributed computing.**
9. Well Known distributed OO computing frameworks: **CORBA(Common object request broker arch)** and **DCOM(dist. component object model)**
10. **Service Oriented Computing: another DC paradigm**:
    - Different from Dist. OO Computing
    - Dist. Services (possibly service data) instead of dist. objects
    - development duties divided: service provision, service brokerage, service consumpiton.
    - reusable services in repositories for matching, discovery and access
    - communication through open standards and protocols that are platform independent.

  

  

## 1.2: Distributed Computing and Distributed Software Architecture

![[image 1 11.png|image 1 11.png]]

### 1.2.1: Distributed Computing

1. There are many Challenges when it comes to distributed computing: Complexity, communication, connectivity, security, reliability, manageability, and unpredicability.
2. **8 Fallacies of distributed computing:**
    1. The network is reliable.
    2. Latency is 0.
    3. Bandwidth is infinite.
    4. The network is secure.
    5. Topology does not change.
    6. There is one administrator.
    7. Transport cost is 0.
    8. The network is homogenous.

  

Tangent: OSI Model for networks

![[osi_model_7_layers.png]]

### 1.2.2: N-tier Architecture

1. Like the osi model, dist. SDE often has a layered structure. This refers to **N-tier Architecture**

  

  

## Software Architectures:

> [!important]
> 
> ### **Pipeline (Pipe-and-Filter) Architecture - Short Notes**
> 
> 🔹 **Concept**:
> 
> - Data flows through a sequence of **filters** (processing components) connected by **pipes** (communication channels).
> - Each filter processes input, transforms it, and sends output to the next filter.
> 
> 🔹 **Key Features**:
> 
> ✔ **Sequential processing**: Data moves step-by-step.
> 
> ✔ **Modular**: Easy to add, remove, or modify filters.
> 
> ✔ **Independent components**: Filters work independently.
> 
> 🔹 **Real-World Applications**:
> 
> ✅ **Unix Pipelines** – e.g., `cat file.txt | grep "error" | sort | uniq > output.txt`
> 
> ✅ **Compilers** – Lexical → Syntax → Semantic → Optimization → Code Generation
> 
> ✅ **ETL Pipelines (Big Data)** – Extract → Transform → Load
> 
> ✅ **Web Middleware (Express.js)** – Requests flow through multiple middleware functions
> 
> 🔹 **Pros**:
> 
> ✔ Easy to maintain and extend
> 
> ✔ Reusable components
> 
> ✔ Supports parallelism
> 
> 🔹 **Cons**:
> 
> ❌ Can introduce latency if too many stages
> 
> ❌ Not efficient for complex interactions between filters

> [!important]
> 
> ### **Blackboard Architecture - Short Notes**
> 
> 🔹 **Concept**:
> 
> - A **central blackboard** stores data and evolving solutions.
> - Independent **modules (knowledge sources)** read and write to the blackboard.
> - No direct communication between modules—only via the blackboard.
> 
> 🔹 **Key Components**:
> 
> 1️⃣ **Blackboard** – Shared knowledge base.
> 
> 2️⃣ **Knowledge Sources** – Independent processing units.
> 
> 3️⃣ **Controller (optional)** – Decides which module updates the blackboard next.
> 
> 🔹 **Real-World Applications**:
> 
> ✅ **Speech Recognition** – Processes audio step-by-step (phonemes → words → meaning).
> 
> ✅ **AI & Expert Systems** – Used in medical diagnosis & machine learning.
> 
> ✅ **Autonomous Robots** – Sensors (vision, GPS) update a shared system.
> 
> ✅ **Air Traffic Control** – Radar, weather, and flight data contribute to a common system.
> 
> 🔹 **Pros**:
> 
> ✔ **Modular & Scalable** – Easy to add/remove components.
> 
> ✔ **Handles Complex Problems** – Useful for uncertain/incomplete data.
> 
> ✔ **Encourages Parallel Processing** – Multiple modules can work simultaneously.
> 
> 🔹 **Cons**:
> 
> ❌ **Performance Bottleneck** – Central blackboard can slow down processing.
> 
> ❌ **Overhead** – Modules must constantly monitor and update the blackboard.