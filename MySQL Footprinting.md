# &#x1F6A9; MySQL Footprinting <br>
<mark>hook it up with a &#x2B50; if this helps!</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>
<br>
<br>
Target IP: 10.129.143.67


---

### TTP Question 1:
Enumerate the MySQL server and determine the version in use. (Format: MySQL X.X.XX)

service and version scan with all mysql scripts, sexy:

	$ sudo nmap -sC -sV -p 3306 --script=mysql* 10.129.143.67 -v


		Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-08-21 18:56 CDT
		NSE: Loaded 57 scripts for scanning.
		NSE: Script Pre-scanning.
		Initiating NSE at 18:56
		Completed NSE at 18:56, 0.00s elapsed
		Initiating NSE at 18:56
		Completed NSE at 18:56, 0.00s elapsed
		Initiating Ping Scan at 18:56
		Scanning 10.129.143.67 [4 ports]
		Completed Ping Scan at 18:56, 0.05s elapsed (1 total hosts)
		Initiating Parallel DNS resolution of 1 host. at 18:56
		Completed Parallel DNS resolution of 1 host. at 18:56, 0.00s elapsed
		Initiating SYN Stealth Scan at 18:56
		Scanning 10.129.143.67 [1 port]
		Discovered open port 3306/tcp on 10.129.143.67
		Completed SYN Stealth Scan at 18:56, 0.03s elapsed (1 total ports)
		Initiating Service scan at 18:56
		Scanning 1 service on 10.129.143.67
		Completed Service scan at 18:56, 8.94s elapsed (1 service on 1 host)
		NSE: Script scanning 10.129.143.67.
		Initiating NSE at 18:56
		Stats: 0:01:39 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
		NSE: Active NSE Script Threads: 17 (16 waiting)
		NSE Timing: About 93.39% done; ETC: 18:57 (0:00:06 remaining)
		Stats: 0:01:56 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
		NSE: Active NSE Script Threads: 21 (7 waiting)
		NSE Timing: About 92.91% done; ETC: 18:58 (0:00:08 remaining)
		Stats: 0:02:04 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
		NSE: Active NSE Script Threads: 7 (7 waiting)
		NSE Timing: About 97.64% done; ETC: 18:58 (0:00:03 remaining)
		Completed NSE at 18:58, 115.13s elapsed
		Initiating NSE at 18:58
		Completed NSE at 18:58, 0.00s elapsed
		Nmap scan report for 10.129.143.67
		Host is up (0.0094s latency).
		
		PORT     STATE SERVICE VERSION
		3306/tcp open  mysql   MySQL 8.0.27-0ubuntu0.20.04.1
		| mysql-enum: 
		|   Accounts: No valid accounts found
		|_  Statistics: Performed 6 guesses in 5 seconds, average tps: 1.2
		| mysql-brute: 
		|   Accounts: No valid accounts found
		|   Statistics: Performed 7644 guesses in 115 seconds, average tps: 85.7
		|_  ERROR: The service seems to have failed or is heavily firewalled...
		| mysql-info: 
		|   Protocol: 10
		|   Version: 8.0.27-0ubuntu0.20.04.1
		|   Thread ID: 60
		|   Capabilities flags: 65535
		|   Some Capabilities: LongPassword, Support41Auth, SwitchToSSLAfterHandshake, SupportsTransactions, DontAllowDatabaseTableColumn, InteractiveClient, Speaks41ProtocolNew, IgnoreSigpipes, LongColumnFlag, ConnectWithDatabase, SupportsLoadDataLocal, FoundRows, Speaks41ProtocolOld, IgnoreSpaceBeforeParenthesis, SupportsCompression, ODBCClient, SupportsMultipleStatments, SupportsAuthPlugins, SupportsMultipleResults
		|   Status: Autocommit
		|   Salt: \x199?\x01oElX!\x17W\x13bi\x16|Com9
		|_  Auth Plugin Name: caching_sha2_password

&#x1F6A9; found **MySQL 8.0.27**.

---
### TTP Question 2:
During our penetration test, we found weak credentials "robin:robin". We should try these against the MySQL server. What is the email address of the customer "Otto Lang"?


Use the creds we've been blessed with:

	$ mysql -u robin -probin -h 10.129.143.67

		Welcome to the MariaDB monitor.  Commands end with ; or \g.
		Your MySQL connection id is 132
		Server version: 8.0.27-0ubuntu0.20.04.1 (Ubuntu)
		
		Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.
		
		Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
		
		MySQL [(none)]> show tables;
		ERROR 1046 (3D000): No database selected
		MySQL [(none)]> show databases;
		+--------------------+
		| Database           |
		+--------------------+
		| customers          |
		| information_schema |
		| mysql              |
		| performance_schema |
		| sys                |
		+--------------------+
		5 rows in set (0.035 sec)

	MySQL [(none)]> use customers;

		Reading table information for completion of table and column names
		You can turn off this feature to get a quicker startup with -A
		
		Database changed
		MySQL [customers]> show tables;
		+---------------------+
		| Tables_in_customers |
		+---------------------+
		| myTable             |
		+---------------------+
		1 row in set (0.012 sec)
	
	
	MySQL [customers]> select * from myTable;

		SNIP

		| 83 | Hanna Finch         | cursus.integer@outlook.htb               | United States      | 48968       | Arequipa                      | Ap #758-5939 Gravida Avenue       | 4024 0071 9785 2228 | 306  |
		| 84 | Myles Koch          | ligula@protonmail.htb                    | Russian Federation | 81636       | Hereford                      | 356-6096 Pede. Ave                | 534315 715275 8847  | 831  |
		| 85 | Hashim Waters       | aenean.euismod.mauris@aol.htb            | India              | 96531-615   | Pondicherry                   | P.O. Box 562, 6205 Et Road        | 402400 7126848131   | 936  |
		| 86 | Quinn Salinas       | mi.lacinia.mattis@google.htb             | United States      | A7L 2H9     | Hudson Bay                    | 6417 Eget Street                  | 5487 6745 5153 1985 | 431  |
		| 87 | Karly Wright        | tempor.diam@icloud.htb                   | Belgium            | 05122       | Kielce                        | 871-224 Ut Street                 | 513239 182594 9922  | 196  |
		| 88 | Otto Lang           | ultrices@google.htb                      | France             | 76733-267   | Belfast                       | 4708 Auctor Rd.                   | 5322224628183391    | 595  |
		| 89 | Hannah Mullen       | donec.tempus.lorem@hotmail.htb           | Peru               | 52018       | Mount Gambier                 | Ap #918-5986 Egestas Avenue       | 526114 931428 6484  | 599  |
		| 90 | Keefe Farmer        | eleifend.egestas.sed@outlook.htb         | Austria            | 314247      | Bhatinda                      | Ap #176-4321 Metus. Rd.           | 558717 273482 2453  | 976  |
		| 91 | Rylee Walton        | lobortis.tellus.justo@yahoo.htb          | Brazil             | 161682      | Morelia                       | P.O. Box 340, 8204 Diam. Ave      | 5561 8537 7945 1635 | 143  |
		| 92 | Quemby Owens        | cursus.et@google.htb                     | Peru               | 72447-13788 | Port Blair                    | Ap #923-4694 Pellentesque Avenue  | 545342 8473632383   | 341  |


		SNIP



&#x1F6A9; found **ultrices @ google.htb**
