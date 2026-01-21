# &#x1F6A9; Footprinting Lab - Easy <br>
<mark>Hack The Box Penetration Tester Path full walkthrough <a href="https://github.com/sudo-st8less?tab=repositories">catalog</a>.</mark><br>
### <mark>hook the repo up with a &#x2B50; if it helps</mark> <br>
### 🐦: @<a href="https://x.com/st8less">**st8less**</a>

---
Target IP: 10.129.15.238<br>

From 'meeting':  
- InlaneFreight Ltd
- inspect DNS server, do not exploit
- given creds:  ceil:qwer1234 
- SSH in use

Engagement:<br>
We were commissioned by the company `Inlanefreight Ltd` to test three different servers in their internal network. The company uses many different services, and the IT security department felt that a penetration test was necessary to gain insight into their overall security posture.

The first server is an internal DNS server that needs to be investigated. In particular, our client wants to know what information we can get out of these services and how this information could be used against its infrastructure. Our goal is to gather as much information as possible about the server and find ways to use that information against the company. However, our client has made it clear that it is forbidden to attack the services aggressively using exploits, as these services are in production.

Additionally, our teammates have found the following credentials "ceil:qwer1234", and they pointed out that some of the company's employees were talking about SSH keys on a forum.

The administrators have stored a `flag.txt` file on this server to track our progress and measure success. Fully enumerate the target and submit the contents of this file as proof.

---

### Question 1:
#### Enumerate the server carefully and find the flag.txt file. Submit the contents of this file as the answer.

Scan all ports:

	$ sudo nmap -sV -sC -A -v -oA init_scan.txt -p- 10.129.236.218
	
		Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-08-27 17:39 CDT
		NSE: Loaded 156 scripts for scanning.
		NSE: Script Pre-scanning.
		Initiating NSE at 17:39
		Completed NSE at 17:39, 0.00s elapsed
		Initiating NSE at 17:39
		Completed NSE at 17:39, 0.00s elapsed
		Initiating NSE at 17:39
		Completed NSE at 17:39, 0.00s elapsed
		Initiating Ping Scan at 17:39
		Scanning 10.129.236.218 [4 ports]
		Completed Ping Scan at 17:39, 0.03s elapsed (1 total hosts)
		Initiating Parallel DNS resolution of 1 host. at 17:39
		Completed Parallel DNS resolution of 1 host. at 17:39, 0.00s elapsed
		Initiating SYN Stealth Scan at 17:39
		Scanning 10.129.236.218 [65535 ports]
		Discovered open port 53/tcp on 10.129.236.218
		Discovered open port 22/tcp on 10.129.236.218
		Discovered open port 21/tcp on 10.129.236.218
		Discovered open port 2121/tcp on 10.129.236.218
		Completed SYN Stealth Scan at 17:39, 7.42s elapsed (65535 total ports)
		Initiating Service scan at 17:39
		Scanning 4 services on 10.129.236.218
		Completed Service scan at 17:39, 28.56s elapsed (4 services on 1 host)
		Initiating OS detection (try #1) against 10.129.236.218
		Retrying OS detection (try #2) against 10.129.236.218
		Retrying OS detection (try #3) against 10.129.236.218
		Retrying OS detection (try #4) against 10.129.236.218
		Retrying OS detection (try #5) against 10.129.236.218
		adjust_timeouts2: packet supposedly had rtt of -72182 microseconds.  Ignoring time.
		adjust_timeouts2: packet supposedly had rtt of -72182 microseconds.  Ignoring time.
		Initiating Traceroute at 17:40
		Completed Traceroute at 17:40, 0.01s elapsed
		Initiating Parallel DNS resolution of 2 hosts. at 17:40
		Completed Parallel DNS resolution of 2 hosts. at 17:40, 0.00s elapsed
		NSE: Script scanning 10.129.236.218.
		Initiating NSE at 17:40
		Completed NSE at 17:40, 10.13s elapsed
		Initiating NSE at 17:40
		Completed NSE at 17:41, 52.88s elapsed
		Initiating NSE at 17:41
		Completed NSE at 17:41, 0.00s elapsed
		Nmap scan report for 10.129.236.218
		Host is up (0.0099s latency).
		Not shown: 65531 closed tcp ports (reset)
		PORT     STATE SERVICE VERSION
		21/tcp   open  ftp
		| fingerprint-strings: 
		|   GenericLines: 
		|     220 ProFTPD Server (ftp.int.inlanefreight.htb) [10.129.236.218]
		|     Invalid command: try being more creative
		|_    Invalid command: try being more creative
		22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
		| ssh-hostkey: 
		|   3072 3f:4c:8f:10:f1:ae:be:cd:31:24:7c:a1:4e:ab:84:6d (RSA)
		|   256 7b:30:37:67:50:b9:ad:91:c0:8f:f7:02:78:3b:7c:02 (ECDSA)
		|_  256 88:9e:0e:07:fe:ca:d0:5c:60:ab:cf:10:99:cd:6c:a7 (ED25519)
		53/tcp   open  domain  ISC BIND 9.16.1 (Ubuntu Linux)
		| dns-nsid: 
		|_  bind.version: 9.16.1-Ubuntu
		2121/tcp open  ftp
		| fingerprint-strings: 
		|   GenericLines: 
		|     220 ProFTPD Server (Ceil's FTP) [10.129.236.218]
		|     Invalid command: try being more creative
		|_    Invalid command: try being more creative
		2 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
		==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
		SF-Port21-TCP:V=7.94SVN%I=7%D=8/27%Time=68AF8921%P=x86_64-pc-linux-gnu%r(G
		SF:enericLines,9D,"220\x20ProFTPD\x20Server\x20\(ftp\.int\.inlanefreight\.
		SF:htb\)\x20\[10\.129\.236\.218\]\r\n500\x20Invalid\x20command:\x20try\x20
		SF:being\x20more\x20creative\r\n500\x20Invalid\x20command:\x20try\x20being
		SF:\x20more\x20creative\r\n");
		==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
		SF-Port2121-TCP:V=7.94SVN%I=7%D=8/27%Time=68AF8921%P=x86_64-pc-linux-gnu%r
		SF:(GenericLines,8E,"220\x20ProFTPD\x20Server\x20\(Ceil's\x20FTP\)\x20\[10
		SF:\.129\.236\.218\]\r\n500\x20Invalid\x20command:\x20try\x20being\x20more
		SF:\x20creative\r\n500\x20Invalid\x20command:\x20try\x20being\x20more\x20c
		SF:reative\r\n");
		No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
		TCP/IP fingerprint:
		OS:SCAN(V=7.94SVN%E=4%D=8/27%OT=21%CT=1%CU=30150%PV=Y%DS=2%DC=T%G=Y%TM=68AF
		OS:897F%P=x86_64-pc-linux-gnu)SEQ(SP=102%GCD=1%ISR=10E%TI=Z%II=I%TS=A)SEQ(S
		OS:P=102%GCD=1%ISR=10E%TI=Z%CI=Z)SEQ(SP=102%GCD=1%ISR=10E%TI=Z%CI=Z%II=I%TS
		OS:=A)SEQ(SP=103%GCD=1%ISR=10E%TI=Z%CI=Z%II=I%TS=D)OPS(O1=M552ST11NW7%O2=M5
		OS:52ST11NW7%O3=M552NNT11NW7%O4=M552ST11NW7%O5=M552ST11NW7%O6=M552ST11)WIN(
		OS:W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)ECN(R=N)ECN(R=Y%DF=Y%T=4
		OS:0%W=FAF0%O=M552NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(
		OS:R=N)T3(R=N)T4(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T4(R=Y%DF=
		OS:Y%T=40%W=0%S=O%A=Z%F=R%O=%RD=0%Q=)T5(R=N)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=O%F=
		OS:AR%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=N)T6(R=
		OS:Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=O%A=Z%F=R%
		OS:O=%RD=0%Q=)T7(R=N)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=O%F=AR%O=%RD=0%Q=)T7(R=Y%DF
		OS:=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=N)U1(R=Y%DF=N%T=40%IPL=164%UN=
		OS:0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)
		
		Uptime guess: 0.001 days (since Wed Aug 27 17:39:58 2025)
		Network Distance: 2 hops
		TCP Sequence Prediction: Difficulty=258 (Good luck!)
		IP ID Sequence Generation: All zeros
		Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
		
		TRACEROUTE (using port 1025/tcp)
		HOP RTT     ADDRESS
		1   8.99 ms 10.10.14.1
		2   9.07 ms 10.129.236.218

Found:

21 : tcp : ProFTPD Server (ftp.int.inlanefreight.htb)  [10.129.236.218]

22 : tcp : OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)

53 : tcp : (DNS?) ISC BIND 9.16.1 (Ubuntu Linux)

2121: tcp : ProFTPD Server (Ceil's FTP)  [10.129.236.218



Then, ran a UDP scan 1-2000:

	$ sudo nmap -sV -sC -n -A -v -sU -oA udp_scan.txt -p 1-2000 10.129.236.218

		PORT    STATE         SERVICE  VERSION
		53/udp  open          domain   ISC BIND 9.16.1 (Ubuntu Linux)
		| dns-nsid: 
		|_  bind.version: 9.16.1-Ubuntu
		68/udp  open|filtered dhcpc
		623/udp open          asf-rmcp

Found 21, 2121, 22, 53, 68 and 623.

connected to ftp on tcp 2121 with intel creds:

	$ ftp -P 2121 ceil@10.129.15.238
	
		Connected to 10.129.15.238.
		220 ProFTPD Server (Ceil's FTP) [10.129.15.238]
		331 Password required for ceil
		Password: 
		230 User ceil logged in
		Remote system type is UNIX.
		Using binary mode to transfer files.
		ftp> ls
		229 Entering Extended Passive Mode (|||54240|)
		150 Opening ASCII mode data connection for file list
		226 Transfer complete
		ftp> list
		?Invalid command.
		ftp> ls -la
		229 Entering Extended Passive Mode (|||10747|)
		150 Opening ASCII mode data connection for file list
		drwxr-xr-x   4 ceil     ceil         4096 Nov 10  2021 .
		drwxr-xr-x   4 ceil     ceil         4096 Nov 10  2021 ..
		-rw-------   1 ceil     ceil          294 Nov 10  2021 .bash_history
		-rw-r--r--   1 ceil     ceil          220 Nov 10  2021 .bash_logout
		-rw-r--r--   1 ceil     ceil         3771 Nov 10  2021 .bashrc
		drwx------   2 ceil     ceil         4096 Nov 10  2021 .cache
		-rw-r--r--   1 ceil     ceil          807 Nov 10  2021 .profile
		drwx------   2 ceil     ceil         4096 Nov 10  2021 .ssh
		-rw-------   1 ceil     ceil          759 Nov 10  2021 .viminfo
		226 Transfer complete
		ftp> 

downloaded shh keys, checked the bash history:


	[★]$ cat .bash_history
		
		ls -al
		mkdir ssh
		cd ssh/
		echo "test" > id_rsa
		id
		ssh-keygen -t rsa -b 4096
		cd ..
		rm -rf ssh/
		ls -al
		cd .ssh/
		cat id_rsa
		ls a-l
		ls -al
		cat id_rsa.pub >> authorized_keys
		cd ..
		cd /home
		cd ceil/
		ls -l
		ls -al
		mkdir flag
		cd flag/
		touch flag.txt
		vim flag.txt 
		cat flag.txt 
		ls -al
		mv flag/flag.txt .

moved id_rsa to my attackbox  ~/.ssh folder, then connected ssh to the server with ceil's creds:

	$ ssh ceil@10.129.15.238
	
		The authenticity of host '10.129.15.238 (10.129.15.238)' can't be established.
		ED25519 key fingerprint is SHA256:AtNYHXCA7dVpi58LB+uuPe9xvc2lJwA6y7q82kZoBNM.
		This key is not known by any other names.
		Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
		Warning: Permanently added '10.129.15.238' (ED25519) to the list of known hosts.
		Welcome to Ubuntu 20.04.1 LTS (GNU/Linux 5.4.0-90-generic x86_64)

then poked around the file system:

	ceil@NIXEASY:~$ ls -la
	
		total 36
		drwxr-xr-x 4 ceil ceil 4096 Nov 10  2021 .
		drwxr-xr-x 5 root root 4096 Nov 10  2021 ..
		-rw------- 1 ceil ceil  294 Nov 10  2021 .bash_history
		-rw-r--r-- 1 ceil ceil  220 Nov 10  2021 .bash_logout
		-rw-r--r-- 1 ceil ceil 3771 Nov 10  2021 .bashrc
		drwx------ 2 ceil ceil 4096 Nov 10  2021 .cache
		-rw-r--r-- 1 ceil ceil  807 Nov 10  2021 .profile
		drwx------ 2 ceil ceil 4096 Nov 10  2021 .ssh
		-rw------- 1 ceil ceil  759 Nov 10  2021 .viminfo

	ceil@NIXEASY:~/.cache$ cd ../../../
	ceil@NIXEASY:/$ ls
		bin  boot  cdrom  dev  etc  home  lib  lib32  lib64  libx32  lost+found  media  mnt  opt  proc  root  run  sbin  snap  srv  sys  tmp  usr  var
	ceil@NIXEASY:/$ cd home
	ceil@NIXEASY:/home$ ls
		ceil  cry0l1t3  flag
	ceil@NIXEASY:/home$ cd flag
	ceil@NIXEASY:/home/flag$ ls
		flag.txt
	ceil@NIXEASY:/home/flag$ cat flag.txt
		HTB{7nrzise7hednrxihskjed7nzrgkweunj47zngrhdbkjhgdfbj--edit--}

&#x1F6A9; found **HTB{7nrzise7hednrxihskjed7nzrgkweunj47zngrhdbkjhgdf--edit--}**
