## SMB Footprinting <br>
<mark>hook it up with a &#x2B50; if this helps!</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>
<br>


Target: 10.129.202.5


---
### Question 1:
What version of the SMB server is running on the target system? Submit the entire banner as the answer.

	sudo nmap -sV -sC -A -p 139,445 -v 10.129.202.5

found banner in NMAP output:<br>
&#x1F6A9; **Samba smbd 4.--edit--**

 ---

### Question 2:
What is the name of the accessible share on the target?

	smbclient -N -L  //10.129.202.5

	Sharename       Type      Comment
	---------       ----      -------
	print$          Disk      Printer Drivers
	sambashare      Disk      InFreight SMB v3.1
	IPC$            IPC       IPC Service (InlaneFreight SMB server (Samba, Ubuntu))

&#x1F6A9;**sambas--edit--**

---

### Question 3:
Connect to the discovered share and find the flag.txt file. Submit the contents as the answer.

	[★]$ smbclient //10.129.202.5/sambashare

return for password.

	smb: \> ls
	  .                                   D        0  Mon Nov  8 07:43:14 2021
	  ..                                  D        0  Mon Nov  8 09:53:19 2021
	  .profile                            H      807  Tue Feb 25 06:03:22 2020
	  contents                            D        0  Mon Nov  8 07:43:45 2021
	  .bash_logout                        H      220  Tue Feb 25 06:03:22 2020
	  .bashrc                             H     3771  Tue Feb 25 06:03:22 2020
	
	5090944 blocks of size 1024. 1765956 blocks available


	smb: \> cd contents
	smb: \contents\> ls
	  .                                   D        0  Mon Nov  8 07:43:45 2021
	  ..                                  D        0  Mon Nov  8 07:43:14 2021
	  flag.txt                            N       38  Mon Nov  8 07:43:45 2021
	
	5090944 blocks of size 1024. 1765952 blocks available
	smb: \contents\> get flag.txt
	getting file \contents\flag.txt of size 38 as flag.txt (1.0 KiloBytes/sec) (average 1.0 KiloBytes/sec)

downloaded flag.txt to home.

&#x1F6A9;**HTB{o873nz4xdo873n4zo873z--edit--**

---

### Question 4:
Find out which domain the server belongs to.

	[★]$ rpcclient -U "" 10.129.202.5

return for password.

	rpcclient $> srvinfo
	
			DEVSMB         Wk Sv PrQ Unx NT SNT InlaneFreight SMB server (Samba, Ubuntu)
			platform_id     :	500
			os version      :	6.1
			server type     :	0x809a03
			
	rpcclient $> enumdomains
	name:[DEVSMB] idx:[0x0]
	name:[Builtin] idx:[0x1]
	rpcclient $> querydom info
	command not found: querydom
	rpcclient $> querydominfo
	Domain:		DEVOPS
	Server:		DEVSMB
	Comment:	InlaneFreight SMB server (Samba, Ubuntu)
	Total Users:	0
	Total Groups:	0
	Total Aliases:	0
	Sequence No:	1755381822
	Force Logoff:	-1
	Domain Server State:	0x1
	Server Role:	ROLE_DOMAIN_PDC
	Unknown 3:	0x1

&#x1F6A9; domain **DEVOPS**

---

### Question 5:
Find additional information about the specific share we found previously and submit the customized version of that specific share as the answer.

	rpcclient $> netshareenumall

	netname: print$
	remark:	Printer Drivers
	path:	'C:\var\lib\samba\printers'
	password:	
	netname: sambashare
	remark:	InFreight SMB v3.1
	path:	'C:\home\sambauser\'
	password:	
	netname: IPC$
	remark:	IPC Service (InlaneFreight SMB server (Samba, Ubuntu))
	path:	'C:\tmp'
	password:	

 &#x1F6A9; found remark:	 **InFreight SMB --edit--**

---


### Question 6:
What is the full system path of that specific share? (format: "/directory/names")

	rpcclient $> netsharegetinfo sambashare
	netname: sambashare
		remark:	InFreight SMB v3.1
		path:	C:\home\sambauser\
		password:	
		type:	0x0
		perms:	0
		max_uses:	-1
		num_uses:	1

Just changed the path found here to linux format:

&#x1F6A9;**/home/samba--edit--**
