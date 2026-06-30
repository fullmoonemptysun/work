
- Header, Content

![[Pasted image 20260504164934.png]]

- SMTP from the origin to the MDA and then POP3 or IMAP4

## Mail Exchange (MX) record
- DNS record identifying mail servers responsible for receiveing email messages on behalf of a domain.


## Email system components
- User agents (Clients)/Webmail:
	- Compose and read emails

- Mail servers:
	- Send and receive emails on user's behalf
		- Mail transfer agent (MTA)
		- Mail Delivery Agent (MDA)
		- Mail Submission Agent (MSA)


## Email clients
- POP3/IMAP4 to download emails from mail servers
- SMTP to send emails to the mail servers
![[Pasted image 20260504171141.png]]

![[Pasted image 20260504171443.png]]
client to server                                          between servers




## Email Forensics steps
- Identification, Preservation
- Steps for analysis:
1. Examine each field in the email header, the recorded IP address of sender,
2. Email route - may include clues about sender’s origin, location, methods,
3. Analyze domain name’s point of contact,
4. Aggregate suspect’s contact information,
5. Content analysis on suspicious email(s): Determine if crime/violation has been committed.
6. Investigate attachments,
7. Acquire attributes against network logs.


## Investigation Methods

Header Analysis: metadata of header
Server Investigation: email sever & system logs.
Network Device Investigation: router, firewalls, Switch
Software Embedded Analysis: MIME (Multipurpose Internet Mail Extensions)
Sender Mail Fingerprints X-Mailer: iPhone Mail (17D50)
X-Mailer: PHPMailer 5.2.14
Use of Email Trackers: IP, location of recipient.
Volatile Memory Analysis: spoofed mails (header)
Attachment Analysis: malware (virus total)


## Examining the headers
Received: Shows the path an email takes as it travels through mail servers, starting from
the sender and ending at the recipient. Each mail server appends a new Received header,
creating a traceable route from sender to recipient.
Message-ID: A unique identifier assigned to each email by the sending server. Ensures
each email is uniquely identifiable, useful for tracking and threading.
○ e.g., Message-ID: ABC12345@mail.sender.com or random value like “6dd8deb1acasibi”
From: Who the message is from. This is the easiest to forge, and thus the least reliable.
Reply-To: The address to which replies should be sent. Often absent from the message,
and very easily forgeable.
Return-Path: The email address for return mail. Same as Reply-To:
X-headers: extra non-standard info like SPF, DKIM, DMARC auth results, spam filter info,
etc. X to denote that the value is experimental or an extension of the standard header


## Received (Email traceablity)
Read from bottom to top to see all server hops


## SPF Sender Policy Framework
- The SPF field verifies whether the server sending the email was authorized by the domain's SPF record (stored in DNS). This is essential to determine if the email was sent legitimately.


## DKIM (tampering detection)
- DKIM ensures the email body and headers remain unchanged after being sent by verifying its cryptographic signature. It is a powerful tool for confirming the integrity of the email.

## Message-ID
- The Message-ID is a unique identifier assigned to every email by the sender’s server. It’s instrumental in tracking emails across servers and detecting duplicate or altered emails.


## Full header list
Reply-To: The email address you send your response too.
From: Displays the message sender; it is easy to forge.
Content-type: The most common character sets are UTF-8 and ISO-8859-1.
MIME-Version: Declares the email format standard in use. The MIME-Version is typically “1.0.”
DKIM-Signature: Domain Keys Identified Mail authenticates the domain the email was sent from and should protect
against email spoofing and sender fraud.
Received: Lists each server that the email travels through before hitting your inbox. You read “Received” lines from
bottom to top (reverse order); the bottom-most line is the originator.
Authentication-Results: Contains a record of the authentication checks carried out; can contain more than one
authentication method.
Received-SPF: The Sender Policy Framework (SPF) forms part of the email authentication process that stops sender
address forgery.
Return-Path: The location where non-send or bounce messages end up.
ARC-Authentication-Results: The Authenticated Receive Chain is another authentication standard; ARC verifies the
identities of the email intermediaries and servers
ARC-Message-Signature: The signature takes a snapshot of the message header information for validation; similar to
DKIM.
ARC-Seal: “Seals” the ARC authentication results and the message signature, verifying their contents; similar to DKIM.
X-Received: Non-standard header added by some user-agents or MTA like the google mail SMTP server.
X-Google-Smtp-Source: Shows the email transferring using a Gmail SMTP server.
Delivered-To: The final recipient of the email in this header.


## Faking emails
- Email spoofing (Seems from someone legit)
- Anonymous remailing: attempt to get rid of tracking details and then send the email
- Valid emails: Seems from a trusted source. Content is sus. May contain URL to malicious site. 


## How to find the real sender
- Find earliest trusted gateway (Bottom)
- Query MX record for the domain (dig mx domain)


## Examining Additional Email files/logs
- Email messages are saved on the client side (if using Email client apps)
- **Unix server logs** : /etc/sendmail.cf (config for sendmail); /etc/syslog.conf, /var/log/maillog (SMTP and POP3 communications. (ip addresses and time stamps).


## Identifying email crimes/violations
- Spam (illegal in washington state)
- Narcotics trafficking, Sexual harassment, CP, Fraud, Terrorism


# Email Content Analysis

- Access the victim's computer and retrieve evidence.
- Use victim's/Suspect's client:
	- Find and copy evidence in the email
	- access protected or encrypted material
	- carve emails:
		- including header.


## Carving Email Messages
- When carving email messages during a digital forensic investigation, analysts must account for the format the email was stored in. The two primary formats are MBOX (RFC 4155), a plain-text file that concatenates all messages into one file and is the most widely supported format, and MIME, which underpins vendor-specific formats like Microsoft's .pst and .ost files, as well as others like .eml, .msg, .pdf, .html, and .nsf. A major challenge in email forensics is that most commercial tools are optimized for Microsoft-based email systems, leaving limited options for analyzing emails stored in other formats.


