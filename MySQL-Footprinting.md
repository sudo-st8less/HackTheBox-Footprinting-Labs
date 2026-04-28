### CPTS / HTB Penetration Tester Path <br>
### Footprinting - MySQL <br>
<mark>hook it up with a &#x2B50; if this helps.</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>

<br>
<br>

---

### MySQL Footprinting



MySQL — TCP 3306. Default config in `/etc/mysql/mysql.conf.d/mysqld.cnf`.

Dangerous settings: `user`, `password`, `admin_address` (plaintext); `debug`, `sql_warnings` (verbose error leak); `secure_file_priv` (file import/export scope).

Nmap with mysql NSE scripts (auth bypass, dump hashes, enum, version):

```diff
+ $ sudo nmap 10.129.14.128 -sV -sC -p3306 --script mysql*
```

Important: validate `mysql-empty-password` results manually — false positive prone.

Connect with creds (no space after `-p`):

```diff
+ $ mysql -u root -pP4SSw0rd -h 10.129.14.128
```

Useful in-shell commands:

```diff
+ MySQL [(none)]> show databases;
+ MySQL [(none)]> use <database>;
+ MySQL [<db>]> show tables;
+ MySQL [<db>]> show columns from <table>;
+ MySQL [<db>]> select * from <table>;
+ MySQL [<db>]> select * from <table> where <col> = "<string>";
```

System schemas to know:
- `information_schema` — metadata about all DBs
- `mysql` — system tables (users, grants, etc.)
- `performance_schema` — runtime stats
- `sys` — Microsoft-style summary views

<br>

---

<br>

### MySQL Exercise

IP: 10.129.143.67

---

### Question 1:
Enumerate the MySQL server and determine the version in use. (Format: MySQL X.X.XX)

```diff
+ $ sudo nmap -sC -sV -p 3306 --script=mysql* 10.129.143.67 -v
```

	3306/tcp open  mysql   MySQL 8.0.27-0ubuntu0.20.04.1

&#x1F6A9; found **MySQL 8--edit--.0.27**.

---

### Question 2:
During our penetration test, we found weak credentials "robin:robin". We should try these against the MySQL server. What is the email address of the customer "Otto Lang"?

```diff
+ $ mysql -u robin -probin -h 10.129.143.67
```

#### Listed databases and selected `customers`:

```diff
+ MySQL [(none)]> show databases;
+ MySQL [(none)]> use customers;
+ MySQL [customers]> show tables;
+ MySQL [customers]> select * from myTable;
```

	| 88 | Otto Lang  | ultrices@google.htb | France | ... |

&#x1F6A9; found **ultri--edit--ces@google.htb**.
