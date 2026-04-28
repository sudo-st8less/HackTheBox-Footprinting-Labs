### CPTS / HTB Penetration Tester Path <br>
### Footprinting - MSSQL <br>
<mark>hook it up with a &#x2B50; if this helps.</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>

<br>
<br>

---

### MSSQL Footprinting



MSSQL — closed-source Microsoft RDBMS, TCP 1433 default. Often runs as `NT SERVICE\MSSQLSERVER`. Default auth = Windows Authentication (uses local SAM or AD).

Default system DBs:
- `master` — system info for the SQL instance
- `model` — template for new DBs
- `msdb` — SQL Server Agent jobs/alerts
- `tempdb` — temp objects
- `resource` — read-only system objects

Dangerous settings: unencrypted client connections, self-signed certs (spoofable), enabled named pipes, default `sa` account, weak `sa` password.

Common clients: SSMS (Windows GUI), `mssql-cli`, `SQL Server PowerShell`, [Impacket's `mssqlclient.py`](https://github.com/SecureAuthCorp/impacket/blob/master/examples/mssqlclient.py).

Locate impacket binary:

```diff
+ $ locate mssqlclient
```

Nmap NSE scan (covers info, empty pw, xp_cmdshell, NTLM, tables, hashes):

```diff
+ $ sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 <target>
```

Metasploit `mssql_ping` aux scanner:

```diff
+ msf6 > use auxiliary/scanner/mssql/mssql_ping
+ msf6 auxiliary(scanner/mssql/mssql_ping) > set rhosts <target>
+ msf6 auxiliary(scanner/mssql/mssql_ping) > run
```

Connect via Impacket (Windows auth):

```diff
+ $ python3 mssqlclient.py Administrator@<target> -windows-auth
```

List DBs once connected:

```diff
+ SQL> select name from sys.databases
```

<br>

---

<br>

### MSSQL Exercise

IP: 10.129.153.162

---

### Question 1:
Enumerate the target using the concepts taught in this section. List the hostname of MSSQL server.

```diff
+ $ sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 10.129.153.162
```

	ms-sql-ntlm-info: 
	  Target_Name: ILF-SQL-01

&#x1F6A9; found **ILF-S--edit--QL-01**.

---

### Question 2:
Connect to the MSSQL instance running on the target using the account (backdoor:Password1), then list the non-default database present on the server.

```diff
+ $ python3 mssqlclient.py backdoor@10.129.153.162 -windows-auth
+ SQL (ILF-SQL-01\backdoor  dbo@master)> select name from sys.databases
```

	master / tempdb / model / msdb / Employees

&#x1F6A9; found **Empl--edit--oyees**.
