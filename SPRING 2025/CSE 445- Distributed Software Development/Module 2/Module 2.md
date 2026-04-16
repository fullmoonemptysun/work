![[CSE445_M2L1_SOC_Standards.pdf]]

  

![[CSE445_M2L2_SOA_Software_Development.pdf]]

> [!important]
> 
> ### **Step-by-Step Explanation of Web Service Creation in C#**
> 
> 1. **Choose a Web Service Template**
>     - You decide what type of web service you want to create.
>     - **WCF (Windows Communication Foundation)**: Used for SOAP-based services.
>     - **WF (Windows Workflow Foundation)**: Used for building workflow-based services.
>     - **ASP.NET Web API**: Used for RESTful services.
> 2. **Define an Ordinary Class with Data Members and Methods in C#**
>     - You create a **C# class** that contains methods that you want to expose as web services.
>     - Example:
>         
>         ```C#
>         public class Calculator
>         {
>             public int Add(int a, int b)
>             {
>                 return a + b;
>             }
>         }
>         ```
>         
> 3. **Choose Methods to be Remotable by Adding** `**[OperationContract]**`
> 
> - To expose a method as a web service, you **annotate it with attributes** like `[OperationContract]` inside an interface that is marked with `[ServiceContract]`.
> - Example:
>     
>     ```C#
>     [ServiceContract]
>     public interface ICalculator
>     {
>         [OperationContract]
>         int Add(int a, int b);
>     }
>     ```
>     
> 
> 1. **Compile and Run the Class, the Service will be Generated**
> 
> - When you compile and host the service (e.g., using IIS or a self-hosted WCF service), it becomes accessible over the network.
> 
> 1. **WSDL File and/or URL of the Web Service will be Generated**
>     - WSDL (Web Services Description Language) is an XML file that describes the web service (methods, parameters, return types).
>     - The URL typically looks like this:
>         
>         ```Plain
>         http://localhost:8080/CalculatorService?wsdl
>         ```
>         
> 2. **SOAP/HTTP Call Interface will be Generated**
> 
> - SOAP (XML-based protocol) defines how clients should send requests and receive responses.
> 
> 1. **There is Little Difference from Writing a Regular C# Class**
>     - The main difference is that the methods are now **remotely accessible** over the internet instead of being just local functions.
> 
>   
>   
> 
> ### **What Does "Wrapping Something in a Web Service" Mean?**
> 
> "Wrapping" a function or class in a **web service** means making it accessible over a network using standard communication protocols like **SOAP or REST**.
> 
> For example:
> 
> - You have a method `Add(a, b)` in a regular C# class.
> - You "wrap" it in a web service using WCF.
> - Now, **external applications can call** `**Add(a, b)**` **over HTTP** instead of running the function locally.

  

> [!important]
> 
> ### **1. SOAP vs. REST**
> 
> - **SOAP**: A **messaging protocol** that defines **message structure**, **processing rules**, and features like **error handling** and **security**. Typically uses **XML** for messages.
> - **REST**: A **data transfer model** that is **stateless**, **lightweight**, and uses **HTTP** for communication. Messages are typically **JSON**.
> 
> ### **2. SOAP Basics**
> 
> - **SOAP** defines:
>     - Message format (XML).
>     - Rules for processing (e.g., WS-Security).
>     - Error handling (SOAP faults).
>     - **Not** responsible for transport (uses HTTP, SMTP, TCP, etc. as transport protocols).
> 
> ### **3. SOAP Over HTTP**
> 
> - **HTTP** is used as the **transport protocol** for sending SOAP messages.
> - SOAP message is embedded in the **HTTP request** body.
> - HTTP headers (like `Content-Type`, `SOAPAction`) define the request’s metadata.
> 
> ### **4. SOAP Over TCP**
> 
> - **TCP** is a **low-level transport protocol** that ensures reliable data transmission.
> - SOAP messages are sent **directly** over the TCP connection without the HTTP overhead.
> - Useful for **high-performance** or **backend-to-backend** communication.
> 
> ### **5. SOAP vs. JSON over HTTP (REST)**
> 
> - **SOAP**: A **protocol** with strict rules for message structure and processing, typically using **XML**.
> - **JSON over HTTP** (REST): A **data format** (JSON) over **HTTP**, with no strict rules about structure or processing.

**WCF is a high-level abstraction tool** that generalizes and simplifies network communication, so developers **don’t have to deal with low-level networking details** like sockets, serialization, transport protocols, or security configurations manually.

### **How WCF Abstracts Low-Level Networking**

WCF lets you **define a service like a normal C# class**, and it automatically:

✔ Handles **network communication** (no need for raw TCP sockets).

✔ Converts **method calls into remote messages** (no need for manual serialization).

✔ Supports **multiple transport protocols** (HTTP, TCP, MSMQ, Named Pipes, etc.).

✔ Manages **security and authentication** (SSL, encryption, identity verification).

✔ Ensures **reliable messaging** (transactions, retries, failovers).

  

> [!important]
> 
> ### **MIME (Multipurpose Internet Mail Extensions) - Concise Notes**
> 
> ### **1. What is MIME?**
> 
> - A standard for identifying and handling different content types in **emails** and **HTTP communication**.
> - It is **metadata (header information)** that tells software how to process received data.
> 
> ### **2. What Does MIME Do?**
> 
> - **Labels content types** so applications know how to handle them.
> - **Enables attachments** in emails by encoding binary files (e.g., images, PDFs).
> - **Supports multiple content parts** (e.g., text + images in an email).
> - **Standardizes web content handling** via `Content-Type` in HTTP headers.
> 
> ### **3. MIME Type Structure**
> 
> - Format: `type/subtype`
> - **Examples:**
>     - `text/html` → HTML document
>     - `image/jpeg` → JPEG image
>     - `application/json` → JSON response
>     - `video/mp4` → MP4 video
> 
> ### **4. MIME in Action**
> 
> - **Email**: Allows attachments via `multipart/mixed`.
> - **Web (HTTP)**: Used in `Content-Type` headers to tell browsers how to handle content.
> 
> ### **5. Key Takeaways**
> 
> ✔ **MIME is just metadata**—it doesn’t transport data.
> 
> ✔ Helps browsers, email clients, and servers **correctly interpret** different file types.
> 
> ✔ Essential for **email attachments, web content handling, and APIs**.

> [!important]
> 
> ### **Hosting Services on a Server**
> 
> - **Purpose**: This refers to a business having **its own infrastructure** (or renting infrastructure through the cloud) where it **runs its applications**, processes data, and stores its files.
> - **What it involves**:
>     - **Applications**: The business hosts its **internal or customer-facing applications**, such as an **e-commerce platform**, **CRM**, or **ERP system**.
>     - **Data Storage**: The business stores its data (e.g., customer records, transactions) in its own **database** on its server.
>     - **Computing**: All business logic, user interactions, and computational tasks are handled by the business's **server** or **cloud infrastructure**.
> 
> **Example**: A business hosts its website and an online shopping application on a cloud server, handles customer transactions, and stores orders in a database.
> 
> ---
> 
> ### **ebXML Registry**
> 
> - **Purpose**: The **ebXML registry** is a **centralized directory** where businesses store and manage **standardized B2B information**, such as **business profiles, process definitions, document schemas**, and **trading partner details**. It’s designed to facilitate **interoperability** between different companies’ systems.
> - **What it involves**:
>     - **Registration**: A business registers **metadata** about its processes, services, document formats (e.g., invoices, purchase orders), and partners.
>     - **Discovery**: Other businesses can **discover** the information about your services, processes, and document types from the registry when they need to interact with you.
>     - **Interoperability**: The registry allows businesses to **communicate and exchange documents** based on standardized **XML formats**. It’s used to facilitate **B2B communication** and **automated business processes**.
> 
> **Example**: A business registers its purchase order format and business process (like how to handle orders and payments) in an ebXML registry. Other businesses can look up this information when they need to interact with the business.

![[CSE445_M2L3_Application_Building.pdf]]

  

  

![[CSE445_M2L4_Development_Example.pdf]]

> [!important]
> 
> ### What .NE T **does**:
> 
> .NET handles the **infrastructure** of application development so developers can focus on writing business logic. Specifically, it:
> 
> 1. **Manages memory** – Automatic garbage collection.
> 2. **Handles runtime execution** – Runs your code safely via the Common Language Runtime (CLR).
> 3. **Provides a large class library** – Ready-to-use code for common tasks (file I/O, networking, cryptography, etc.).
> 4. **Abstracts OS differences** – Code can run on different platforms without changes.
> 5. **Compiles and optimizes code** – Just-In-Time (JIT) and Ahead-Of-Time (AOT) compilation.
> 6. **Offers security and type safety** – Prevents many classes of bugs at runtime.
> 7. **Simplifies building UIs, APIs, data access** – With built-in frameworks like ASP.NET and Entity Framework.
> 
> ---
> 
> ### Without .NET, you’d have to manually:
> 
> - **Manage memory** – Allocate and free memory (risking leaks and crashes).
> - **Handle OS-level APIs** – Write different code for Windows, Linux, etc.
> - **Write or use third-party libraries** – For basics like file handling or HTTP.
> - **Secure your app at low level** – Handle buffer overflows, pointer errors, etc.
> - **Manually compile and link** – Build process is more complex and error-prone.
> - **Build UI/data frameworks from scratch** – Or rely on lower-level, less-integrated tools.

  

> [!important]
> 
> ## .NET Runtime (CLR) Execution – Concise Notes
> 
> ### When a .NET App Runs
> 
> - OS creates one process (`dotnet.exe` or similar).
> - PE header points to `mscoree.dll` which loads `clr.dll` (the CLR).
> - CLR initializes and loads your IL code (`MyApp.dll`).
> 
> ### What CLR Does
> 
> - Finds `Main()` via metadata in the assembly.
> - Uses a JIT compiler (like RyuJIT) to:
>     - Read IL
>     - Translate IL to x86/x64 machine code
>     - Store compiled code in executable memory
>     - Redirect calls to the native code
> 
> ### What the CPU Executes
> 
> - Native code inside `clr.dll` first.
> - Then jumps to JIT-compiled code for `Main()` and other methods.
> - CPU never sees or runs IL directly.
> 
> ### CLR Internals (all native C++ code)
> 
> - IL parsing
> - JIT compilation
> - Garbage collection
> - Type loading
> - Reflection
> - Thread and exception management
> 
> ### Memory Behavior
> 
> - JIT-generated code is stored in memory pages marked executable (RX).
> - All execution happens within the same process.