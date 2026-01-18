# &#x1F6A9; FTP Footprinting <br>
<mark>hook it up with a &#x2B50; if this helps!</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>
<br>

Target IP: 10.129.202.5

---

### Question 1:
Which version of the FTP server is running on the target system? Submit the entire banner as the answer.

Quick netcat:

	nc -nv 10.129.202.5 21
&#x1F6A9; **220 InFreight FTP --edit--**

excluded the 220 ftp message and it worked.

---

### Question 2:
Enumerate the FTP server and find the flag.txt file. Submit the contents of it as the answer.

Easy wget anon login:
<br>

	wget -m --no-passive ftp://anonymous:anonymous@10.129.202.5

(this downloaded entire ftp directory to local pwd)

&#x1F6A9;**HTB{b7skjr4c76zhsds7--edit--**
