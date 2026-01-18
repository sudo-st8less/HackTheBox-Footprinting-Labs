# &#x1F6A9; DNS Footprinting <br>
<mark>hook it up with a &#x2B50; if this helps!</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>
<br>


Target IP:
10.129.200.249

---

### TTP Question 1:

 Interact with the target DNS using its IP address and enumerate the FQDN of it for the "inlanefreight.htb"s domain.

	$ dig @10.129.200.249 inlanefreight.htb ANY
	
	; <<>> DiG 9.18.33-1~deb12u2-Debian <<>> @10.129.200.249 inlanefreight.htb ANY
	; (1 server found)
	;; global options: +cmd
	;; Got answer:
	;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 43450
	;; flags: qr aa rd; QUERY: 1, ANSWER: 5, AUTHORITY: 0, ADDITIONAL: 2
	;; WARNING: recursion requested but not available
	
	;; OPT PSEUDOSECTION:
	; EDNS: version: 0, flags:; udp: 4096
	; COOKIE: fdea5a9edde6c6930100000068a509c7cd33a777b51d3842 (good)
	;; QUESTION SECTION:
	;inlanefreight.htb.		IN	ANY
	
	;; ANSWER SECTION:
	inlanefreight.htb.	604800	IN	TXT	"v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.124.8 ip4:10.129.127.2 ip4:10.129.42.106 ~all"
	inlanefreight.htb.	604800	IN	TXT	"atlassian-domain-verification=t1rKCy68JFszSdCKVpw64A1QksWdXuYFUeSXKU"
	inlanefreight.htb.	604800	IN	TXT	"MS=ms97310371"
	inlanefreight.htb.	604800	IN	SOA	inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
	inlanefreight.htb.	604800	IN	NS	ns.inlanefreight.htb.
	
	;; ADDITIONAL SECTION:
	ns.inlanefreight.htb.	604800	IN	A	127.0.0.1
	
	;; Query time: 9 msec
	;; SERVER: 10.129.200.249#53(10.129.200.249) (TCP)
	;; WHEN: Tue Aug 19 18:33:27 CDT 2025
	;; MSG SIZE  rcvd: 437

found FQDN next to SOA record, removed the root dot (.) <br>
&#x1F6A9;**ns.inlanefreight.htb**

---
### TTP Question 2:

 Identify if its possible to perform a zone transfer and submit the TXT record as the answer. (Format: HTB{...})

	$ host -l inlanefreight.htb 10.129.200.249
	Using domain server:
	Name: 10.129.200.249
	Address: 10.129.200.249#53
	Aliases: 
	
	inlanefreight.htb name server ns.inlanefreight.htb.
	app.inlanefreight.htb has address 10.129.18.15
	dev.inlanefreight.htb has address 10.12.0.1
	internal.inlanefreight.htb has address 10.129.1.6
	mail1.inlanefreight.htb has address 10.129.18.201
	ns.inlanefreight.htb has address 127.0.0.1

found subdomains, and ran dig on all of them until i found flag on 'internal' subdomain.

	$ dig @10.129.200.249 internal.inlanefreight.htb AXFR
	
	; <<>> DiG 9.18.33-1~deb12u2-Debian <<>> @10.129.200.249 internal.inlanefreight.htb AXFR
	; (1 server found)
	;; global options: +cmd
	internal.inlanefreight.htb. 604800 IN	SOA	inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
	internal.inlanefreight.htb. 604800 IN	TXT	"MS=ms97310371"
	internal.inlanefreight.htb. 604800 IN	TXT	"HTB{DN5_z0N3_7r4N5F3r_iskdufhcnlu34}"
	internal.inlanefreight.htb. 604800 IN	TXT	"atlassian-domain-verification=t1rKCy68JFszSdCKVpw64A1QksWdXuYFUeSXKU"
	internal.inlanefreight.htb. 604800 IN	TXT	"v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.124.8 ip4:10.129.127.2 ip4:10.129.42.106 ~all"
	internal.inlanefreight.htb. 604800 IN	NS	ns.inlanefreight.htb.
	dc1.internal.inlanefreight.htb.	604800 IN A	10.129.34.16
	dc2.internal.inlanefreight.htb.	604800 IN A	10.129.34.11
	mail1.internal.inlanefreight.htb. 604800 IN A	10.129.18.200
	ns.internal.inlanefreight.htb. 604800 IN A	127.0.0.1
	vpn.internal.inlanefreight.htb.	604800 IN A	10.129.1.6
	ws1.internal.inlanefreight.htb.	604800 IN A	10.129.1.34
	ws2.internal.inlanefreight.htb.	604800 IN A	10.129.1.35
	wsus.internal.inlanefreight.htb. 604800	IN A	10.129.18.2
	internal.inlanefreight.htb. 604800 IN	SOA	inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
	;; Query time: 9 msec
	;; SERVER: 10.129.200.249#53(10.129.200.249) (TCP)
	;; WHEN: Tue Aug 19 18:46:39 CDT 2025
	;; XFR size: 15 records (messages 1, bytes 677)
&#x1F6A9; **HTB{DN5_z0N3_7r4N5F3r_iskd--edit--}**

---
### TTP Question 4:

What is the IPv4 address of the hostname DC1?

	dig @10.129.200.249 internal.inlanefreight.htb AXFR

found A record in output:
&#x1F6A9;**10.129.34.16**

---
### TTP Question 5:

What is the FQDN of the host where the last octet ends with "x.x.x.203"?

enumerated each subdomain found on the dns queries until i found one with an A record with the last octet set at 203.

	$ dnsenum --dnsserver 10.129.128.96 --enum -p 0 -s 0 -o subdomains.txt -f /usr/share/wordlists/seclists/Discovery/DNS/fierce-hostlist.txt dev.inlanefreight.htb
	dnsenum VERSION:1.2.6
	
	-----   dev.inlanefreight.htb   -----
	
	
	Host's addresses:
	__________________
	
	
	
	Name Servers:
	______________
	
	ns.inlanefreight.htb.                    604800   IN    A         127.0.0.1
	
	
	Mail (MX) Servers:
	___________________
	
	
	
	Trying Zone Transfers and getting Bind Versions:
	_________________________________________________
	
	unresolvable name: ns.inlanefreight.htb at /usr/bin/dnsenum line 900 thread 2.
	
	Trying Zone Transfer for dev.inlanefreight.htb on ns.inlanefreight.htb ... 
	AXFR record query failed: no nameservers
	
	
	Brute forcing with /usr/share/wordlists/seclists/Discovery/DNS/fierce-hostlist.txt:
	____________________________________________________________________________________
	
	dev1.dev.inlanefreight.htb.              604800   IN    A         10.12.3.6
	ns.dev.inlanefreight.htb.                604800   IN    A         127.0.0.1
	win2k.dev.inlanefreight.htb.             604800   IN    A        10.12.3.203
	
	
	Launching Whois Queries:
	_________________________
	
	
	
	dev.inlanefreight.htb_____________________
	
	
	
	Performing reverse lookup on 0 ip addresses:
	_____________________________________________
	
	
	0 results out of 0 IP addresses.
	
	
	dev.inlanefreight.htb ip blocks:
	_________________________________
	
	
	done.



&#x1F6A9; **win2k.dev.inlan--edit--ht.htb**


