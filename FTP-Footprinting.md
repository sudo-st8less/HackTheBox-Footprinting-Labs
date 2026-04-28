### CPTS / HTB Penetration Tester Path <br>
### Footprinting - FTP <br>
<mark>hook it up with a &#x2B50; if this helps.</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>

<br>
<br>

---

### FTP Footprinting



FTP — control on TCP/21, data on TCP/20. Active mode = client tells server which port; passive mode = server announces port (firewall-friendly). TFTP is FTP over UDP/69 with no auth.

Most common Linux server: vsFTPd, config at `/etc/vsftpd.conf`. Denied users in `/etc/ftpusers`.

Dangerous settings: `anonymous_enable=YES`, `anon_upload_enable=YES`, `no_anon_password=YES`, `write_enable=YES`.

Anonymous login + listing:

```diff
+ $ ftp 10.129.14.136
```

vsFTPd status / verbose / trace inside ftp client:

```diff
+ ftp> status
+ ftp> debug
+ ftp> trace
+ ftp> ls -R
```

Download file:

```diff
+ ftp> get filename.txt
```

Upload file:

```diff
+ ftp> put file.txt
```

Mirror entire FTP tree to local dir:

```diff
+ $ wget -m --no-passive ftp://anonymous:anonymous@10.129.14.136
```

Nmap NSE scan (anon check, syst banner):

```diff
+ $ sudo nmap -sV -p21 -sC -A 10.129.14.136
```

Available NSE FTP scripts:

```diff
+ $ find / -type f -name ftp* 2>/dev/null | grep scripts
```

Banner grab via netcat / telnet:

```diff
+ $ nc -nv 10.129.14.136 21
+ $ telnet 10.129.14.136 21
```

FTP over TLS — use openssl client:

```diff
+ $ openssl s_client -connect 10.129.14.136:21 -starttls ftp
```

<br>

---

<br>

### Exercise

IP: 10.129.202.5

---

### Question 1:
Which version of the FTP server is running on the target system? Submit the entire banner as the answer.

#### Banner grab:

```diff
+ $ nc -nv 10.129.202.5 21
```

	220 InFreight FTP v1.1

#### Excluded the 220 SMTP code from the answer.

&#x1F6A9; found **InFreight F--edit-- v1.1**.

---

### Question 2:
Enumerate the FTP server and find the flag.txt file. Submit the contents of it as the answer.

#### Mirror everything anonymously:

```diff
+ $ wget -m --no-passive ftp://anonymous:anonymous@10.129.202.5
```

#### Downloaded the entire ftp directory to local pwd. Found `flag.txt`.

&#x1F6A9; found **HTB{b7sk--edit--c76zhsds7fzhd4k3ujg7nhdjre}**.
