# &#x1F6A9; SNMP Footprinting <br>
<mark>hook it up with a &#x2B50; if this helps!</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>
<br>
<br>
Target IP: 10.129.255.129


---

### TTP Question 1:
Enumerate the SNMP service and obtain the email address of the admin. Submit it as the answer.

Try to make the habit of outputting all cmds, here we kinda need too to find the email:

	$ snmpwalk -v2c -c public 10.129.255.129 > snmpwalk_output.txt

&#x1F6A9; found **devadmin@inlanefreight.htb** in large output.

---

### TTP Question 2:
What is the customized version of the SNMP server?

&#x1F6A9; found **InFreight SNMP v0.91** in output of snmpwalk command.

---

### TTP Question 3:
Enumerate the custom script that is running on the system and submit its output as the answer.

&#x1F6A9; found **HTB{5nMp_fl4g_uidhfljnsldiuhbfsdij447--edit--}** in output from snmpwalk cmd.
