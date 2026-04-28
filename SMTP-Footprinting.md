### CPTS / HTB Penetration Tester Path <br>
### Footprinting - SMTP <br>
<mark>hook it up with a &#x2B50; if this helps.</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>

<br>
<br>

---

### SMTP Footprinting



SMTP — TCP 25 (default), 465 (SMTPS), 587 (auth submission, STARTTLS). ESMTP extends SMTP with auth, encryption, and SPF/DKIM/DMARC anti-spoofing.

Pipeline: MUA → MSA (relay) → MTA (open relay) → MDA → POP3/IMAP mailbox.

Postfix config: `/etc/postfix/main.cf`. The `mynetworks` setting controls relay scope — `mynetworks = 0.0.0.0/0` is an open relay (DANGEROUS).

Core SMTP commands: `AUTH PLAIN`, `HELO`, `EHLO`, `MAIL FROM`, `RCPT TO`, `DATA`, `RSET`, `VRFY`, `EXPN`, `NOOP`, `QUIT`.

Connect with telnet:

```diff
+ $ telnet 10.129.14.128 25
```

Inside the session — send EHLO + enumerate users via VRFY:

```diff
+ EHLO mail1
+ VRFY root
+ VRFY cry0l1t3
```

Note: VRFY can return `252` for ALL inputs depending on config (false positives).

Pivot via web proxy:

```diff
+ CONNECT 10.129.14.128:25 HTTP/1.0
```

Send a mail end-to-end:

```diff
+ EHLO inlanefreight.htb
+ MAIL FROM: <attacker@inlanefreight.htb>
+ RCPT TO: <victim@inlanefreight.htb> NOTIFY=success,failure
+ DATA
```

Nmap default scripts (`smtp-commands` runs EHLO):

```diff
+ $ sudo nmap 10.129.14.128 -sC -sV -p25
```

Open-relay test (16 different vectors):

```diff
+ $ sudo nmap 10.129.14.128 -p25 --script smtp-open-relay -v
```

User enum with [smtp-user-enum](https://pentestmonkey.net/tools/user-enumeration/smtp-user-enum):

```diff
+ $ smtp-user-enum -M VRFY -U users.txt -t 10.129.14.128 -v -w20
```

Modes: `VRFY`, `EXPN`, `RCPT`.

<br>

---

<br>

### SMTP Errors and Reply Codes



Reply codes are 3 digits: 1xx (info), 2xx (success), 3xx (more input needed), 4xx (transient), 5xx (permanent).

Quick reference for the codes you'll see most often:

| Code | Meaning |
|---|---|
| 220 | Server ready (welcome banner) |
| 221 | Closing channel ("Goodbye") |
| 250 | Action OK / message accepted |
| 251 | User not local — relayed |
| 252 | Server can't VRFY user (still tries delivery) |
| 354 | DATA accepted — start mail body |
| 421 | Service unavailable (temporary) |
| 450 | Mailbox unavailable (temp) |
| 451 | Local processing error / antispam reject |
| 500/501 | Syntax error in command/parameters |
| 503 | Bad command sequence / auth required |
| 530 | Authentication required |
| 550 | Mailbox not found / firewall block |
| 553 | Mailbox name invalid |
| 554 | Transaction failed (perm) — often blacklist |

`5xx` series (especially `550–559`) = permanent failures; investigate blacklisting, recipient address validity, or relay restrictions.

<br>

---

<br>

### SMTP Exercise

IP: 10.129.53.175

---

### Question 1:
Enumerate the SMTP service and submit the banner, including its version as the answer.

```diff
+ $ nc -nv 10.129.53.175 25
```

	220 InFreight ESMTP v2.11

#### Removed the 220 reply code from the answer.

&#x1F6A9; found **InFreight ES--edit--MTP v2.11**.

---

### Question 2:
Enumerate the SMTP service even further and find the username that exists on the system. Submit it as the answer.

#### Pulled `footprinting-wordlist.txt` from GitHub. Sprayed VRFY against it:

```diff
+ $ smtp-user-enum -M VRFY -U fw.txt -t 10.129.53.175 -v -w20
```

	10.129.53.175: robin exists

&#x1F6A9; found **rob--edit--in**.
