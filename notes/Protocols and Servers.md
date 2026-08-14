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