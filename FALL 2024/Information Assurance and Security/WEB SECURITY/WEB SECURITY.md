1. Example scenario:
    
    - Machine A tells Machine B to connect to its website
    - B ———GET / HTTP/1.0 ————> A
    - A ———200 OK `<img src = "http://bank/withdraw?money=1000">` ——> B
    - B’s browser starts to look for that image and likely has cookies stored for authentication with the bank
    - ==**Scam is complete.**==
    
      
    

  

1. Recieving arbitrary data from a remote server can make our web client do many things:
    - perform a complex render of a page
    - make additional http requests to other servers
    - run arbitrary code (Javascript)

  

1. Broswer is the front line defense when grabbing data from servers.

  

1. In the same way as above, arbitrary data from a remote client may cause a server to:
    - start accessing and modifying databases
    - interact with the broader server system
    - influence other web clients

  

## SQL:

1. Used to implement data queries and data bases.
2. Uses a table with cols and rows

  

1. Create table: `CREATE TABLE tablename (column1, column2, etc)`

  

1. Insert into table: `INSERT INTO <table> VALUES (value1, value2, ...etc)`

  

1. Select: `SELECT <cols> FROM <table> WHERE <conditions/queries>` think prolog.
    
      
    
    ```Python
    SELECT * FROM table \#give me all the columns
    SELECT usernames FROM table # give all usernames
    SELECT username, password FROM table # all username/pass
    SELECT * FROM table WHERE username = "admin"
    SELECT * FROM table WHERE username = "admin" and PASSWORD = "password"
    ```
    

  

1. Delete:
    
    ```Python
    DELETE FROM table WHERE username = "admin"
    ```
    

  

1. Update: 
    
    ```Python
    UPDATE table SET password = "pass" WHERE username = "connor"
    ```
    

  

1. Union: only one (first) column will be returned with all the data merged
    
    ```Python
    SELECT username FROM users UNION SELECT password FROM users
    ```
    

  

1. The schema table: a meta concept, database of databases. The master database, manages data about the other database.
    
    in sqlite,;
    
    ```Python
    sqlite_master is the table name
    and tbl_name is the column name with the names
    of all the tables. 
    
    
    SELECT tbl_name FROM sqlite_master
    ```
    

  

1. Drop Table: Delete the whole table:
    
    `DELETE table`
    

  

  

## INJECTION:

  

1. Just have the shell take care of things when using system call. Happens many times. Programs shell out complex problems.

![[image 23.png|image 23.png]]

- -c means whatever you’re getting, treat it is as user put something in the shell.
- shell finds the date program
- we get the date back

  

1. Specifying time zones

![[image 1 4.png|image 1 4.png]]

  

Red stuff could come from users in some way.

  

1. The string has user input and we don’t know what they might enter.
2. The backtick ` operator takes the term as a command substitution and runs it.

![[image 2 5.png|image 2 5.png]]

  

1. This is a very aggressive level of control…

![[image 3 5.png|image 3 5.png]]

- TZ = ; means this is done, go to next command, which is whoami and then after that is comment.
- So we are only running the whoami command. ==DANGER==

  

- ==User can run any command they want. ⚠⚠==

  

1. This concept is called **Command Injection** into a different context from a different context.

  

There are many types of such injections:

  

### i. HTML INJECTION:

  

![[image 4 3.png|image 4 3.png]]

  

and then,

![[image 5 3.png|image 5 3.png]]

  

1. Data could be anything, not just a name.
2. But this way, user is only attacking themselves and it becomes dangerous only when we’re storing arbitrary input on a database that might be being accessed by other users.

  

### ii SQL INJECTION:

  

![[image 6 3.png|image 6 3.png]]

  

and then…

![[image 7 2.png|image 7 2.png]]

- ==1=1 returns true and now admin is pwned! ⚠==

  

Solution is to parse before and then fill arguments rather than the opposite and parsing as full statement.

  

### iii STACK INJECTION:

![[image 8 2.png|image 8 2.png]]

  

and then…

![[image 9 2.png|image 9 2.png]]

- access beyond the memory has happened and return address has been lost. Denial of service. Crash.

  

![[image 10 2.png|image 10 2.png]]

- we are returning to a completely different context.

  

## Same Origin policy:

![[image 11 2.png|image 11 2.png]]

1. Blue server is located somewhere else and needs session info to be able to provide the resources.

  

1. Origin is tuple:
    
    ![[image 12 2.png|image 12 2.png]]
    
    ![[image 13 2.png|image 13 2.png]]
    
    subdomains are also different origin, and different ports too.
    
      
    
    ![[image 14 2.png|image 14 2.png]]
    

When we are making requests to different origin, the above is all that’s allowed. (Jsons not allowed, etc.)

What’s allowed to be read?

![[image 15 2.png|image 15 2.png]]

  

  

CHALLENGE NOTES:

**PATH TRAVERSAL**:

1. when traversing paths on server using curl or python, URL to URI resolution takes place and all the ‘.’s are got rid of. However, if we have something like, /.., without anything before the /, it just goes to / and not a directory above / ( because it does not know what’s above /), hence we must encode the ‘.’ s. (not the slashes). Look up URI normalization for more info.

  

1. If the host header is not what is expected (from the hostname), server will not respond correctly.

  

1. challenge is the subdomain and [localhost](http://localhost) is the domain in challenge.localhost.
2. in Path Traversal II: we use the fortunes directory to navigate to the flag because then we will not have to start with any / or ‘.’s and then the split won’t be able to remove anything and the path will be exactly what we pass in.

  

**CMDi: Command injection**

1. Depressingly often, developers rely on the command line shell to help with complex operations. In these cases, a web server will execute a Linux command and use the command's results in its operation (a frequent usecase of this, for example, is the `Imagemagick` suite of commands that facilitate image processing). 
2. In python this is usually done by os.system(look up). But we use subprocess.check_output
3. We GET when we fill up a form and want some data from the server. POST is only when we are making changes to/doing something on the server.
4. When ‘;’ cannot be used, we can utilize piping. |
    - in CMDi 2 we used pipes but in a non conventional way: `ls -l / | cat /flag`. Pipe the output of the first command to the second command but actually don’t do that and take the argument for the second command. (???).

  

**SQLi:**

1. ; is used to chain SQL commands
2. % is a wild card that matches all characters
3. “- -” is used to make SQL ignore everything after that.
4. UNION was used heavily for SQLi 3 and 4
    - SQLi3: The solution was to inject UNION SELECT password FROM users
    - SQLi4: The solution was to first UNION SELECT name FROM sqlite_master WHERE type = “table” and then the same thing as SQLi3 using the table name obtained.

  

1. SQLi 5 we do “ OR substr(password, 0, 0) = something.
    - This challenge needs a python script to be done efficiently
    - In the script we have to make sure we are being specific of the characters. The challenge had stuff like pwnscoldege to mislead the script. We had to think of more specific start.

  

  

XSS1:

1. XSS is cross site scripting: Vulnerabilities in which injections occur into client-side web data (such as HTML).
    
    - Victim is not the server but some client using the web application.
    - Must make sure the weird syntax of curl is right for example, check the quotes, make stuff that’s long a string, etc.
    - First 2 challenges, what we inject gets stored into the server in the database as posts and the victim program retrieves those posts from that database. This is called _**stored XSS**_.
    
      
    
2. Refelected XSS: in Challenge 3, we practice this type of scripting. The scripting happenes immediately as the victim loads the specially crafted url.
3. XSS5: Breathe. Stretch. You were having doubts because of not looking at how the page was changing with the xss injection.
    - Everything was becoming part of the textarea
    - you cannot login by sending POST request to /. To login we need to go to /login.
        - Basically you send a GET request to / and get a login form
        - Then you enter parameters and use a POST to send to /login
        - and then you get a session cookie in response
        - then send this cookie as a header in a GET to / and you will be returned the internal page.

  

1. XSS6: In the same way as above, this time, you cannot directly access publish using a GET. This time it is a form with a submit button that you have to send your POST request to. Probably data from that button?, not there is no data to send so just send a POST with empty body object.

  

1. XSS7: We start a listening port at 1337 with `nc -lv` `[localhost](http://localhost)` `1337`
    
    - Once we recieve a request on our nc server, we must also send a response like:
        
        ```Bash
        HTTP/1.0 200 OK 
        Content-Type:
        Content-Length:
        
        body
        ```
        
    - In this challenge, we had to send admin’s cookies as the body of the fetch request. We only had to do document.cookie to get those.
    
      
    

  

  

**CSRF:**

1. There is almost nothing preventing a malicious website from simply  
    directly causing a victim visitor to make potentially sensitive  
    requests, such as (in our case) a  
    `GET` request to `http://challenge.localhost/publish`!
    
    This style of _forging_ requests _across_ sites is called _Cross Site Request Forgery_, or _CSRF_ for short.
    

In CSRF 1 we cannot do a javascript fetch because SOP prevents it. So we use a Form to publish the flag.