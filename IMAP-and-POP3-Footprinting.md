### CPTS / HTB Penetration Tester Path <br>
### Footprinting - IMAP & POP3 <br>
<mark>hook it up with a &#x2B50; if this helps.</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>

<br>
<br>

---

### IMAP & POP3 Footprinting



IMAP — manage emails on server, supports folder structure, multi-client sync. TCP 143 / 993 (TLS).
POP3 — list / retrieve / delete only. TCP 110 / 995 (TLS).

Dovecot is the typical Linux daemon. Both unencrypted by default — wrap with TLS via STARTTLS or use the SSL ports.

Dangerous settings (Dovecot):
- `auth_debug` — logs auth attempts
- `auth_debug_passwords` — logs submitted passwords
- `auth_verbose` — logs failed auths
- `auth_anonymous_username` — username for anonymous SASL

Key IMAP commands:

```diff
+ 1 LOGIN username password
+ 1 LIST "" *
+ 1 SELECT INBOX
+ 1 FETCH <ID> ALL
+ 1 FETCH <ID> BODY[]
+ 1 LOGOUT
```

Key POP3 commands: `USER`, `PASS`, `STAT`, `LIST`, `RETR id`, `DELE id`, `CAPA`, `RSET`, `QUIT`.

Nmap scan all four ports:

```diff
+ $ sudo nmap 10.129.14.128 -sV -p110,143,993,995 -sC
```

cURL — IMAPS auth + list folders:

```diff
+ $ curl -k 'imaps://10.129.14.128' --user user:p4ssw0rd
+ $ curl -k 'imaps://10.129.14.128' --user cry0l1t3:1234 -v
```

OpenSSL — interactive TLS shell:

```diff
+ $ openssl s_client -connect 10.129.14.128:pop3s
+ $ openssl s_client -connect 10.129.14.128:imaps
```

<br>

---

<br>

### IMAP & POP3 Exercise

IP: 10.129.10.175

---

### Question 1:
Figure out the exact organization name from the IMAP/POP3 service and submit it as the answer.

```diff
+ $ sudo nmap -sV -sC -p 110,143,993,995 10.129.10.175
```

	ssl-cert: Subject: commonName=dev.inlanefreight.htb/organizationName=InlaneFreight Ltd

&#x1F6A9; found **InlaneFr--edit-- Ltd**.

---

### Question 2:
What is the FQDN that the IMAP and POP3 servers are assigned to?

#### From the same nmap output's `commonName`.

&#x1F6A9; found **dev.inlane--edit--freight.htb**.

---

### Question 3:
Enumerate the IMAP service and submit the flag as the answer. (Format: HTB{...})

```diff
+ $ nc -nv 10.129.10.175 143
```

	* OK [CAPABILITY ...] HTB{roncfbw7iszerd7shni7jr2343zhrj}

&#x1F6A9; found **HTB{roncfb--edit--w7iszerd7shni7jr2343zhrj}**.

---

### Question 4:
What is the customized version of the POP3 server?

```diff
+ $ nc -nv 10.129.10.175 110
```

	+OK InFreight POP3 v9.188

&#x1F6A9; found **InFreight P--edit--OP3 v9.188**.

---

### Question 5:
What is the admin email address?

```diff
+ $ openssl s_client -connect 10.129.10.175:imaps
```

#### Login with creds from earlier (`robin:robin`):

```diff
+ 1 login robin robin
+ 1 list "" *
+ 1 select DEV.DEPARTMENT.INT
+ 1 fetch 1 all
```

	ENVELOPE ... (("CTO" NIL "devadmin" "inlanefreight.htb"))

&#x1F6A9; found **devadm--edit--in@inlanefreight.htb**.

---

### Question 6:
Try to access the emails on the IMAP server and submit the flag as the answer. (Format: HTB{...})

```diff
+ 1 FETCH 1 BODY[TEXT]
```

	* 1 FETCH (BODY[TEXT] {34}
	HTB{983uzn8jmfgpd8jmof8c34n7zio}

&#x1F6A9; found **HTB{983uzn--edit--8jmfgpd8jmof8c34n7zio}**.
