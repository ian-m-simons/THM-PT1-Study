Active Reconnaissance is the process of directly interacting with a target system or network to gather information about it. Active techniques leave traces such as log entries, IDS alerts, WAF blocks, and honeypot triggers. From a Red Team perspective, the goal is to blend in. In this room we will cover:
- [[#Web Browser]] developer tools
- [[#Ping]]
- [[#Traceroute|TraceRoute and MTR]] 
- [[#Telnet]] 
- [[#NetCat|NetCat (nc)]] 
### Web Browser 
least suspicious tool for active reconnaissance. traffic blends in with normal user activity making it difficult to distinguish. 
##### Transport-Level Basics
- http = tcp port 80 (nowadays typically redirects to https)
- https = tcp port 443 
- QUIC (HTTP/3) = udp port 443 (combo of TCP and TLS) protocol column will typically display `h3`
##### Developer Tools
- Network tab displays all requests and responses in real time including requests and response header, timing, status codes, and cookies sent and received
- Console tab allows you to execute JavaScript snippets directly in the page context, view errors, and interact with the DOM
- Sources tab lets you browse JavaScript, CSS, and HTML files loaded by the page
- Application tab under the storage section lets you inspect cookies, local storage, and session storage.
- Security Tab provides certificate details including the issue, validity period, and Subject alternative names (SANs)
##### Browser Extensions
some popular reconnaissance browser extensions include 
- FoxyProxy - allows you to switch between proxies such as burp suite, ZAP, and SOCKS5 tunnels. 
- User-Agent Switcher and Manager - changes the User agent string to emulate different browsers, OS's, or devices. Though this technique can often be detected by WAFs and CDNs
- Wappalyzer - automagically identifies technologies used on the site, including CMS platforms, web servers, JavaScript frameworks, analytics tools, CDNs, and databases. It runs passively while you browse. popular alternatives include
  1. BuiltWith Technology Profiler
  2. WhatRuns
  3. Library Detector for Chrome/Firefox
over time most people settle on a small set of three to five extensions that best match their workflow

### Ping
used to check if a host is reachable or not using an Internet Control Message Protocol (ICMP) echo request (type 8) and ICMP echo reply (type 0). 
to ping x number of times use -c in linux or -n in windows ie 
`ping -c 5 10.60.22.10` for linux or 
`ping -n 5 10.60.22.10` for windows
-4 and -6 will force ipv4 and 6 pings respectively
ping6 is also available as a standalone command for IPv6
Rememebr TTL = maximum router hops 
Linux typically defaults to a TTL of 64 while windows typically defaults to a 128 TTL
Ping quick reference table

| Result                                 | Most likely meaning                             | Next Step                            |
| -------------------------------------- | ----------------------------------------------- | ------------------------------------ |
| Fast Replies, low or no packet loss    | Target is online and Allows ICMP                | Proceed to port scanning             |
| "Destination Host Unreachable"         | Target is down or no route exists               | Check if machine is powered on       |
| 100% packet loss with no error message | ICMP is filtered or blocked                     | Try TCP/UDP host discovery with nmap |
| High Latency or heavy loss             | Network congestion, long distance, or filtering | investigate the path with traceroute |
### Traceroute
the `Traceroute` command (`tracert` for windows) traces the route that a packet takes from your system to a target host. point is to discover IP addresses of routers along the path and determine how many hops between you and the destination. for IPv6 use the -6 flag or the standalone `traceroute6` command

##### How traceroute works
exploit ttl start with ttl of 1 well first router drops and replies "hey your packet died btw" continue increasing ttl until you reach target saving IPs along the way. Some routers are configured not to send ICMP TTL exceeded messages, this is common in secure environments and these routers appear as  an `*` in the output. Linux will default to UDP datagrams, to switch te TCP based tracing use the -T flag
### Telnet
sends all data in cleartext including usernames and passwords. today we generally use SSH instead. Telnet is still useful for banner grabbing, you can use telnet to connect to any TCP port and observe the response which may give you useful information to telnet to a machine at IP 10.0.62.13 on port 3389 you would enter the following 
```bash
telnet 10.0.62.13 3389
```
if facing encryption use curl, openssl, or netcat instead 
```bash
curl --head https://<destination>
openssl s_client -connect <ip>:port 
nc --ssl 
```
### NetCat
netcat is a versatile networking utility that supports both TCP and UDP protocols. It can function as a client or a server. There are also more modern versions like ncat from the Nmap project which also support IPv6 and SSL encryption. 
banner grabbing is performed as follows 
```bash
nc <ip address> <port>
```

netcat flags

| flag | meaning                                      |
| ---- | -------------------------------------------- |
| -l   | listen mode                                  |
| -p   | specify the port number                      |
| -n   | numeric only; no DNS resolution of hostnames |
| -v   | verbose output                               |
| -vv  | very verbose output                          |
| -k   | keep listening after the client disconnects  |
