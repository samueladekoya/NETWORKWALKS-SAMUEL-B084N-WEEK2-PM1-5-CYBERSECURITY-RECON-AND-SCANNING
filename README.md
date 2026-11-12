# NETWORKWALKS-SAMUEL-B084N-WEEK2-PM1-5-CYBERSECURITY-RECON-AND-SCANNING
Documentation and evidence for Week 2 internship projects: External footprinting (Kali Linux) and local network discovery (Zenmap).

# PENETRATION TESTING REPORT
## FOOTPRINTING & NETWORK SCANNING PHASES
**W2-PM-FINAL | CYBERSECURITY | NETWORKWALKS**

| Field | Details |
| :--- | :--- |
| **Pentester Name** | Samuel Adekoya |
| **Program/Batch** | B084-Networkwalks |
| **Date** | 12 November 2026 |
| **Modules Completed** | W2-PM1 (Multiple Kali Tools)<br>W2-PM5 (Zenmap Scanning) |
| **Client/Target** | 1. Networkwalks (secured written permission already)<br>2. My own local LAN Network |
| **Permission Secured from Client?** | Yes |
| **Phases Covered** | Phase 1: Reconnaissance & Footprinting<br>Phase 2: Scanning & Network Discovery |

---

## 1. Liability Disclaimer
I have performed these activities only on the systems & devices where I had secured written permission or the devices/systems that I own myself. All these materials are for education and research purposes only. Do not use anything from here to break the law. The instructor, the authors, and Networkwalks are not responsible for what you do with this knowledge. Every action you take is your own responsibility. Misuse can lead to criminal charges, heavy fines, loss of your job, and a permanent record. In most countries, unauthorized access is a crime even when nothing is damaged.

---

## 2. Introduction
This report covers footprinting the `networkwalks.com` domain using multiple Kali Linux tools (W2-PM1) and scanning my own local network with Zenmap (W2-PM5). One module covers the footprinting phase and the other covers the scanning phase, so together they show how an attacker moves from gathering public information to mapping live hosts on a network. It is the Week 2 part of my ongoing internship program at Networkwalks.

All commands were run in Kali Linux (footprinting) and on a Windows PC with Zenmap installed (scanning). Every step below includes the exact command used, the result observed, an inline screenshot as evidence, and a short note on why the finding matters from an attacker's point of view.

---

## 3. Tools Used

| Tool | Purpose |
| :--- | :--- |
| **Kali Linux & Windows** | Operating systems used for reconnaissance and scanning activities |
| **WHOIS** | Find domain registration details (owner, dates, name servers) |
| **whatweb** | Fingerprint web technologies (server, CMS, plugins, IP) |
| **nslookup** | Resolve the domain name to its IP address using DNS |
| **curl -I** | Read the HTTP response headers of the website |
| **wafw00f** | Detect whether a Web Application Firewall protects the site |
| **dnsrecon** | Enumerate all DNS records (NS, MX, SPF, TXT, SRV) |
| **Zenmap (Nmap GUI)** | Scan the local subnet to find live hosts, IPs, and MAC addresses |
| **Windows CMD** | Local IP and MAC address identification |

---

## 4. Activities Performed

### 4.1 Footprinting & Reconnaissance (W2-PM1)
I performed reconnaissance against the `networkwalks.com` domain using six Kali Linux tools: WHOIS, WhatWeb, Nslookup, Curl, Wafw00f, and DNSRecon. Each tool was used to collect a different type of public information about the target.

#### Task 1: Domain Registration Lookup (`whois`)
* **Command:** `whois networkwalks.com`
* **Observation:** Obtained publicly available domain registration records, identifying GoDaddy as the registrar, creation and expiration dates, abuse contacts, and HostGator name servers (`ns6135.hostgator.com`, `ns6136.hostgator.com`).
* **Attacker Perspective:** Reveals the hosting provider and administrative contact details, aiding infrastructure mapping and social engineering planning.

![WHOIS Output](Screenshot%20(594).png)

---

#### Task 2: Web Technology Fingerprinting (`whatweb`)
* **Command:** `whatweb networkwalks.com`
* **Observation:** Identified the Apache web server, WordPress CMS (`7.1.4`), WP Download Manager plugin (`3.3.76`), Bootstrap, jQuery (`3.7.1`), contact email (`info@networkwalks.com`), and server IP address (`192.232.216.135`).
* **Attacker Perspective:** Exposes exact software and plugin versions that can be cross-referenced against CVE databases for known vulnerabilities.

![WhatWeb Output](Screenshot%20(599).png)

---

#### Task 3: DNS IP Resolution (`nslookup`)
* **Command:** `nslookup networkwalks.com`
* **Observation:** Resolved the domain name directly to its non-authoritative IPv4 address: `192.232.216.135`.
* **Attacker Perspective:** Allows direct scanning of the underlying server infrastructure and reverse-IP lookups for co-hosted sites.

![Nslookup Output](Screenshot%20(595).png)

---

#### Task 4: HTTP Response Header Inspection (`curl -I`)
* **Command:** `curl -I https://networkwalks.com`
* **Observation:** Inspected HTTP/2 response headers, confirming the Apache server banner, caching headers, `HttpOnly` cookie attributes, and the exposed WordPress REST API endpoint (`/wp-json/`).
* **Attacker Perspective:** Reveals server stack details and API entry points without needing to render the full webpage.

![Curl Output](Screenshot%20(596).png)

---

#### Task 5: Web Application Firewall Detection (`wafw00f`)
* **Command:** `wafw00f networkwalks.com`
* **Observation:** Detected that `networkwalks.com` is protected behind a **ModSecurity (SpiderLabs)** Web Application Firewall.
* **Attacker Perspective:** Indicates that automated or noisy payloads will likely be blocked or logged, requiring evasion techniques or stealthier testing.

![Wafw00f Output](Screenshot%20(597).png)

---

#### Task 6: DNS Record Enumeration (`dnsrecon`)
* **Command:** `dnsrecon -d networkwalks.com`
* **Observation:** Enumerated SOA and NS records, Bind DNS server software versions (`9.16.23-RH`), MX mail records (`mail.networkwalks.com`), SPF/TXT validation records, and cPanel autodiscovery SRV records.
* **Attacker Perspective:** Maps the broader email and hosting perimeter, uncovering auxiliary services that may serve as secondary entry points.

![DNSRecon Output](Screenshot%20(598).png)

---

### 4.2 Network Scanning with Zenmap (W2-PM5)
For the second activity, I used Zenmap on Windows to perform active network discovery on my local LAN subnet.

#### Tasks 1–6: Local Subnet Identification & Ping Scan
* **Commands Used:** 
  * Windows CMD: `ipconfig` and `ipconfig /all` (to identify local IP, subnet mask, and local MAC address)
  * Zenmap Ping Scan: `nmap -sn 10.205.137.0/24`
* **Observation:** Identified active live hosts on the local network segment along with their corresponding IPv4 addresses and MAC hardware addresses.
  * **Live Hosts Count:** *(5)*
  * **Discovered IP Addresses:** *(`10.205.137.62`, `10.205.137.65`, `10.205.137.182`, `10.205.137.239`, `10.205.137.7`)*
  * **Discovered MAC Addresses:** *(`8A:94:EB:CB:33:37`, `A2:AA:30:ED:87:51`,`CA:AA:8E:9F:2C:EB`, `A6:5B:45:59:4D:03`, `38-BA-F8-05-F3-25`)*

![Zenmap Ping Scan Output](Screenshot%20(589).png)

---

#### Task 7: Network Topology Visualization & PDF Export
* **Observation:** Navigated to the **Topology** tab in Zenmap, enabled the Legend to interpret host node icons and connection lines, and exported the visual network topology map in PDF format.

![Zenmap Topology Output](Screenshot%20(593).png)

---

## 5. Risk Analysis / Impact

Based on the information collected during the footprinting and network scanning activities, I identified the following observations and potential risks:

| # | Risk / Finding | Evidence / Observation | Potential Impact | Risk Level |
| :-: | :--- | :--- | :--- | :---: |
| **1** | Web technology information exposed | WhatWeb identified WordPress and WP Download Manager versions | Attackers may use exposed technology/version information to identify software requiring further security review | **Medium** |
| **2** | Server IP address identifiable | Nslookup resolved the domain to `192.232.216.135` | Provides information about the direct network location of the web service | **Low** |
| **3** | HTTP technical information exposed | Curl returned HTTP response headers and exposed `/wp-json/` | May assist technology fingerprinting and further endpoint enumeration | **Low** |
| **4** | WAF technology identifiable | Wafw00f identified ModSecurity (SpiderLabs) | Reveals information about the web application’s security architecture | **Low** |
| **5** | DNS infrastructure information exposed | DNSRecon identified DNS, mail, Bind version, and service-related records | DNS information can help build a broader infrastructure profile | **Medium** |
| **6** | Multiple live hosts visible on local network | Zenmap identified live hosts and MAC addresses on the local subnet | Unknown or unauthorized devices may potentially be present on a network | **Medium** |

> **Note:** The risks above are observations from passive/active reconnaissance and host discovery exercises, not confirmed vulnerabilities. No exploitation or vulnerability validation was performed as part of these two modules. Further authorized security testing would be required to confirm any actual exploitability.

---

## 6. Recommendations

Based on the observations from these activities, I recommend the following security improvements:

* **Review Publicly Exposed Technology Information:** Organizations should regularly review what information about their web technologies, CMS, and plugins is publicly visible.
* **Keep Software Updated:** CMS platforms, plugins, and other web technologies should be regularly updated and reviewed against current security advisories.
* **Review HTTP Headers:** HTTP response headers should be reviewed to determine whether unnecessary technical information or API endpoints are being exposed.
* **Review DNS Records Regularly:** DNS records should be checked periodically to ensure that only required information and services are publicly exposed.
* **Properly Configure and Monitor the WAF:** Keep the WAF (ModSecurity) enabled and tuned, since it already helps block automated and naive attacks.
* **Perform Regular Internal Network Discovery:** Organizations should periodically scan their own networks to identify active devices.
* **Investigate Unknown Devices:** Any unexpected device discovered during network scanning should be investigated and verified.
* **Maintain Network Documentation:** Network topology and device information should be documented and updated regularly.
* **Perform Security Testing with Authorization:** Reconnaissance and scanning should only be performed against systems and networks where appropriate written authorization has been provided.

---

## 7. Conclusion

During Week 2 of my Cybersecurity & Ethical Hacking internship, I completed practical activities covering footprinting, reconnaissance, and network scanning.

In the footprinting activity (W2-PM1), I used six Kali Linux tools to collect information about the target domain. I learned how `whois` provides domain registration details, `whatweb` identifies web technologies, `nslookup` resolves domain names, `curl` inspects HTTP headers, `wafw00f` detects a WAF, and `dnsrecon` enumerates DNS infrastructure records.

In the network scanning activity (W2-PM5), I used Zenmap to identify my local network configuration and discover active hosts. I also collected IP and MAC address information and generated a visual network topology.

These exercises demonstrated that information gathering is a foundational phase of cybersecurity. Even before attempting to test for vulnerabilities, a security professional can learn a significant amount about an environment by analyzing publicly available information and network responses. Clear documentation and strict adherence to authorized scope remain essential throughout every phase of an engagement.
