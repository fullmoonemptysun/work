1. Multiple layers when communicating through the internet:
    
    1. Frame data layer: low layer consists of computers connected directly
    2. Internet layer: Find the right computer on the internet (IP, etc.)
    3. Transport Layer: Find the right program/service from the many services on the right computer using ports.
    4. Application layer: how we are talking with the application.
    
      
    

  

1. When an HTTP request is sent, it is in the form of a request line:
    
    `METHOD SP REQUEST URI SP HTTP-VERSION`
    

  

1. HTTP response format (status line)
    
    `HTTP VERSION sp STATUS-CODE`
    
    **Status code** refers to what the status is of taking some action (whatever was requested)
    
    ![[image 22.png|image 22.png]]
    
    ![[image 1 3.png|image 1 3.png]]
    

  

  

1. Request METHODs:

- GET: Retrieve information that is identified by the URI. This information can be static or dynamic (output of a process).
- HEAD: Same as get, but don’t send the actual content-body itself, instead, send Meta-info (header info) things like resource type, size, etc.
- POST: Push resource to the server or interact with a resource on the server. Basically, doing stuff on the server.

  

1. URL format:

![[image 2 4.png|image 2 4.png]]

  

1. REQUEST URI: comes from the url.
    
    - The following characters in the URI must be encoded.
    
    ![[image 3 4.png|image 3 4.png]]
    
    - Encoding is done in the following format
        
        `DANGERCHAR ----> %hexVal` , where hexVal is the hex value of the char in the ASCII encoding.
        
    
      
    
2. JSON: Data structuring format. JavaScript Object Notation.

  

1. HTTP is a stateless protocol. Example: I post login credentials and the server logs me in, now when I try to access something, the server won’t know I’m the same person because http does not carry that information.

  

1. Solution: we will maintain HTTP headers to track state.
    - CLIENT: POST /login HTTP/1.0 {user: connor pass: 12345}
    - SERVER: 302 MOVED TEMPORARILY {LOCATION: [dlkfjsdlk.com](http://dlkfjsdlk.com) SET-COOKIE: auth=connor
    - CLIENT: GET / HTTP/1.0 , HOST: [dlkfjsdlk.com](http://dlkfjsdlk.com/), COOKIE: auth=connor
    - SERVER: “ok I remember you from that cookie, now I have some state to refer to” HTTP/1.0 200 OK “hello connor”

  

1. But the above approach has a vulnerability:
    - CLIENT: GET / HTTP/1.0, ……, COOKIE: auth===admin==
    - SERVER: HTTP/1.0 200 OK “Hello ==admin==”
    - this is bad…Someone can directly just send an http request with the cookie header without the right credentials.

  

1. Solution:
    - A database is maintained with session_id cookie values assigned to users. These can only be accessed if someone log ins with the right credentials.
    - Subsequently any changes like a theme change, a preference change can be traced using cookies.

  

### cURL OPERATIONS

1. **cURL** is a computer software project providing a library and command-line tool for transferring data using various network protocols. The name stands for "Client for URL". We can connect like:
    
    `curl url` can send HTTPS requests from the console itself.
    
    - `curl -X POST URL` will send a POST HTTP request.
    - use -v option to see what the whole HTTP request looks like
    - for headers, use -H with ‘Header:value’.
    - -F for form data curl like: `-F "someparam=value" hostname` , 
        
        ```Python
        curl -F "name=daniel;type=text/foo" url.com 
        ```
        
    - if there are more than one fields in the form, they have to be preceded by one -F for each of them and then sent.
    - -d is also used to pass form data like : `-d/--data` uses application/x-www-form-urlencoded.
    - When -F is used, method is always POST
    - With -d, we can set the method also to GET, PUT, PATCH

### NETCAT OPERATIONS

1. Can also use netcat `nc :`
    
    - `nc URL port`
    - For headers we just set it on the prompt
    - For POST form data, we just set it directly but we have to specify Content-length and Content-Type first then a blank line seperates the body of the data from these headers.
    - For multiple POST fields, we just count the length and type and just put it on the console (must count newlines).
    
      
    

### PYTHON OPERATIONS

1. Can also use python requests library:
    
    ```Python
    import requests
    
    request = requests.get("url", headers={dictionaryobject})
    
    print(request)
    
    ```
    
    - In python requests, specify header like this : `requests.method("local host", headers={})`
    - To pass form data to POST, we do : `requests.post("hostname", data={form data})`
    - Since it is passed like an object, we can include a comma seperated list of other fields in the same object.
    - Must use params instead of data when sending a form in GET
    
      
    

  

  

1. Parameters to GET can be sent as a query string using curl, nc or python. But it must be in double quotes for curl because the & character will be interpreted as an operator in bash.
2. When we are sending GET requests, any form data is just data (query data). Only when POST, we talk about having Form data (-F), -d is data, and we can use it for both form data and query string.