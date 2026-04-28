### CPTS / HTB Penetration Tester Path <br>
### Footprinting - NFS <br>
<mark>hook it up with a &#x2B50; if this helps.</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>

<br>
<br>

---

### NFS Footprinting



NFS — file sharing for Linux/Unix. Built on ONC-RPC over TCP/UDP 111. NFSv4 uses TCP/UDP 2049 only. NFSv3 auths the client; NFSv4 auths the user (Kerberos, ACLs).

Auth is shifted to RPC; authz is via UID/GID — host the same UID/GID locally to inherit perms.

Exports defined in `/etc/exports`. Common options:
- `rw` / `ro` — read-write / read-only
- `sync` / `async` — sync data transfer
- `secure` / `insecure` — sub-1024 ports only / allow above
- `no_subtree_check` — skip subdirectory tree checks
- `root_squash` — root UID maps to anonymous (prevents root from accessing files)
- `no_root_squash` — root keeps UID 0 (DANGEROUS)
- `nohide` — re-export submount as its own export

Nmap scan + RPC info:

```diff
+ $ sudo nmap 10.129.14.128 -p111,2049 -sV -sC
```

Run NFS NSE scripts (lists shares, contents, stats):

```diff
+ $ sudo nmap --script nfs* 10.129.14.128 -sV -p111,2049
```

List exports:

```diff
+ $ showmount -e 10.129.14.128
```

Mount the export locally:

```diff
+ $ mkdir target-NFS
+ $ sudo mount -t nfs 10.129.14.128:/ ./target-NFS/ -o nolock
```

View contents with usernames or numeric IDs:

```diff
+ $ ls -l mnt/nfs/
+ $ ls -n mnt/nfs/
```

Unmount when done:

```diff
+ $ sudo umount ./target-NFS
```

If `root_squash` is set, you can read root files but can't modify them — escalate via SUID binary uploaded with target-uid ownership.

<br>

---

<br>

### NFS Exercise

IP: 10.129.202.5

---

### Question 1:
Enumerate the NFS service and submit the contents of the flag.txt in the "nfs" share as the answer.

```diff
+ $ sudo nmap -sV -sC -p 111,2049 10.129.202.5
```

#### Confirmed 111 + 2049 are open. Iterate with nfs scripts:

```diff
+ $ sudo nmap -sV -script=nfs* -p 111,2049 10.129.202.5
```

	Volume /var/nfs
	  access: Read Lookup Modify Extend Delete NoExecute
	rw-r--r--   65534  65534  39    flag.txt
	
	Volume /mnt/nfsshare
	  access: Read Lookup Modify Extend Delete NoExecute
	rw-r--r--   65534  65534  59    flag.txt

#### Both shares have a flag.txt. Mount and read:

```diff
+ $ mkdir target-NFS
+ $ sudo mount -t nfs 10.129.202.5:/ ./target-NFS/ -o nolock
+ $ cat ./target-NFS/var/nfs/flag.txt
```

&#x1F6A9; found **HTB{hjglmv--edit--tkjhlkfuhgi734zthrie7rjmdze}**.

---

### Question 2:
Enumerate the NFS service and submit the contents of the flag.txt in the "nfsshare" share as the answer.

#### Flag was in the `/mnt/nfsshare` mountpoint, sync'd via the same mount:

```diff
+ $ cat ./target-NFS/mnt/nfsshare/flag.txt
```

&#x1F6A9; found **HTB{8o74--edit--35zhtuih7fztdrzuhdhkfjcn7ghi4357ndcthzuc7rtfghu34}**.
