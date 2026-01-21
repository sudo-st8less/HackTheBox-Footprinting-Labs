# &#x1F6A9; IMAP & POP3 Footprinting <br>
<mark>hook it up with a &#x2B50; if this helps!</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>
<br>
<br>
Target IP: 10.129.153.162


---
### TTP Question 1:
Enumerate the target using the concepts taught in this section. List the hostname of MSSQL server

Checkout this denny's grand slam of a service scan:

	sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 10.129.153.162
	

	Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-08-22 17:04 CDT
	Nmap scan report for 10.129.153.162
	Host is up (0.0086s latency).
	
	Bug in ms-sql-hasdbaccess: no string output.
	PORT     STATE SERVICE  VERSION
	1433/tcp open  ms-sql-s Microsoft SQL Server 2019 15.00.2000.00; RTM
	| ms-sql-ntlm-info: 
	|   10.129.153.162\MSSQLSERVER: 
	|     Target_Name: ILF-SQL-01
	|     NetBIOS_Domain_Name: ILF-SQL-01
	|     NetBIOS_Computer_Name: ILF-SQL-01
	|     DNS_Domain_Name: ILF-SQL-01
	|     DNS_Computer_Name: ILF-SQL-01
	|_    Product_Version: 10.0.17763
	| ms-sql-xp-cmdshell: 
	|_  (Use --script-args=ms-sql-xp-cmdshell.cmd='<CMD>' to change command.)
	| ms-sql-dac: 
	|   10.129.153.162\MSSQLSERVER: 
	|     port: 1434
	|     state: closed
	|_    error: ERROR
	| ms-sql-info: 
	|   10.129.153.162\MSSQLSERVER: 
	|     Instance name: MSSQLSERVER
	|     Version: 
	|       name: Microsoft SQL Server 2019 RTM
	|       number: 15.00.2000.00
	|       Product: Microsoft SQL Server 2019
	|       Service pack level: RTM
	|       Post-SP patches applied: false
	|     TCP port: 1433
	|     Named pipe: \\10.129.153.162\pipe\sql\query
	|_    Clustered: false
	| ms-sql-config: 
	|   10.129.153.162\MSSQLSERVER: 
	|_  ERROR: Bad username or password
	| ms-sql-empty-password: 
	|_  10.129.153.162\MSSQLSERVER: 
	| ms-sql-tables: 
	|   10.129.153.162\MSSQLSERVER: 
	|_[10.129.153.162\MSSQLSERVER]
	| ms-sql-dump-hashes: 
	|_  10.129.153.162\MSSQLSERVER: ERROR: Bad username or password



&#x1F6A9;found **ILF-SQL-01** in scan output.


---
### TTP Question 2:
Connect to the MSSQL instance running on the target using the account (backdoor:Password1), then list the non-default database present on the server.

Impacket's mssqlclient is invaluable:

	$ python3 mssqlclient.py backdoor@10.129.153.162 -windows-auth
	
		Impacket v0.13.0.dev0+20250130.104306.0f4b866 - Copyright Fortra, LLC and its affiliated companies 
		
		Password:
		[*] Encryption required, switching to TLS
		[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
		[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english
		[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
		[*] INFO(ILF-SQL-01): Line 1: Changed database context to 'master'.
		[*] INFO(ILF-SQL-01): Line 1: Changed language setting to us_english.
		[*] ACK: Result: 1 - Microsoft SQL Server (150 7208) 
		[!] Press help for extra shell commands
		SQL (ILF-SQL-01\backdoor  dbo@master)>

		SQL (ILF-SQL-01\backdoor  dbo@master)> select name from sys.databases
		name        
		---------   
		master      
		
		tempdb      
		
		model       
		
		msdb        
		
		Employees   
		


&#x1F6A9; found **Employees**
