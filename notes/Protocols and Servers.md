a lot of these protocols are old and generally have been replaced by their secure counterparts. However the underlying protocols are the same they're just wrapped in TLS now. We will cover:
- [[#Telnet]] 
- [[#Hypertext Transfer Protocol (HTTP)]]
- [[#File Transfer Protocol (FTP)]]
- [[#Simple Mail Transfer Protocol (SMTP)]]
- [[#Post Office Protocol 3 (POP3)]]
- [[#Internet Message Access Protocol (IMAP)]]
### Telnet 
Telnet is an application-layer protocol used to connect to a virtual terminal of another computer. All data is sent as clear text, making it inherently insecure and an easy target for attackers
##### Telnet Today
Telnet used to be widely used for remote administration. However it has been almost entirely replaced by SSH for interactive remote access. You will likely only encounter telnet in the following places:
- Legacy systems and elder networking equipment
- Embedded devices and IoT equipment with limited resources
- Internal networks where security was never prioritized
- Misconfigured systems where Telnet was enabled but never disabled
Typically finding an open Telnet port is a significant finding because it indicates either a legacy system or security miscofiguration
##### Telnet Client as a Testing Tool
Telnet servers are rare, but the client remains usefl as a simple tool for connecting to any TCP port and manually interacting with text-based protocols. This can be very useful for [[Active Reconnaissance]] 
##### How Telnet Works
A telnet server listens for incoming connections on port 23. the connection steps are:
1. The user is asked to provide their login name
2. They are asked for their password (password is not displayed on screen similar to ssh)
3. Once the system checks the login credentials, the user is greeted with a welcome message
4. the remote server grants a command prompt ($=not root | #=root)
the secure alternative to telnet is SSH

### Hypertext Transfer Protocol (HTTP)
HTTP is the protocol used to transfer web pages.
##### HTTP vs HTTPS
HTTP sends data as clear text, meaning anyone with access to the network traffic can read the content being transferred, including sensitive information like login credentials and personal data. Most websites today use HTTPS (HTTP Secure) wrapping HTTP inside DLS encryption. Modern browsers will mark HTTP sites as "Not Secure" and some will limit capabilities of HTTP sites. All that said, we still need to know HTTP because:
- HTTP commands and structure are identical to HTTPS
- You will encounter HTTP during internal penetration tests and on legacy systems
- Understanding the protocol helps you identify and exploit web vulnerabilities
- Tools like Burp Suite decrypt HTTPS traffic for analysis showing you raw HTTP
##### Manually Sending HTTP requests
Because HTTP is a cleartext protocol, you can use tools such as Telnet (or NetCat) to communicate with a web server. However to do this you will need to input the HTTP-related commands instead of the web browser doing that automagically for you. 
##### Information Revealed in HTTP Headers
In the practice exercise we did, the headers revealed both the webserver and version, as well as the operating system. This info  is valuable during [[Active Reconnaissance|reconnaisance]] because:
- specific versions may have known vulnerabilities
- the OS helps tailor further attacks
- Even knowing the web server software narrows down potential attack vectors
Security-conscious administrators often configure their servers to suppress or obscure this info. 
##### Web Servers and Clients
An HTTP server and an HTTP client are required to use the HTTP protocol. popular choices for web servers include:
- Nginx -- has becme the most widely used web server on the internet, popular for performance and efficiency in handling concurrent connections (its FOSS)
- Apache -- still extremely popular and powers a large portion of websites. highly configurable with a vast ecosystem of modules (its FOSS)
- Internet Information Services (IIS) -- Microsoft's web server, commonly found in Windows enterprise environments, requires a Windows server license
other notable options include; LiteSpeed, Caddy, and Node.js
most popular browsers today are
- Chrome -- dominant market share
- Safari -- default on Mac and iOS
- Edge -- by microsoft, chromuim based internet explorer replacement
- Firefox -- by mozilla open-source, privacy focused
generally web browsers are free to install and use. For penetration testing and security research, Firefox is usually preferred because of its extensive developer tools and add-on ecosystem. 
##### HTTP protocol versions
there are 3 versions of HTTP to be aware of 
- HTTP/1.1 workhorse for decades, pretty straight forward, most common for learning and manual testing due to human readable format
- HTTP/2 introduced multiplexing (multiple requests over a single connection), header compression, and server push. It is binary rather than text-based, making it harder to manually interact with via Telnet
- HTTP/3 uses QUIC for improved performance especially on unreliable networks. it is increasingly common on major websites
### File Transfer Protocol (FTP)
Developed to make the transfer of files between different comptures with different systems efficient. one of the earliest protocols designed for the internet and remains in use today. Has largely been replaced by secure alternatives for most purposes
##### Modern FTP
FTP sends credentials and data in the clear. For this reason, it has been largely replaced by:
- SFTP (SSH FTP) runs over SSH on port 22 and encrypts all traffice (must common replacement for FTP)
- FTPS (FTP Secure) adds TLS encryption to the FTP protocol on port 990 (implicit TLS) or uses STARTTLS on port 21
- SCP (Secure Copy Protocol) also runs over SSH, though it is being deprecated in favor of SFTP
an FTP is a common and significant finding in a penetration test 
##### Manually Interacting with FTP
FTP sends and receives data as cleartext, so you can use telnet (or netcat) to communicate with an FTP server and act as an FTP client. FTP has two modes, passive and active
- Active: in active mode the data is sent over a separate channel originating from the FTP server's port 20. The server initiates the data connection back to the client. This often fails when the client is behind a firewall or NAT
- Passive: in passive mode, the data is sent over a separate channel originating form an FTP client's port above number 1023. The client initiates both connections. This is smore firewall-friendly and is the default for most modern FTP clients
##### How FTP File Transfer Works
The FTP client initiates a connection to an FTP server, which listens on port 21 by default. All commands are sent over the control channel, once the client requests a file, another TCP connection is established between them for the data transfer.
##### Using an FTP Client
Because of the dual connection architecture, an actual FTP client is needed to download files.  Note you may need to switch to binary or ascii mode as appropriate
##### Anonymous FTP 
Some FTP servers Allow anonymous login typically using the username anonymous or ftp with any email address as the password (or no password at all). Historically this was for public file distribution, such as software downloads and documentation. During penetration testing, always try an anonymous login when you discover an FTP server
##### FTP Servers and Clients
there are various FTP server software options available, popular options include:
- VSFTPD (very secure FTP daemon) is one of the most common FTP servers on Linux systems
- proFTPD is highly configurable and modular
- Pure-FTPd focuses on security and simplicity
- on Windows IIS includes FTP server capabilities
for clients a console client is commonlf found on linux systems, you can also use FileZilla. Note that major web browsers have removed FTP support in recent years, so browser based FTP is no longer available
##### Security Implications
it's all clear text. capturing traffic means you have usernames, passwords, file contents, directory listings, the hole 9 yards

### Simple Mail Transfer Protocol (SMTP)
email delivery over the internet requires the following components:
1. Mail User Agent (MUA): the email client (ie outlook, thunderbird, a webmail interface)
2. Mail Submission Agent (MSA): Receives mail from the MUA, checks for errors, and forwards it
3. Mail Transfer Agent (MTA): Routes and delivers mail between servers. 
4. Mail Delivery Agent (MDA): Stores the email in the recipients mailbox for retrieval

the five steps that an email needs to go through to reach the recipients inbox are:
1. the MUA has an email message to be sent, it connects to the MSA to submit the message
2. the MSA receives the message, then checks for any errors before transferring it to the MTA, which is commonly hosted on the same server. 
3. the MTA sends the email message to the MTA of the recipient. The MTA can also function as an MSA 
4. A typical setup has the MTA server also functioning as the MDA
5. the recipient collects their email from the MDA using their email client (MUA)
##### Email Protocols
- Simple mail transfer protocol (SMTP) for sending email 
- Post Office Protocol version 3 (POP3) or Internet Message Access Protocol (IMAP) for receiving email
##### SMTP Ports and Encryption
originally SMTP sent everything in the clear, however modern email uses several ports with different security models
- Port 25 is the traditional SMTP port used for server-to-server communication (MTA to MTA). it is often blocked by ISPs for residential connections to prevent spam. On port 25, encryption is optional and negotiated via STARTTLS
- Port 587 is the submission port, used by email clients (MUA) to submit messages to their mail server (MSA) this is the recommended port for sending email and typically requires authentication. TLS encryption is negotiated via the STARTTLS command. 
- Port 465 was originally designated for SMTPS (SMTP over implicit TLS), then deprecated, and has since been reinstated. On this port, TLS encryption begins immediately upon connection.
##### Manually Sending Email with Telnet
because SMTP sends in the clear, you can use a basic Telnet client to connect to an SMTP server and act as an email client (MUA) sending a message. use the command `helo <hostname>` or `ehlo <hostname>` for extended SMTP and then start composing the email
##### Email Spoofing and Why it Works
the server does not verify the sending address (the from command) this is how spoofing works. 
### Post Office Protocol 3 (POP3)
Post Office Protocol version 3 (POP3) is a protocol used to download email messages from a Mail Delivery Agent (MDA). The mail client connects to the POP3 server, authenticates, ddownloads the new email messages, and then (optionally) deletes them from the server. 
##### POP3 Ports and Encryption
POP3 was originally designed without encryption
- Port 110 is the default POP3 port using cleartext. Some servers support upgrading the connection to TLS using the STLS command (similar to STARTTLS in SMTP)
- Port 995 is used for POP3S (POP3 over implicit TLS). The Connection is encrypted from the start
##### Manually Interacting with POP3
you can manually interact with POP3 using telnet because again, clear text. The commands you use via telnet are the exact same commands a GUI mail client would use.
##### POP3 Behavior: Download and Delete
The default setting is to have the mail client delete messages after they are downloaded, this means:
- emails are stored locally on your device, not the server
- once downloaded, the email is only accessible from that specific device
- If your device is lost or damaged, the emails are gone (unless you backed them up)
- storage on the mail server is minimized
This behavior can be changed from the mail client settings to keep copies on the server. However POP3 is not convenient for multiple mail clients using the same account because there is no synchronization
##### POP3 vs IMAP: When to Use Each
POP3 is still useful in specific scenarios such as:
- you want to access email offline and have limited or unreliable internet connection
- you need to minimize server-side storage
- you only access email from a single device
- archiving emails locally
IMAP has largely replaced POP3 because of its synchronization capabilities
### Internet Message Access Protocol (IMAP)
IMAP is more sophisticated than POP3. IMAP makes it possible to keep your email synchornized across multiple devices (and mail clients). 
##### Why IMAP Became the Standard
- Emails remain on the server and are accessible from any device
- read/unread status, folders, and flags sync across clients
- Deleting an email on one device removes it everywhere
- Search can be performed server-side without downloading all messages
##### IMAP Ports and Encryption
like previously examined protocols, originally designed without encryption:
- port 143 is the default IMAP port using cleartext, Many servers support upgrading the connection to TLS using STARTTLS command
- port 993 is used for IMAPS (IMAP over implicit TLS) the connection is encrypted from the start
Most email providers today require IMAPS on port 993. Major providers like gmail, outlook, and yahoo, have disabled plaintext IMAP access entirely. 
##### Understanding the IMAP Response
initial respons will include `CAPABILITY` which lists features for example:
- `IMAP4rev1` indicates IMAP version
- `STARTTLS` means the server supports upgrading to an encrypted connection
- `IDLE` allows the server to push notifications of new mail
- `ACL` indicates access control list support
##### Common IMAP Commants
remember all commands must be preceded by a unique tag

| Command                 | Description                             |
| ----------------------- | --------------------------------------- |
| LOGIN username password | Authenticates the User                  |
| LIST "" "*"             | Lists all mailbox folders               |
| SELECT folder           | Opens a folder for read/write access    |
| EXAMINE folder          | Opens a folder for read-only access     |
| FETCH n BODY[]          | Retrieves message number n              |
| SEARCH criteria         | Searches for messages matching criteria |
| STORE n + FLAGS (\Seen) | Marks message n as read                 |
| LOGOUT                  | Ends the session                        |
##### IMAP vs Webmail
nowadays most people use webmail (logging into gmail through a web interface) the underlyng mail protocols are still used though. Many orgs still run their own mail servers, 
### Key Takeaways
Every protocol we covered transmits in clear text by default this means that anyone listening can sniff credentials etc and telnet can be used to poke and prod
##### Protocol Reference
| protocl | TCP port | Application(s) | Data Security | Secure Alternative          | Secure Port                   |
| ------- | -------- | -------------- | ------------- | --------------------------- | ----------------------------- |
| FTP     | 21       | File transfer  | clear text    | FTPS or SFTP                | 990 (FTPS), 22 (SFTP)         |
| HTTP    | 80       | World Wide Web | cleartext     | HTTPS                       | 443                           |
| IMAP    | 143      | Email (MDA)    | cleartext     | IMAPS                       | 993                           |
| POP3    | 110      | Email (MDA)    | cleartext     | POP3S                       | 995                           |
| SMTP    | 25       | Email (MTA)    | cleartext     | SMTPS or SMTP with STARTTLS | 465 (SMTPS), 587 (Submission) |
| telnet  | 23       | Remote Access  | cleartext     | SSH                         | 22                            |
