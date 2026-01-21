# &#x1F6A9; IMAP & POP3 Footprinting <br>
<mark>hook it up with a &#x2B50; if this helps!</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>
<br>
<br>
Target IP:10.129.10.175

---

### TTP Question 1:
Figure out the exact organization name from the IMAP/POP3 service and submit it as the answer.

Pop off a quick nmap on the ports in question:

	$ sudo nmap -sV -sC -p 110,143,993,995 10.129.10.175
  
	Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-08-21 00:05 CDT
	Nmap scan report for 10.129.10.175
	Host is up (0.0093s latency).
	
	PORT    STATE SERVICE  VERSION
	110/tcp open  pop3     Dovecot pop3d
	|_ssl-date: TLS randomness does not represent time
	| ssl-cert: Subject: commonName=dev.inlanefreight.htb/organizationName=InlaneFreight Ltd/stateOrProvinceName=London/countryName=UK
	| Not valid before: 2021-11-08T23:10:05
	|_Not valid after:  2295-08-23T23:10:05
	|_pop3-capabilities: UIDL TOP SASL STLS PIPELINING CAPA RESP-CODES AUTH-RESP-CODE
	143/tcp open  imap     Dovecot imapd
	|_ssl-date: TLS randomness does not represent time
	| ssl-cert: Subject: commonName=dev.inlanefreight.htb/organizationName=InlaneFreight Ltd/stateOrProvinceName=London/countryName=UK
	| Not valid before: 2021-11-08T23:10:05
	|_Not valid after:  2295-08-23T23:10:05
	|_imap-capabilities: LOGINDISABLEDA0001 more IMAP4rev1 post-login have Pre-login ID OK LITERAL+ capabilities listed ENABLE LOGIN-REFERRALS SASL-IR IDLE STARTTLS
	993/tcp open  ssl/imap Dovecot imapd
	| ssl-cert: Subject: commonName=dev.inlanefreight.htb/organizationName=InlaneFreight Ltd/stateOrProvinceName=London/countryName=UK
	| Not valid before: 2021-11-08T23:10:05
	|_Not valid after:  2295-08-23T23:10:05
	|_ssl-date: TLS randomness does not represent time
	|_imap-capabilities: more IMAP4rev1 post-login have AUTH=PLAINA0001 ID Pre-login LITERAL+ capabilities listed ENABLE LOGIN-REFERRALS SASL-IR OK IDLE
	995/tcp open  ssl/pop3 Dovecot pop3d
	| ssl-cert: Subject: commonName=dev.inlanefreight.htb/organizationName=InlaneFreight Ltd/stateOrProvinceName=London/countryName=UK
	| Not valid before: 2021-11-08T23:10:05
	|_Not valid after:  2295-08-23T23:10:05
	|_ssl-date: TLS randomness does not represent time
	|_pop3-capabilities: UIDL TOP SASL(PLAIN) USER PIPELINING CAPA RESP-CODES AUTH-RESP-CODE
	
	Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
	Nmap done: 1 IP address (1 host up) scanned in 14.55 seconds

&#x1F6A9; found **InlaneFreight Ltd**

---

### TTP Question 2:
What is the FQDN that the IMAP and POP3 servers are assigned to?

&#x1F6A9; found **dev.inlanefreight.htb** in output of nmap above.

---

### TTP Question 3:
Enumerate the IMAP service and submit the flag as the answer. (Format: HTB{...})

Netcat to banner grab:

	$ nc -nv 10.129.10.175 143
	(UNKNOWN) [10.129.10.175] 143 (imap2) open
	* OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ STARTTLS LOGINDISABLED] HTB{roncfbw7iszerd7shni7jr2343zhrj}



&#x1F6A9; found **HTB{roncfbw7iszerd7shni7jr2343zhrj}**

---

### TTP Question 4:
What is the customized version of the POP3 server?

Banner grab pop3 port:

	$ nc -nv 10.129.10.175 110
	(UNKNOWN) [10.129.10.175] 110 (pop3) open
	+OK InFreight POP3 v9.188

&#x1F6A9; found **InFreight POP3 v9.188**

---

### TTP Question 5:
What is the admin email address?

We'll start with openssl to connect:

	$ openssl s_client -connect 10.129.141.172:imaps
    1 OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE SORT SORT=DISPLAY THREAD=REFERENCES THREAD=REFS THREAD=ORDEREDSUBJECT MULTIAPPEND URL-PARTIAL CATENATE UNSELECT CHILDREN NAMESPACE UIDPLUS LIST-EXTENDED I18NLEVEL=1 CONDSTORE QRESYNC ESEARCH ESORT SEARCHRES WITHIN CONTEXT=SEARCH LIST-STATUS BINARY MOVE SNIPPET=FUZZY PREVIEW=FUZZY LITERAL+ NOTIFY SPECIAL-USE] Logged in

Now login:

    ---

	1 login robin robin

    1 OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE SORT SORT=DISPLAY THREAD=REFERENCES THREAD=REFS THREAD=ORDEREDSUBJECT MULTIAPPEND URL-PARTIAL CATENATE UNSELECT CHILDREN NAMESPACE UIDPLUS LIST-EXTENDED I18NLEVEL=1 CONDSTORE QRESYNC ESEARCH ESORT SEARCHRES WITHIN CONTEXT=SEARCH LIST-STATUS BINARY MOVE SNIPPET=FUZZY PREVIEW=FUZZY LITERAL+ NOTIFY SPECIAL-USE] Logged in

      ---

list what's availible:

	1 list "" *

    * LIST (\Noselect \HasChildren) "." DEV
    * LIST (\Noselect \HasChildren) "." DEV.DEPARTMENT
    * LIST (\HasNoChildren) "." DEV.DEPARTMENT.INT
    * LIST (\HasNoChildren) "." INBOX
    1 OK List completed (0.008 + 0.000 + 0.007 secs).
    
    ---
    
select and fetch:

	1 select DEV.DEPARTMENT.INT

    * FLAGS (\Answered \Flagged \Deleted \Seen \Draft)
    * OK [PERMANENTFLAGS (\Answered \Flagged \Deleted \Seen \Draft \*)] Flags permitted.
    * 1 EXISTS
    * 0 RECENT
    * OK [UIDVALIDITY 1636414279] UIDs valid
    * OK [UIDNEXT 2] Predicted next UID
    1 OK [READ-WRITE] Select completed (0.010 + 0.000 + 0.009 secs).
    
    ---

	1 fetch 1 all

    * 1 FETCH (FLAGS (\Seen) INTERNALDATE "08-Nov-2021 23:51:24 +0000" RFC822.SIZE 167 ENVELOPE ("Wed, 03 Nov 2021 16:13:27 +0200" "Flag" (("CTO" NIL "devadmin" "inlanefreight.htb")) (("CTO" NIL "devadmin" "inlanefreight.htb")) (("CTO" NIL "devadmin" "inlanefreight.htb")) (("Robin" NIL "robin" "inlanefreight.htb")) NIL NIL NIL NIL))
    1 OK Fetch completed (0.016 + 0.000 + 0.015 secs).


&#x1F6A9; found **devadmin @ inlanefreight.htb**

---

### TTP Question 6:
Try to access the emails on the IMAP server and submit the flag as the answer. (Format: HTB{...})

while in the same IMAP dir, I ran:

	1 FETCH 1 BODY[TEXT]
	
	* 1 FETCH (BODY[TEXT] {34}
	HTB{983uzn8jmfgpd8jmof8--edit--}

&#x1F6A9; found  **HTB{983uzn8jmfgpd8jmof8--edit--}**

