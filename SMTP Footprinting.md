# &#x1F6A9; SMTP Footprinting  <br>
<mark>hook it up with a &#x2B50; if this helps!</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>
<br>

Target IP:
10.129.53.175

---

### TTP Question 1:
Enumerate the SMTP service and submit the banner, including its version as the answer.

	$ nc -nv 10.129.53.175 25
	(UNKNOWN) [10.129.53.175] 25 (smtp) open
	220 InFreight ESMTP v2.11

&#x1F6A9; found banner, removed message code.

---
### TTP Question 2:
Enumerate the SMTP service even further and find the username that exists on the system. Submit it as the answer.

Downloaded 'footprinting-wordlist.txt' from Github, then used smtp-user-enum tool to spray VRFY's against the WL.

	$ smtp-user-enum -M VRFY -U fw.txt  -t 10.129.53.175 -v -w20
	Starting smtp-user-enum v1.2 ( http://pentestmonkey.net/tools/smtp-user-enum )
	
	 ----------------------------------------------------------
	|                   Scan Information                       |
	 ----------------------------------------------------------
	
	Mode ..................... VRFY
	Worker Processes ......... 5
	Usernames file ........... fw.txt
	Target count ............. 1
	Username count ........... 101
	Target TCP port .......... 25
	Query timeout ............ 20 secs
	Target domain ............ 
	
	######## Scan started at Wed Aug 20 19:42:18 2025 #########
	10.129.53.175: john <no such user>
	10.129.53.175: michael <no such user>
	10.129.53.175: robert <no such user>
	10.129.53.175: james <no such user>
	10.129.53.175: david <no such user>
	10.129.53.175: joseph <no such user>
	10.129.53.175: william <no such user>
	10.129.53.175: richard <no such user>
	--snip--
	10.129.53.175: brenda <no such user>
	10.129.53.175: robin exists
	10.129.53.175: rachel <no such user>
	10.129.53.175: heather <no such user>
	10.129.53.175: adam <no such user>
	10.129.53.175: kelly <no such user>
	10.129.53.175: katherine <no such user>
	10.129.53.175: debra <no such user>
	10.129.53.175: zachary <no such user>
	10.129.53.175: dennis <no such user>
	10.129.53.175: christine <no such user>
	10.129.53.175: jordan <no such user>
	10.129.53.175: julie <no such user>
	10.129.53.175: christina <no such user>
	10.129.53.175: nathan <no such user>
	10.129.53.175: kyle <no such user>
	10.129.53.175: anna <no such user>
	######## Scan completed at Wed Aug 20 19:45:48 2025 #########
	1 results.
	
	101 queries in 210 seconds (0.5 queries / sec)


&#x1F6A9; found **robin**
