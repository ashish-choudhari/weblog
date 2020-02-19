---
layout: post
title:  "Netezza database copy without data..."
author: Ashish
categories: [ Netezza, NZ_MIGRATE ]
image: assets/images/4.jpg
---
Netezza database copy without data...
**Netezza copy database structure without data\...**

[You can take backup of user and groups if there is similar setup available already.]{.underline}

> /nz/support/bin/nz\_ddl\_grant\_group SRC\_DB \> ddl\_grant\_group.txt

> /nz/support/bin/nz\_ddl\_grant\_user SRC\_DB \> ddl\_grant\_users.txt

> /nz/support/bin/`nz\_migrate` `-shost localhost` `-sdb SRC\_DB` `-thost localhost` `-tdb TRG\_DB` `-tuser TRG\_USER` -tpassword \'P\@s\$W0rd\' `-CreateTargetDatabase Yes` `-CreateTargetTable Yes`  `-CreateTargetUDX Yes` `-genStats full` `-SourceWhereClause` \"1=2\"

Let Me break above command

1\. -shost localhost : you are running command at `source` DB box.

2\. -sdb SRC\_DB:Sourse DB

3\. DB -thost localhost: target to be created at can be another reachable location

4\. -tdb TRG\_DB: target database name

5\. DB -tuser TRG\_USER -tpassword \'P\@s\$W0rd\': target database `owner` user

6\. -CreateTargetDatabase Yes :will crete new database if not exists

7\. -CreateTargetTable Yes:will create non existing tables

8\. -CreateTargetUDX Yes : create UDX

9\. -genStats full:run genstat on newly created table as soon ascreated.

10\. -SourceWhereClause \"1=2\" :This is condition which do not copy but creates DB's (Any false condition will do)

If you are creating \`database\` like you need to need to create permissions from scratch if you haven't captured earlier.

Something similar you can do

Head over to prompt

> CREATE GROUP **TRG\_DB \_USERS**;

> CREATE GROUP **TRG\_DB \_DEVELOPERS**;

> grant list on **TRG\_DB** to **TRG\_DB** \_**DEVELOPERS**;

> \\c **TRG\_DB**

>grant list,select,insert,update,delete,truncate,lock,alter,drop,abort,load,genstats,groom,execute on TABLE to **TRG\_DB** \_DEVELOPERS;

>grant list,select,insert,update,delete,truncate,lock,alter,drop,abort,load,genstats,groom,execute on EXTERNAL TABLE to **TRG\_DB** \_DEVELOPERS;

>grant list,select,insert,update,delete,truncate,lock,alter,drop,abort,load,genstats,groom,execute on TEMP TABLE to **TRG\_DB** \_DEVELOPERS ;

>grant list,select,insert,update,delete,truncate,lock,alter,drop,abort,load,genstats,groom,execute on VIEW to **TRG\_DB** \_DEVELOPERS ;

>grant list,select,insert,update,delete,truncate,lock,alter,drop,abort,load,genstats,groom,execute on SEQUENCE to **TRG\_DB** \_DEVELOPERS ;

>grant list,select,insert,update,delete,truncate,lock,alter,drop,abort,load,genstats,groom,execute on PROCEDURE to **TRG\_DB** \_DEVELOPERS;

>grant execute on FUNCTION to **TRG\_DB** \_DEVELOPERS ;

>grant execute on AGGREGATE to **TRG\_DB** \_DEVELOPERS;

>grant list on **TRG\_DB** to **TRG\_DB** \_ **USERS**;

>grant list,select on TABLE to **TRG\_DB** \_DEVELOPERS;

>grant list,select on EXTERNAL TABLE to **TRG\_DB** \_DEVELOPERS;

>grant list,select on TEMP TABLE to **TRG\_DB** \_DEVELOPERS ;

>grant list,select on VIEW to **TRG\_DB** \_DEVELOPERS ;

>grant list,select on SEQUENCE to **TRG\_DB** \_DEVELOPERS ;

>grant list,select on PROCEDURE to **TRG\_DB** \_DEVELOPERS;

>grant execute on FUNCTION to **TRG\_DB** \_DEVELOPERS ;

>grant execute on AGGREGATE to **TRG\_DB** \_DEVELOPERS;