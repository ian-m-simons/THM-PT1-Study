### Passive vs Active Recon

##### Passive Reconnaissance
Passive reconnaissance relies exclusively on publicly available information. There is zero interaction with the target. common activities include:
- Querying public DNS records from open resolvers
- searching certificate transparency logs for subdomains and issued certificates
- reviewing job postings on linkedin, indeed, company career pages 
- Reading public news, press releases or leaked documents on paste sites
- Checking exposed devices via search engines like shodan or censys
- Scanning public GitHub Repos for hardcoded creds or configs
##### [[Active Reconnaissance]]
Actively engaging with the target. Probes can be logged, detected, or blocked. think of it like testing locks cameras alarms etc. Carries a higher risk of alerting the target. Without explicit authorization, it could potentially lead to legal issues. Passive recon is stealthier and often the first practical step. Common Active reconnaissance steps include
- Sending packets to discover live hosts (pings, ARP requests)
- Port scanning or service enumeration (nmap or masscan)
- Interacting with Web applications or APIs (puzzing endpoints, directory brute-forcing)
- Social Engineering attempts (phishing, vishing, pretecting phone calls)
- Physical approaches (tailgating, posing as vendor)
note if you interact with personnel it counts as active recon ie at a social function talk and learn about their tech stack.
### Whois

##### WHOIS
WHOIS is a query/response protocol defined in RFC 3912. WHOIS servers listen on TCP port 43 and provide registration details for domain names. Records are maintained by the domain registrar. 
Typically a response will include the following (though some items may be redacted)
- Registrar -- the company that registered the domain
- Registrant contact information -- Name, org, address, phone, email (though this is usually redacted)
- Dates -- creation (registration), updated (last change), and expiration (renewal deadline).
- Name Servers -- DNS servers authoritative for the domain
- Status codes -- fore example, `clientTransferProhibited` indicates the domain is locked against unauthorized transfers
- Abuse contacts  -- the registrars email and phone for reporting issues 
services like whoxy.com provide historical snapshots which can be revealing

##### RDAP
Whois is being replaced by RDAP as of 28 Jan 2025. RDAP (Registration Data Access Protocol) is the modern successor of Whois. it uses HTTPS and returns JSON. It supports internationalization, provides better privacy controls, and aligns with current data protection rules. 

##### Example usage

to query whois enter `whois <domain_name>`

to user RDAP use curl as shown 
`curl -s https://rdap.verisign.com/com/v1/domain/tryhackme.com | jq`

##### alternatives

you can also use online alternatives ie https://whois.icann.org https://lookup.icann.org (modern RDAP) https://www.whoxy.com (historical WHOIS snapshots, free limited use)

### NSLOOKUP and DIG
nslookup and dig are both fully passive because you're reaching out to a public/open resolver not the target. dig is the modern preferred option, it provides cleaner output, displays TTL values by default, and is more relaible for complex queries and scripting. 
##### NSLOOKUP
`nslookup <domain-name>` is used to perform a simple lookup using your default resolver
`nslookup -type=<record-type> <domain-name> [SERVER]` secifies a record type and optionally the DNS server
you should already generally know DNS records but to review 

| Query Type | Result                                                                                    |
| ---------- | ----------------------------------------------------------------------------------------- |
| A          | IPv4 address(es) for the domain                                                           |
| AAAA       | IPv6 address(es) for the domain                                                           |
| CNAME      | Canonical Name: an alias that poinst one domain name to another                           |
| MX         | Mail Servers: the servers responsible for handling email for the domain                   |
| SOA        | Start of Authority: the primary name server, admin email, and zone serial number          |
| TXT        | Text Records: arbitrary text, commonly used for SPF, DKIM, DMARC, and domain verification |

##### DIG
dig is the modern, preferred DNS query tool.

run with `dig [@SERVER] <Domain-name> [TYPE]`
overall much nicer

### DNS Dumpster
typical DNS lookups only resolve names you already know, they don't enumerate subdomains. subdomains are important because they often expose forgotten or vulnerable services, shadow IT,  misconfigured applications, and additional attack surface

in passive recon, we can't touch the target's infrastructure so we have to use public OSINT sources to find these subdomains. A well-known free tool is DNSDumpster. it aggregates public DNS data from sources such as search engine caches, zone transfer databases and certificate records. It does not brute-force enumeration so that it remains fully passive and the potential at least exists for it to miss things. results include subdomains and hosts, resolved IPs with geolocation, MX, TXT, and CNAME records, and visual maps showing the relationship between these
##### Certificate Transparency (CT) Logs
most effective passive subdomain discover is certificate transparency logs which are accessible through crt.sh. Certificate transparency is a public framework (mandatory since 2015) that records every SSL/TLS certificate issued by participating Certificate Authorities. Each cert contains a Subject alternative name (SAN) field listing domains and subdomains it covers. in these searches `%` acts as a wildcard so you can search `%.imcu.com` crt will typically have 10-100 times more subdomains than DNSdumpster alone. other options include securityTrails (free limited searches) and command-line tools like subfinder, which aggregate multiple passive sources. 

### Shodan.io 

[shodan.io](https://shodan.io) is a search engine for internet connected devices. It continually scans the public internet, collects banners and responses from open ports and services, and indexes them for search. Google indexes for web pages, shodan focuses on devices: servers, IoT, equipment, cameras, routers, ICS, and more. 
searchtips 

| dork | demo | use |
| --- | --- | --- |
|hostname|`hostname: tryhackme.com`| matches a specific hostname|
|org|`org:"TryHackMe"`|filters by organization name|
|port|`port:443 country:US`| filters by port and country|
|http.component|`http.component:"wordpress"`|identifies tech stack if exposed|

[Censys.io](https://Censys.io) provides similar host and certificate data. can serve as a useful complement when cross-referencing results