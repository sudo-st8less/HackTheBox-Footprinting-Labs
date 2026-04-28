### CPTS / HTB Penetration Tester Path <br>
### Footprinting - DNS <br>
<mark>hook it up with a &#x2B50; if this helps.</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>

<br>
<br>

---

### DNS Footprinting



DNS resolves names → IPs. Server types: Root, Authoritative, Non-authoritative, Caching, Forwarding, Resolver. DoT / DoH / DNSCrypt encrypt traffic.

DNS record types:
- `A` — IPv4 / `AAAA` — IPv6
- `MX` — mail servers / `NS` — nameservers
- `TXT` — verification keys, SPF/DMARC/DKIM
- `CNAME` — alias / `PTR` — reverse lookup
- `SOA` — zone admin contact

Bind9 config: `named.conf`, `named.conf.local`, `named.conf.options`, `named.conf.log`. Zone files contain forward records; reverse zone files contain PTR records.

Dangerous Bind options:
- `allow-query` — who can query
- `allow-recursion` — who can recurse
- `allow-transfer` — who can AXFR
- `zone-statistics` — collects stats

SOA query reveals admin email (replace dot with @):

```diff
+ $ dig soa www.inlanefreight.com
```

NS query against a target server:

```diff
+ $ dig ns inlanefreight.htb @10.129.14.128
```

Bind version (CHAOS class TXT):

```diff
+ $ dig CH TXT version.bind 10.129.120.85
```

ANY query — pulls all available records:

```diff
+ $ dig any inlanefreight.htb @10.129.14.128
```

AXFR zone transfer (if `allow-transfer` is loose):

```diff
+ $ dig axfr inlanefreight.htb @10.129.14.128
+ $ dig axfr internal.inlanefreight.htb @10.129.14.128
```

Subdomain brute-force via bash for-loop:

```diff
+ $ for sub in $(cat /opt/useful/seclists/Discovery/DNS/subdomains-top1million-110000.txt);do dig $sub.inlanefreight.htb @10.129.14.128 | grep -v ';\|SOA' | sed -r '/^\s*$/d' | grep $sub | tee -a subdomains.txt;done
```

Same with [DNSenum](https://github.com/fwaeytens/dnsenum):

```diff
+ $ dnsenum --dnsserver 10.129.14.128 --enum -p 0 -s 0 -o subdomains.txt -f /opt/useful/seclists/Discovery/DNS/subdomains-top1million-110000.txt inlanefreight.htb
```

<br>

---

<br>

### Types of DNS Attacks



- `Domain Hijacking` — attacker takes control via registrar exploit or DNS record takeover; redirects traffic to phishing sites.
- `DNS Flood` — DDoS hammering a DNS server with requests until resolution fails.
- `DRDoS` (Distributed Reflection DoS) — spoofs source IP to victim, causes amplifier servers to flood the target. Botnet-driven.
- `Cache Poisoning` (DNS Spoofing) — inject malicious data into resolver cache to redirect victims.
- `DNS Tunneling` — encode arbitrary data inside DNS queries/responses to bypass network controls; used for C2/exfil.
- `DNS Hijacking` — local malware modifies TCP/IP config to point at attacker DNS server.
- `Random Subdomain Attack` — flood requests for non-existent subdomains, saturate authoritative server.
- `NXDOMAIN Attack` — fill DNS server cache with NXDOMAIN responses, starve legitimate lookups.
- `Phantom Domain Attack` — force resolver to wait on phantom domains that never respond, degrade performance.

<br>

---

<br>

### DNS Exercise

IP: 10.129.200.249

---

### Question 1:
Interact with the target DNS using its IP address and enumerate the FQDN of it for the "inlanefreight.htb"s domain.

```diff
+ $ dig @10.129.200.249 inlanefreight.htb ANY
```

	;; ANSWER SECTION:
	inlanefreight.htb.	604800	IN	SOA	inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
	inlanefreight.htb.	604800	IN	NS	ns.inlanefreight.htb.

#### Found FQDN next to the SOA record (removed root dot).

&#x1F6A9; found **ns.inlanefr--edit--eight.htb**.

---

### Question 2:
Identify if its possible to perform a zone transfer and submit the TXT record as the answer. (Format: HTB{...})

#### Listed records to enumerate subdomains:

```diff
+ $ host -l inlanefreight.htb 10.129.200.249
```

#### Ran AXFR on each subdomain until one succeeded on `internal`:

```diff
+ $ dig @10.129.200.249 internal.inlanefreight.htb AXFR
```

	internal.inlanefreight.htb. 604800 IN	TXT	"HTB{DN5_z0N3_7r4N5F3r_iskdufhcnlu34}"

&#x1F6A9; found **HTB{DN5_z0N3--edit--_7r4N5F3r_iskdufhcnlu34}**.

---

### Question 3:
What is the IPv4 address of the hostname DC1?

```diff
+ $ dig @10.129.200.249 internal.inlanefreight.htb AXFR
```

#### Read A record from output.

&#x1F6A9; found **10.129.34.16**.

---

### Question 4:
What is the FQDN of the host where the last octet ends with "x.x.x.203"?

#### Enumerated each subdomain found via DNS queries until one had a `203` A record. Used DNSenum on `dev.inlanefreight.htb`:

```diff
+ $ dnsenum --dnsserver 10.129.128.96 --enum -p 0 -s 0 -o subdomains.txt -f /usr/share/wordlists/seclists/Discovery/DNS/fierce-hostlist.txt dev.inlanefreight.htb
```

	win2k.dev.inlanefreight.htb.   604800   IN    A    10.12.3.203

&#x1F6A9; found **win2k.dev.inla--edit--nefreight.htb**.
