This Section is about attacks against the clear text communication we discussed in [[Protocols and Servers]]. It includes how it happens, what attackers do with access and how modern protocols prevent it. Protocols we will examine are:
- Telnet
- HTTP
- FTP
- SMTP
- POP3
- IMAP
Servers implementing these protocols are subject to different kinds of attacks:
1. [[#Sniffing Attack|Sniffing Attack (Network Packet Capture)]]
2. [[#Man-in-the-Middle (MITM) Attack]]
3. [[#Password Attack|Password Attack (Authentication Attack)]]
4. Vulnerabilities

You are of course aware of the CIA triad (confidentiality, integrity, availability) but the other side of that is the attacker aims, DAD (Disclosure, Alteration, and Destruction). This room will also look at:
- [[#Transport Layer Security (TLS)]]
- [[#Sniffing Attack]]
### Sniffing Attack
A sniffing attack refers to using a network packet capture to collect information about a target.
##### Where Sniffing Attacks are Still Relevant
Sniffing attacks remain relevant in 
- **Internal corporate networks** where traffic between systems may not be encrypted
- **Legacy systems** that still use cleartext protocols (older mail servers, embedded devices, ICS)
- **Misconfigured services** where TLS is available but not enforced
- **IoT devices** that often use unencrypted protocols for communication
- **Wireless networks** where attackers within range can capture traffic
- **After a successfully MITM attack** that downgrades or strips encryption
Sniffing remains a valuable technique for gathering credentials and understanding how systems communicate
##### Packet Capture Tools
The most common tools used for packet captures are 
- **TCPDump** is a FOSS CLI program that has been ported to work on many OS's. it's lightweight and available on most Linux systems by default
- **Wireshark** is a FOSS GUI program available for several operating systems including Linux, Mac and windows. It provides powerful filtering, protocol dissection, and visualization capabilites
- **Tshark** is a CL alternative to Wireshark that uses the same dissection engine. It is useful for scripting and automation
Other useful tools include
- **TCPFlow** for reassembling TCP streams
- **Ngrep** for pattern matching in network traffic
- **NetworkMiner** for extracting files and images from captured traffic
##### Practical Example: Capturing POP3 Credentials
go back and review the exercise but here are some quick tcpdum one liners
```bash
#Capture traffice on a specific port (ascii)
sudo tcpdump port 110 -A

#Capture traffic to/from a specific host (ascii)
sudo tcpdump host 10.20.30.148 -A

#Capture HTTP traffice (may include credentials in POST requests) (ascii)
sudo tcpdump port 80 -A

#Capture FTP traffic (credentials sent in cleartext) (ascii)
sudo tcpdump port 21 -A

#Write captured packets to a file for later analysis
sudo tcpdump -w capture.pcap

#Read and analyse a pcap (ascii)
tcpdump -r capture.pcap -A
```
##### Mitigation
All clear text data is susceptible to this kind of attack regardless of protocol. all that is needed for success is access to a system between the two communicating parties or being on the same network segment. the primary mitigation is switching to the secure version of protocols. Additional mitigations include:
- **Network segmentation** limits which systems can see each other's traffic by dividing the network into isolated zones. 
- **Encrypted VLANs and tunnels** protect sensitive traffic even on internal networks
- **802.1X port-based authentication** requires devices to authenticate before gaining network access, preventing unauthorized devices from connecting and sniffing traffic
- **Zero trust architecture** is a security model that treats all network traffic as potentially hostile. encrypting all communications regardless of whether they originate inside or outside the network perimeter. 
- **Monitoring for ARP spoofing** and other traffic redirection techniques helps detect sniffing attempts in progress
The modern best practice is to assume the network is hostile, even if it is an internal network
### Man-in-the-Middle (MITM) Attack
easy to carry out if two parties do not confirm authenticity and integrity of each message. Some protocols have inherent insecurities that make them susceptible to this kind of attack
##### How MITM Attacks Work
to perform this attack, the attacker needs to reroute traffic through his device. common techniques include 
- **ARP Spoofing** is effective on local networks. The attacker sends forged ARP messages to associate their MAC address with the IP address of the default gateway or target system.
- **DNS Spoofing** involves providing false DNS responses to redirect victims to attacker-controlled servers.
- **Rogue Access Points** are fake wireless access points set up by attackers. 
- **BGP Hijacking** operates at the internet routing level, where attackers announce false BGP routes to redirect traffic through their own infrastructure. This is a much more sophisticated attack typically targeting specific organizations or regions
##### Tools for MITM Attacks
- **Bettercap** is the modern successor to Ettercap and is actively maintained. It supports ARP spoofing, DNS spoofing, HTTP/HTTPS proxying, and has a modular architecture for various scenarios
- **Ettercap** is a classic tool for MITM attacks on LANs. While still functional, Bettercap is generally preferred for modern assessments
- **mitmproxy** is an interactive HTTPS proxy that allows inspection and modification of traffic. It is particularly useful for analysing and manipulating HTTP/HTTPS communications
- **Responder** is designed for windows environments and exploits name resolution protocols such as LLMNR (Link-Local Multi-cast Name Resolution) and NBT-NS (NetBIOS name service) These are fallback protocols that windows systems use when standard DNS resolution fails. Responder listens for these broadcasts and queries and responds with its own IP address, tricking victims into sending authentication credentials to the attacker. This is a common technique during internal penetration tests of Active Directory environments.
##### MITM Against Encrypted Traffic
options include:
- **SSL Stripping** downgrades connection to HTTP. Attacker connects to server with HTTPS then serves HTTP to victim
- **Fake Certificates** attacker presents their own certificate establishing separate encrypted connections with both parties. works if victim accepts an invalid cert warning or the attacker compromises a CA
- **Compromised or Rogue CA** attacker controls a trusted CA or obtains a fraudulent cert from one, they can generate a valid-looking certificate for any domain
##### Defense Against MITM
quick list of mitigations
- HTTPS Everywhere: use HTTPS by default and browsers typically mark http as "not secure"
- HSTS (HTTP with Strict Transport Security)  tells browsers to only connect via HTTPS for a specified period. Once a browser has seen an HSTS header for a domain, it will refuse to connect over HTTP, preventing SSL stripping attacks. Many major sites are also on the HSTS preload list, meaning browsers ship with knowledge that these sites should only be accessed via HTTPS.
- **Certificate Transparency** requires CAs to log all issued certificates to public, auditable logs. This makes it much harder for attackers to obtain fraudulent certificates without detection. Browsers can check these logs and reject certificates that are not properly logged.
- **Certificate Pinning**  allows applications to specify exactly which certificates or public keys are valid for their connections. This is common in mobile apps and prevents attacks even if a CA is compromised. However, it makes certificate rotation more complex.
- **DANE (DNS-based Authentication of Named Entities)**  uses DNSSEC to publish certificate information in DNS records, providing an alternative trust path that does not rely solely on the CA system.
