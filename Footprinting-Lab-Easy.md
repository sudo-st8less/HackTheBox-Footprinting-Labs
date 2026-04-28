### CPTS / HTB Penetration Tester Path <br>
### Footprinting - Lab Easy <br>
<mark>hook it up with a &#x2B50; if this helps.</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>

<br>
<br>

---

### Footprinting Lab - Easy

We were commissioned by `Inlanefreight Ltd` to test three different servers in their internal network. The first server is an internal DNS server. Our goal is to gather as much info as possible and find ways to use it against the company — but no aggressive exploits, the services are in production.

Teammates handed us creds `ceil:qwer1234` and a tip about SSH keys on the company forum.

Admin stored a `flag.txt` to track our progress.

IP: 10.129.15.238

Notes from kickoff:
- InlaneFreight Ltd
- inspect DNS server, do not exploit
- given creds: `ceil:qwer1234`
- SSH in use

---

### Question 1:
Enumerate the server carefully and find the flag.txt file. Submit the contents of this file as the answer.

#### Full TCP scan:

```diff
+ $ sudo nmap -sV -sC -A -v -oA init_scan.txt -p- 10.129.236.218
```

	21/tcp   open  ftp     ProFTPD Server (ftp.int.inlanefreight.htb)
	22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2
	53/tcp   open  domain  ISC BIND 9.16.1
	2121/tcp open  ftp     ProFTPD Server (Ceil's FTP)

#### UDP scan 1-2000:

```diff
+ $ sudo nmap -sV -sC -n -A -v -sU -oA udp_scan.txt -p 1-2000 10.129.236.218
```

	53/udp  open          domain   ISC BIND 9.16.1
	68/udp  open|filtered dhcpc
	623/udp open          asf-rmcp

#### Connected to FTP on TCP 2121 with given creds:

```diff
+ $ ftp -P 2121 ceil@10.129.15.238
```

	230 User ceil logged in

#### Pulled the SSH keys from `.ssh/` and inspected `.bash_history` (showed key generation + flag creation pattern). Moved `id_rsa` to local `~/.ssh/`, then SSH'd in:

```diff
+ $ ssh ceil@10.129.15.238
```

#### Traversed to `/home/flag/`:

```diff
+ ceil@NIXEASY:/home/flag$ cat flag.txt
```

	HTB{7nrzise7hednrxihskjed7nzrgkweunj47zngrhdbkjhgdfbjkc7hgj}

&#x1F6A9; found **HTB{7nrzise--edit--7hednrxihskjed7nzrgkweunj47zngrhdbkjhgdfbjkc7hgj}**.
