# Penetration Testing Report
Footprinting, Network Discovery & OSINT
Program: Cybersecurity --- Networkwalks
Pentester: Aylia Zulfiqar
Batch: B084E-Networkwalks
Report date: 13 November 2026
Operating system: Kali Linux

## Overview
This repository documents three cybersecurity practical activities:
1. Footprinting and reconnaissance of networkwalks.com using six
   Kali Linux tools.
2. Local network discovery using Zenmap/Nmap on the lab subnet
   192.168.56.0/24.
3. Public-source OSINT collection for microsoft.com using
   theHarvester.
The work focuses on information gathering and network visibility. It
does not document exploitation or vulnerability validation.
Scope and Authorization
- Reconnaissance against networkwalks.com was stated by the report
  author to be authorized.
- Network discovery was performed against the author's own lab subnet:
  192.168.56.0/24.
- theHarvester collection for microsoft.com is documented as passive
  public-source OSINT only. This report does not claim authorization
  to scan or test Microsoft's systems.
All testing should remain within the approved scope and applicable law.

## Tools Used
  Tool                                Purpose
  WHOIS                               Domain registration and nameserver
information

  WhatWeb                             Website technology fingerprinting

  NSLookup                            DNS name resolution

  cURL (-I)                         HTTP response-header inspection

  WAFW00F                             Web application firewall detection

  DNSRecon                            DNS record enumeration

  Zenmap / Nmap                       Local network host discovery and
topology visualization
theHarvester 4.11.1                 Public-source OSINT collection

**1. Footprinting & Reconnaissance --- networkwalks.com**

WHOIS
- Registrar: GoDaddy.com, LLC
- Creation date: 2019-11-06T22:51:46Z
- Updated date: 2025-11-12
- Expiry date: 2027-11-06
- Registrant privacy: Domains By Proxy, LLC
- Registry nameservers reported: NS6135.HOSTGATOR.COM,
  NS6136.HOSTGATOR.COM
- Additional nameservers shown in registrar WHOIS:
  NS29.DOMAINCONTROL.COM, NS30.DOMAINCONTROL.COM
- DNSSEC: unsigned
Note: The nameserver lists differed between the registry and
registrar WHOIS outputs. The active authoritative delegation should be
verified before drawing conclusions.

WhatWeb
The supplied WhatWeb output reported:
- HTTP redirected to HTTPS.
- HTTPS returned 200 OK.
- Server fingerprint: Apache.
- Resolved address: 192.232.216.135.
- Page title: Networkwalks Academy.
- Tool-reported fingerprints included WordPress 7.1.4, WordPress
  Download Manager 3.3.76, Bootstrap 7.1.4, jQuery 3.7.1, Google
  Tag Manager and HTML5.
These are tool-reported fingerprints and are not independent
confirmation of deployed component versions.

NSLookup
Command:
nslookup networkwalks.com 1.1.1.1
Observed result:
networkwalks.com -> 192.232.216.135

cURL
Command:
curl -I http://networkwalks.com
Observed result: HTTP/1.1 301 Moved Permanently, redirecting to
https://networkwalks.com/.

Other reported response details included Apache,
Cache-Control: max-age=3600,
X-Redirect-By: WordPress - Really Simple Security, an HttpOnly
__wpdm_client cookie, Referrer-Policy: no-referrer-when-downgrade,
X-Endurance-Cache-Level: 0, X-nginx-cache: WordPress and an HTML
UTF-8 content type.

**Limitation:** This evidence is from the HTTP response. It does not
establish which headers are present or absent on the HTTPS response.

WAFW00F
Command:
wafw00f https://networkwalks.com
Observed result: WAFW00F 2.4.2 identified ModSecurity
(SpiderLabs). The tool reported two requests.

DNSRecon
The supplied output reported:
- SOA: ns6135.hostgator.com --- 50.87.144.87
- NS: ns6135.hostgator.com --- 50.87.144.87
- NS: ns6136.hostgator.com --- 192.232.216.131
- A: networkwalks.com --- 192.232.216.135
- MX: mail.networkwalks.com --- 192.232.216.135
- SPF TXT:
  v=spf1 +a +mx +ip4:50.87.144.87 +include:websitewelcome.com ~all
- A Google site-verification TXT record was also reported.
- _autodiscover._tcp.networkwalks.com pointed to
  cpanelemaildiscovery.cpanel.net; the output listed eight
  SRV-related address/port records on port 443.
- DNSRecon reported the BIND version string 9.16.23-RH for both DNS
  servers.
- DNSSEC query: no answer; WHOIS also reported DNSSEC as unsigned.
The 8 Records Found message appeared after SRV enumeration and is
treated as the SRV enumeration count, not the total number of all DNS
records.

**2. Local Network Discovery**
--- Zenmap
Target subnet: 192.168.56.0/24

The supplied Kali Linux configuration showed:

Interface     IPv4 address          Notes
lo            127.0.0.1/8           Loopback
eth0          10.0.0.2/24           Default route via 10.0.0.1
eth1          192.168.56.103/24     Interface on the scanned subnet

The target subnet corresponds to eth1 (192.168.56.103/24).

**3. OSINT Enumeration** --- theHarvester
TheHarvester version 4.11.1 was used against microsoft.com. This is
documented as passive public-source OSINT only.
Baidu source
Command:
theHarvester -d microsoft.com -b baidu -l 1000
Observed result:
- IP addresses: 0
- Email addresses: 0
- People: 0
- Hosts: 0
All sources
Command:
theHarvester -d microsoft.com -b all -l 50
The supplied output reported:
- 3 email addresses
  - dotnet-docker-bot@microsoft.com
  - opencode@microsoft.com
  - secure@microsoft.com
- 9,964 hosts reported by the tool.
The output also showed multiple sources unavailable because API keys
were missing or invalid. Examples included BeVigil, Bitbucket,
BufferOver, BuiltWith, Brave Search, Censys, Criminal IP, Dehashed,
DNSDumpster, FullHunt, GitHub, Have I Been Pwned, Hunter, IntelX,
Netlas, Onyphe, PentestTools, ProjectDiscovery, RocketReach,
SecurityScorecard and SecurityTrails. Mojeek reported using default
scraping mode.

Interpretation: The command used -l 50, while the aggregated
output reported 9,964 hosts. The limit should not be interpreted as a
verified total-output cap. The count is tool-reported, not a manually
verified count of unique or active hosts. Missing API keys also mean the
collection was incomplete and source-dependent.

Key Observations
Area                                Observation
Web fingerprinting                  Apache and WordPress-related
technology fingerprints were
reported.

DNS                                 A, MX, NS, SOA, TXT/SPF and SRV
information was returned.

DNSSEC                              WHOIS reported DNSSEC unsigned;
DNSRecon returned no DNSSEC answer.

WAF                                 WAFW00F identified ModSecurity
(SpiderLabs).

Local network                       Subnet 192.168.56.0/24 and Kali
interface 192.168.56.103/24 were
confirmed; host-level scan evidence
remains to be added.
OSINT                               theHarvester reported 3 email
addresses and 9,964 hosts for the
all-sources run, with multiple
API-key limitations.
These are reconnaissance observations, not confirmed exploitable
vulnerabilities.

## Recommendations
1. Validate technology fingerprints and maintain supported, patched CMS
   and plugin versions.
2. Review HTTP and HTTPS response headers separately.
3. Reconcile the nameserver differences between WHOIS outputs and
   active DNS delegation.
4. Assess whether DNSSEC should be deployed for the domain.
5. Review DNS records and remove obsolete service-discovery entries.
6. Confirm the reported BIND version and minimize unnecessary
   version-banner disclosure where practical.
7. Keep ModSecurity rules and components updated and review WAF logs.
8. Complete and document the Zenmap scan; verify discovered devices
   with their owners.
9. Treat theHarvester results as OSINT leads, not as a verified
   inventory of active systems or accounts.
10. Keep all future reconnaissance and scanning within written
    authorization and scope.

## Evidence Checklist

## Conclusion
The exercises demonstrate how domain registration data, web technology
fingerprints, DNS records, HTTP responses, WAF detection and
public-source OSINT can contribute to an initial security profile. The
local network activity confirmed the selected subnet and interface
configuration, but host-level Zenmap results and the topology PDF still
need to be attached. Findings should be validated and interpreted within
the authorized scope.

Author: Aylia Zulfiqar
Program / Batch: B084E-Networkwalks
Report date: 13 November 2026
