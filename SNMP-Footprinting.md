### CPTS / HTB Penetration Tester Path <br>
### Footprinting - SNMP <br>
<mark>hook it up with a &#x2B50; if this helps.</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>

<br>
<br>

---

### SNMP Footprinting



SNMP — UDP/161 (queries), UDP/162 (traps). MIB defines available OIDs in tree hierarchy. SNMPv1/v2c send community strings in cleartext (no encryption); SNMPv3 adds username/password auth + pre-shared key encryption.

Common community strings: `public` (read-only default), `private` (read-write default).

Daemon config: `/etc/snmp/snmpd.conf`.

Dangerous settings:
- `rwuser noauth` — full OID tree, no auth
- `rwcommunity <str> <ip>` — full read-write
- `rwcommunity6 <str> <ip>` — same for IPv6

Walk an OID tree using a known community string:

```diff
+ $ snmpwalk -v2c -c public 10.129.14.128
```

If the walk hangs / huge — output to file:

```diff
+ $ snmpwalk -v2c -c public 10.129.255.129 > snmpwalk_output.txt
```

Brute-force community strings with [onesixtyone](https://github.com/trailofbits/onesixtyone):

```diff
+ $ sudo apt install onesixtyone
+ $ onesixtyone -c /opt/useful/seclists/Discovery/SNMP/snmp.txt 10.129.14.128
```

Brute-force individual OIDs using [braa](https://github.com/mteg/braa):

```diff
+ $ sudo apt install braa
+ $ braa public@10.129.14.128:.1.3.6.*
```

<br>

---

<br>

### SNMP Exercise

IP: 10.129.255.129

---

### Question 1:
Enumerate the SNMP service and obtain the email address of the admin. Submit it as the answer.

```diff
+ $ snmpwalk -v2c -c public 10.129.255.129 > snmpwalk_output.txt
```

#### Searched the output file — large output, write to file is essential.

&#x1F6A9; found **devadmi--edit--n@inlanefreight.htb**.

---

### Question 2:
What is the customized version of the SNMP server?

#### Same `snmpwalk_output.txt` — search for version strings.

&#x1F6A9; found **InFreight SN--edit--MP v0.91**.

---

### Question 3:
Enumerate the custom script that is running on the system and submit its output as the answer.

#### Same output file — looked for HTB{...} substring.

&#x1F6A9; found **HTB{5nMp_fl4--edit--g_uidhfljnsldiuhbfsdij44738b2u763g}**.
