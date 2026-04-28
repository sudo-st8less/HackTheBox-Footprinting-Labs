### CPTS / HTB Penetration Tester Path <br>
### Footprinting - Enumeration Principles, Methodology, Domain Info, Cloud, Staff <br>
<mark>hook it up with a &#x2B50; if this helps.</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>

<br>
<br>

---

### Enumeration Principles / Methodology



Enumeration is active recon; OSINT is passive. Goal: not to break in, but to map all the ways in. Three core principles:
1. There's more than meets the eye — consider all points of view.
2. Distinguish between what we see and don't see.
3. There's always more info — understand the target.

6-layer enumeration methodology (boundaries to pass):
| # | Layer | Information |
|---|---|---|
| 1 | Internet Presence | Domains, subdomains, vHosts, ASN, Netblocks, IPs, Cloud, Security |
| 2 | Gateway | Firewalls, DMZ, IPS/IDS, EDR, Proxies, NAC, Segmentation, VPN |
| 3 | Accessible Services | Service Type, Functionality, Config, Port, Version, Interface |
| 4 | Processes | PID, Processed Data, Tasks, Source, Destination |
| 5 | Privileges | Groups, Users, Permissions, Restrictions, Environment |
| 6 | OS Setup | OS Type, Patch Level, Network config, Configs, Sensitive files |

<br>

---

<br>

### Domain Information



Passive recon — analyze the target's website, services, tech stack from the developer's POV.

SSL cert often lists multiple domains/subdomains it covers. Check via browser cert viewer.

Find subdomains via Certificate Transparency logs at [crt.sh](https://crt.sh/):

```diff
+ $ curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq .
```

Extract unique subdomain names:

```diff
+ $ curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq . | grep name | cut -d":" -f2 | grep -v "CN=" | cut -d'"' -f2 | awk '{gsub(/\\n/,"\n");}1;' | sort -u
```

Resolve subdomain → IP (filter to company-hosted only):

```diff
+ $ for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done
```

Run IPs through [Shodan](https://www.shodan.io/) to fingerprint exposed services:

```diff
+ $ for i in $(cat ip-addresses.txt);do shodan host $i;done
```

Pull all DNS records to find mail servers, NS, TXT (SPF/DKIM/DMARC), 3rd-party verifications:

```diff
+ $ dig any inlanefreight.com
```

TXT records often reveal 3rd-party providers (Atlassian, Google, LogMeIn, Mailgun, INWX, etc.) — each is a recon thread to pull.

<br>

---

<br>

### Cloud Resources



Cloud storage misconfigured by admins is a common attack surface — S3 buckets (AWS), blobs (Azure), cloud storage (GCP).

Find via Google Dorks:

```diff
+ intext:<company> inurl:amazonaws.com
+ intext:<company> inurl:blob.core.windows.net
```

Check pages' source code for direct CDN/blob references (`dns-prefetch`, `preconnect`).

Useful third-party recon services:
- [domain.glass](https://domain.glass/) — domain status, Cloudflare assessment, SSL
- [GrayHatWarfare](https://buckets.grayhatwarfare.com/) — searchable AWS/Azure/GCP bucket index, sortable by file format

Search target acronyms / abbreviations as well — admins often use shortened forms in bucket names.

Common high-value finds: SSH private keys (`id_rsa`), config files, backup archives.

<br>

---

<br>

### Staff



Employees on LinkedIn / Xing / GitHub reveal infrastructure clues — programming languages, frameworks, databases, source control, services in use.

Job postings list the exact tech stack — required skills + tools = recon gold.

GitHub profiles often leak: hardcoded JWT tokens, credentials, internal hostnames in code samples.

LinkedIn search is filterable by company, school, location, role, language — narrow to technical security/dev staff for the most actionable infrastructure intel.
