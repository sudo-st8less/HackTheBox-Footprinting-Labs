### CPTS / HTB Penetration Tester Path <br>
### Footprinting - Remote Management Protocols <br>
<mark>hook it up with a &#x2B50; if this helps.</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>

<br>
<br>

---

### Linux Remote Management Protocols



#### SSH (TCP 22)

OpenSSH is the standard fork. SSH-2 fixes MITM weaknesses in SSH-1.

Auth methods: password, public-key, host-based, keyboard, challenge-response, GSSAPI.

Default config: `/etc/ssh/sshd_config`.

Dangerous settings: `PasswordAuthentication yes`, `PermitEmptyPasswords yes`, `PermitRootLogin yes`, `Protocol 1`, `X11Forwarding yes`, `AllowTcpForwarding yes`, `PermitTunnel`, `DebianBanner yes`.

Audit a server's crypto + key exchange config:

```diff
+ $ git clone https://github.com/jtesta/ssh-audit.git && cd ssh-audit
+ $ ./ssh-audit.py 10.129.14.132
```

Connect verbosely to see supported auth + banner:

```diff
+ $ ssh -v cry0l1t3@10.129.14.132
```

Force a specific auth method (useful for brute force):

```diff
+ $ ssh -v cry0l1t3@10.129.14.132 -o PreferredAuthentications=password
```

#### Rsync (TCP 873)

Fast file copy with delta-transfer. Often used for backups/mirrors. Can use SSH transport.

Scan for service:

```diff
+ $ sudo nmap -sV -p 873 <target>
```

Probe for shares (no auth):

```diff
+ $ nc -nv <target> 873
+ #list
```

List share contents:

```diff
+ $ rsync -av --list-only rsync://<target>/dev
```

Sync everything down:

```diff
+ $ rsync -av rsync://<target>/dev
```

Over SSH transport:

```diff
+ $ rsync -av -e ssh user@host:/src/ ./local/
+ $ rsync -av -e "ssh -p2222" user@host:/src/ ./local/
```

#### R-Services (TCP 512/513/514)

Legacy Berkeley remote-access suite — `rcp`, `rsh`, `rexec`, `rlogin`, `rstat`, `ruptime`, `rwho`. Cleartext, MITM-vulnerable, replaced by SSH.

Trust files: `/etc/hosts.equiv` (global) and `~/.rhosts` (per-user). The `+` wildcard = auto-trust everything (catastrophic).

Scan for r-services ports:

```diff
+ $ sudo nmap -sV -p 512,513,514 <target>
```

Login via rlogin (abusing trust file misconfig):

```diff
+ $ rlogin <target> -l htb-student
```

List active sessions:

```diff
+ $ rwho
+ $ rusers -al <target>
```

<br>

---

<br>

### Windows Remote Management Protocols



#### RDP (TCP 3389)

Encrypts via TLS since Vista. Default Windows Server install. NLA (Network Level Authentication) is the default protection.

Nmap RDP enumeration:

```diff
+ $ nmap -sV -sC <target> -p3389 --script rdp*
```

Trace packets to inspect handshake:

```diff
+ $ nmap -sV -sC <target> -p3389 --packet-trace --disable-arp-ping -n
```

Note: Nmap leaves `mstshash=nmap` cookies — visible to threat hunters / EDR.

Detailed RDP security check via [rdp-sec-check](https://github.com/CiscoCXSecurity/rdp-sec-check):

```diff
+ $ git clone https://github.com/CiscoCXSecurity/rdp-sec-check.git && cd rdp-sec-check
+ $ ./rdp-sec-check.pl <target>
```

Connect with xfreerdp:

```diff
+ $ xfreerdp /u:user /p:"P455w0rd!" /v:<target>
```

#### WinRM (TCP 5985 HTTP / 5986 HTTPS)

SOAP-based. Enabled by default Windows Server 2012+. Often only HTTP (5985) is in use.

Nmap WinRM:

```diff
+ $ nmap -sV -sC <target> -p5985,5986 --disable-arp-ping -n
```

Test reachability from PowerShell:

```diff
+ PS> Test-WsMan <target>
```

Connect from Linux via [evil-winrm](https://github.com/Hackplayers/evil-winrm):

```diff
+ $ evil-winrm -i <target> -u Cry0l1t3 -p P455w0rD!
```

#### WMI (TCP 135 + dynamic)

Windows-native admin interface. Initial connect on TCP 135, then random high port.

Use Impacket's `wmiexec.py` for command execution:

```diff
+ $ /usr/share/doc/python3-impacket/examples/wmiexec.py user:"P455w0rD!"@<target> "hostname"
```
