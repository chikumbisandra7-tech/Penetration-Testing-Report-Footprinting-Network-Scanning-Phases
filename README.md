# Penetration Testing Report

## Footprinting, Reconnaissance & Network Scanning

**W2-PM1 & W2-PM5 | Cybersecurity | Networkwalks**

| Detail | Information |
|---|---|
| **Pentester** | Sandra Chkumbi |
| **Program / Batch** | B083 – Networkwalks |
| **Date** | 15–16 September 2026 |
| **Modules** | W2-PM1 – Multiple Kali Linux Tools (Footprinting & Reconnaissance)<br>W2-PM5 – Zenmap (Network Scanning) |
| **Client / Target** | Networkwalks |
| **Permission** | Written permission secured |
| **Phases Covered** | Phase 1: Reconnaissance & Footprinting<br>Phase 2: Network Scanning |

---

## ⚠️ Liability Disclaimer

These activities were performed only on systems and devices where written permission had been secured. This repository is for educational and research purposes only. Do not use the material to access or test systems without authorization. Unauthorized access to computer systems may constitute a criminal offence and can result in serious legal and professional consequences.

---

## 📖 Introduction

This report documents two practical modules completed during Week 2 of the cybersecurity programme at Networkwalks (Cohort B083):

- **W2-PM1:** Footprinting & Reconnaissance using multiple Kali Linux tools
- **W2-PM5:** Network Scanning using Zenmap

Footprinting is the first phase of a penetration test and involves gathering publicly available information about a target to understand its infrastructure, technologies, and potential attack surface without actively exploiting systems.

Network scanning builds on reconnaissance by probing an authorized network to discover live hosts, open ports, and running services.

For W2-PM1, six Kali Linux command-line tools were used against the `networkwalks.com` domain. For W2-PM5, Zenmap, the graphical front-end for Nmap, was used to perform a ping scan across a `/24` subnet in a controlled VirtualBox environment.

---

## 🛠️ Tools Used

| Tool | Purpose |
|---|---|
| **Kali Linux (VirtualBox)** | Operating system used for reconnaissance and scanning |
| **WHOIS** | Retrieves domain registration details such as registrar, dates, name servers, and registrant information |
| **WhatWeb** | Fingerprints web technologies, CMS, plugins, JavaScript frameworks, and IP information |
| **Nslookup** | Resolves domain names to IP addresses using DNS |
| **curl -I** | Inspects HTTP response headers, cookies, caching information, and exposed endpoints |
| **wafw00f** | Detects Web Application Firewalls protecting a website |
| **dnsrecon** | Enumerates DNS records including NS, SOA, MX, TXT/SPF, and SRV records |
| **Zenmap (Nmap GUI)** | Performs network host discovery and visualizes network topology |

---

# 🔎 W2-PM1: Footprinting & Reconnaissance

Passive reconnaissance was performed against `networkwalks.com` using six Kali Linux tools.

## 1. WHOIS

**Command:**

```bash
whois networkwalks.com
```

### Key observations

- Domain registered: **6 November 2019**
- Expiry date: **6 November 2027**
- Registrar: **GoDaddy.com, LLC**
- Name servers: **NS6135.HOSTGATOR.COM** and **NS6136.HOSTGATOR.COM**
- Registrant identity protected by **Domains By Proxy, LLC**
- DNSSEC was reported as not enabled
- Several registrar lock/status flags were present

### Security relevance

WHOIS information can provide an initial picture of domain ownership, hosting arrangements, and registration details. DNSSEC status is also a relevant security observation.

---

## 2. WhatWeb

**Command:**

```bash
whatweb networkwalks.com
```

### Key observations

WhatWeb identified:

- Apache web server
- WordPress 7.1
- WP Download Manager 3.3.58
- Bootstrap 7.1
- jQuery 3.7.1
- Google Tag Manager
- HTML5
- Open Graph Protocol
- HTTP 301 redirect from HTTP to HTTPS
- Server IP: `192.232.216.135`
- Contact email exposed in page metadata

### Security relevance

Publicly visible technology and version information can help a tester identify software that should be checked against known security advisories.

---

## 3. Nslookup

**Command:**

```bash
nslookup networkwalks.com
```

### Key observations

The domain resolved to:

```text
192.232.216.135
```

The lookup used Google's public DNS resolver:

```text
8.8.8.8
```

The result was non-authoritative and confirmed the IP address identified by WhatWeb.

---

## 4. HTTP Header Inspection with curl

**Command:**

```bash
curl -I https://networkwalks.com
```

### Key observations

The response included:

- `HTTP/2 200 OK`
- Apache server information
- WordPress REST API links:
  - `/wp-json/`
  - `/wp-json/wp/v2/pages/53`
- `__wpdm_client` session cookie with Secure and HttpOnly flags
- `x-nginx-cache` header
- Permissions-Policy references to third-party services including Google, Cloudflare, reCAPTCHA, and hCaptcha

### Security relevance

The presence of WordPress REST API endpoints can provide additional information for enumeration, depending on the site's configuration.

---

## 5. WAF Detection with wafw00f

**Command:**

```bash
wafw00f networkwalks.com
```

### Key observation

The tool identified **ModSecurity (SpiderLabs)** as the Web Application Firewall protecting the website.

### Security relevance

Identifying a WAF is useful during authorized security assessments because it helps a tester understand the defensive controls in front of an application.

---

## 6. DNS Enumeration with dnsrecon

**Command:**

```bash
dnsrecon -d networkwalks.com
```

### Key observations

The enumeration identified:

- SOA record
- HostGator name servers
- A record: `192.232.216.135`
- MX record: `mail.networkwalks.com`
- TXT/SPF records
- Google Site Verification record
- SRV records associated with cPanel email discovery
- BIND version information

The mail host resolved to the same IP address as the web server.

### Security relevance

DNS records can reveal hosting architecture and other infrastructure information useful during authorized reconnaissance.

---

# 🌐 W2-PM5: Network Scanning with Zenmap

The network scanning exercise was performed in a controlled VirtualBox NAT environment using the `10.0.0.0/24` subnet.

## 7. Zenmap Ping Scan

**Command:**

```bash
nmap -sn 10.0.0.0/24
```

### Scan configuration

- **Target:** `10.0.0.0/24`
- **Scan type:** Ping scan / host discovery
- **Addresses covered:** 256
- **Environment:** Internal VirtualBox NAT network
- **Scan time:** 3.09 seconds
- **Timestamp:** 16 September 2026 at 15:38

### Live hosts discovered

| IP Address | Observation |
|---|---|
| `10.0.0.1` | Host up; latency approximately 0.0051 seconds; QEMU virtual NIC identified |
| `10.0.0.2` | Host up; Kali Linux VM used as the scanner |

The scan demonstrated how host discovery can identify reachable systems before more detailed authorized scanning.

---

## 8. Zenmap Topology View

After the ping scan, the Zenmap **Topology** tab was used to visualize the discovered network.

The topology view displayed:

- `10.0.0.1`
- `10.0.0.2`
- Their relationship within the `10.0.0.0/24` network
- The topology legend and connection information

This provides a quick visual representation of the network structure discovered during the scan.

---

# ⚠️ Risk Analysis

> **Important:** The findings below are observations from footprinting and network-scanning exercises. They are **not confirmed vulnerabilities**. No exploitation, vulnerability scanning, or active attack was performed.

| # | Finding | Evidence / Observation | Potential Impact | Risk |
|---|---|---|---|---|
| 1 | CMS and plugin versions publicly exposed | WhatWeb identified WordPress 7.1 and WP Download Manager 3.3.58 | Versions can be checked against public vulnerability databases | Medium |
| 2 | WordPress REST API endpoint exposed | `/wp-json/` and `/wp-json/wp/v2/pages/53` appeared in headers | May assist information gathering and enumeration | Medium |
| 3 | DNSSEC not enabled | WHOIS and dnsrecon reported unsigned DNS | DNS spoofing/cache-poisoning risks may be relevant | Medium |
| 4 | Mail and web server share the same IP | MX and A records both resolved to `192.232.216.135` | A compromise affecting the shared host could affect multiple services | Medium |
| 5 | DNS infrastructure details exposed | BIND, cPanel SRV, and SPF information identified | May assist infrastructure profiling | Medium |
| 6 | Live hosts identifiable through ping scan | `10.0.0.1` and `10.0.0.2` discovered | Provides initial information for further authorized assessment | Low |
| 7 | Virtual gateway MAC address exposed | `52:54:00:12:35:00` identified | May provide additional local-network information | Low |
| 8 | Server IP and WAF identifiable | `192.232.216.135` and ModSecurity identified | Enables more targeted authorized testing | Low |
| 9 | HTTP technical information exposed in headers | Apache, cache headers, and cookie names visible | Assists technology fingerprinting | Low |

---

# 💡 Recommendations

Based on the observations documented in the report:

1. **Enable DNSSEC** to improve the integrity and authenticity of DNS responses.
2. **Review access to the WordPress REST API** and restrict unnecessary unauthenticated information exposure.
3. **Reduce unnecessary CMS and plugin version disclosure** in publicly visible metadata.
4. **Consider separating web and mail services** where appropriate to reduce shared-hosting exposure.
5. **Review DNS records** and remove unnecessary infrastructure information where possible.
6. **Keep WordPress, plugins, and themes updated** according to current security advisories.
7. **Regularly update and tune ModSecurity rules** to maintain effective web application protection.
8. **Use network segmentation and host-based firewalls** to reduce unnecessary network exposure.
9. **Review ICMP exposure** on production systems according to the organization's security requirements.
10. **Always obtain explicit written authorization** before performing reconnaissance or network scanning.

---

# 📊 Key Learning Outcomes

This practical demonstrated:

- Domain and registration reconnaissance using WHOIS
- Web technology fingerprinting using WhatWeb
- DNS resolution using Nslookup
- HTTP header inspection using `curl`
- WAF identification using wafw00f
- DNS record enumeration using dnsrecon
- Host discovery using Nmap/Zenmap
- Basic network topology visualization
- Security risk identification and documentation
- Translating reconnaissance observations into security recommendations

---

# 🖼️ Evidence Collected



1. **WHOIS** – `whois networkwalks.com
2. <img width="1577" height="717" alt="427913" src="https://github.com/user-attachments/assets/4f41551a-bd68-4830-9cb7-ea74350d5669" />
`
3. **WhatWeb** – `whatweb networkwalks.com`
   <img width="1382" height="777" alt="427912" src="https://github.com/user-attachments/assets/30b6e1ad-d35c-44c0-ac6c-a5bc40419b3b" />

5. **WhatWeb + Nslookup** – `nslookup networkwalks.com`
   <img width="1147" height="717" alt="427911" src="https://github.com/user-attachments/assets/afb88123-5f14-4b3a-8877-9d020514b8fa" />

7. **curl** – `curl -I https://networkwalks.com`
  <img width="1137" height="762" alt="427916" src="https://github.com/user-attachments/assets/962f3ee6-e4ad-4f5f-9e8e-72ebbbb6880e" />
 
9. **wafw00f** – `wafw00f networkwalks.com`
    <img width="1175" height="792" alt="427915" src="https://github.com/user-attachments/assets/9c00ff25-4587-43af-9dd0-10eb3bf59985" />

11. **dnsrecon** – `dnsrecon -d networkwalks.com`
12. **Zenmap Ping Scan** – `nmap -sn 10.0.0.0/24`
    <img width="1352" height="755" alt="430495" src="https://github.com/user-attachments/assets/fe54c9c0-ae8f-4d8d-a6c4-32c23420dbd6" />

14. **Zenmap Topology View**
15. <img width="1248" height="795" alt="430494" src="https://github.com/user-attachments/assets/b4abe2bf-f742-438d-ab92-8530558b0246" />


> **Suggested GitHub structure**
>
> ```text
> penetration-testing-report/
> ├── README.md
> ├── evidence/
> │   ├── 01-whois.png
> │   ├── 02-whatweb.png
> │   ├── 03-nslookup.png
> │   ├── 04-curl-headers.png
> │   ├── 05-wafwoof.png
> │   ├── 06-dnsrecon.png
> │   ├── 07-zenmap-ping-scan.png
> │   └── 08-zenmap-topology.png
> └── report/
>     └── penetration-testing-report.pdf
> ```

---

# 👤 Author

**Sandra Chkumbi**

Cybersecurity intern – B083

**Programme:** Cybersecurity at Networkwalks  
**Week:** 02  
**Modules:** W2-PM1 & W2-PM5  
**Cohort:** B083

🔗 LinkedIn: https://www.linkedin.com/in/sandra-chikumbi-536160295?utm_source=share_via&utm_content=profile&utm_medium=member_android
---

## 📌 Scope & Authorization

All reconnaissance and network-scanning activities documented in this repository were conducted within the authorized scope of the Networkwalks educational programme.

**No exploitation, vulnerability scanning, or active attack was performed.**

---

> **Educational cybersecurity project — Networkwalks, Cohort B083**
