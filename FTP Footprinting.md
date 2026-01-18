hook it up with a &#x2B50;!
<br>

Target IP: 10.129.202.5

---

### Question 1:
Which version of the FTP server is running on the target system? Submit the entire banner as the answer.

Quick netcat:

	nc -nv 10.129.202.5 21
&#x1F6A9; <mark>220 InFreight FTP --edit--</mark>

excluded the 220 ftp message and it worked.

---

### Question 2:
Enumerate the FTP server and find the flag.txt file. Submit the contents of it as the answer.

Easy wget anon login:
<br>

	wget -m --no-passive ftp://anonymous:anonymous@10.129.202.5

(this downloaded entire ftp directory to local pwd)

&#x1F6A9;<mark>HTB{b7skjr4c76zhsds7--edit--</mark>
