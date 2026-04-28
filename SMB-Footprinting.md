### CPTS / HTB Penetration Tester Path <br>
### Footprinting - SMB <br>
<mark>hook it up with a &#x2B50; if this helps.</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>

<br>
<br>

---

### SMB Footprinting



SMB — file/printer/IPC sharing. Linux equivalent is Samba. CIFS is the SMB v1 dialect (TCP 137/138/139); modern SMB uses TCP 445. ACLs control access per-share.

Dangerous Samba config (in `/etc/samba/smb.conf`): `browseable=yes`, `read only=no`, `writable=yes`, `guest ok=yes`, `enable privileges=yes`, `create mask=0777`.

List shares anonymously (null session):

```diff
+ $ smbclient -N -L //10.129.14.128
```

Connect to a share:

```diff
+ $ smbclient //10.129.14.128/notes
```

Inside `smbclient`: `help`, `ls`, `get <file>`, `!ls` (local), `!cat <file>` (local).

Server-side connection check (admin):

```diff
+ # smbstatus
```

Nmap SMB sweep:

```diff
+ $ sudo nmap 10.129.14.128 -sV -sC -p139,445
```

`rpcclient` — null session against MS-RPC:

```diff
+ $ rpcclient -U "" 10.129.14.128
```

Common rpcclient queries:

```diff
+ rpcclient $> srvinfo
+ rpcclient $> enumdomains
+ rpcclient $> querydominfo
+ rpcclient $> netshareenumall
+ rpcclient $> netsharegetinfo <share>
+ rpcclient $> enumdomusers
+ rpcclient $> queryuser <RID>
+ rpcclient $> querygroup <RID>
```

Brute-force RIDs in bash:

```diff
+ $ for i in $(seq 500 1100);do rpcclient -N -U "" 10.129.14.128 -c "queryuser 0x$(printf '%x\n' $i)" | grep "User Name\|user_rid\|group_rid" && echo "";done
```

Impacket `samrdump.py` — automated user enum via SAMR:

```diff
+ $ samrdump.py 10.129.14.128
```

Other SMB enum tools:

```diff
+ $ smbmap -H 10.129.14.128
+ $ crackmapexec smb 10.129.14.128 --shares -u '' -p ''
+ $ ./enum4linux-ng.py 10.129.14.128 -A
```

<br>

---

<br>

### SMB Exercise

IP: 10.129.202.5

---

### Question 1:
What version of the SMB server is running on the target system? Submit the entire banner as the answer.

```diff
+ $ sudo nmap -sV -sC -A -p 139,445 -v 10.129.202.5
```

#### Found banner in NMAP output.

&#x1F6A9; found **Samba sm--edit-- 4.6.2**.

---

### Question 2:
What is the name of the accessible share on the target?

```diff
+ $ smbclient -N -L //10.129.202.5
```

	Sharename       Type      Comment
	---------       ----      -------
	print$          Disk      Printer Drivers
	sambashare      Disk      InFreight SMB v3.1
	IPC$            IPC       IPC Service (InlaneFreight SMB server (Samba, Ubuntu))

&#x1F6A9; found **samba--edit--**.

---

### Question 3:
Connect to the discovered share and find the flag.txt file. Submit the contents as the answer.

```diff
+ $ smbclient //10.129.202.5/sambashare
```

#### Empty password worked. Listed dirs and pulled the flag:

```diff
+ smb: \> cd contents
+ smb: \contents\> get flag.txt
```

&#x1F6A9; found **HTB{o87--edit--3nz4xdo873n4zo873zn4fksuhldsf}**.

---

### Question 4:
Find out which domain the server belongs to.

```diff
+ $ rpcclient -U "" 10.129.202.5
+ rpcclient $> querydominfo
```

	Domain:		DEVOPS
	Server:		DEVSMB

&#x1F6A9; found **DEV--edit--**.

---

### Question 5:
Find additional information about the specific share we found previously and submit the customized version of that specific share as the answer.

```diff
+ rpcclient $> netshareenumall
```

	netname: sambashare
	remark:	InFreight SMB v3.1
	path:	'C:\home\sambauser\'

&#x1F6A9; found **InFreight S--edit-- v3.1**.

---

### Question 6:
What is the full system path of that specific share? (format: "/directory/names")

```diff
+ rpcclient $> netsharegetinfo sambashare
```

	path:	C:\home\sambauser\

#### Translated to Linux path format.

&#x1F6A9; found **/home/sa--edit--user**.
