---
layout: post
title:  "Netezza Groom and Genstat"
author: ashish
categories: [ Bash, Netezza,Groom,Gen_stat ]
image: assets/images/10.jpg
---
`Netezza` is high performance hardware and a database query engine designed for massively parallel processing, data warehousing and analytics

Netezza needs some performance tuning overtime as some leftovers in system starts degrading the performance.

Netezza generates the plans for query execution which are highly dependent on the statestics collected on tables,which helps optimizing query performance.

plans get outdated as soon as any `DML` opeartion is performed on the Database,sadly the netezza did not provide any scheduled mechanism to generate this statestics.but these thing also depends on the frequeny the data is manupulated.so we can build the script as per our requirement,i am attaching the example here.

second thing whenever any `DML` occure on the Database the netezza maintane the records that hold active and passive records,even if we delete the record deleted records is maintained when actually hold space which results in out of date statestics and space and there is second utility that can help here is Grooming netezza `database/table`.

 _Please check your backup mechanism before running groom it can force the full backup if diff back are run_**.

There are optional params that can be used based on granularity you need.

check based on your requirement.

>-RECORDS READY – Reclaim and reorganize ungroomed records in the table and those tables previously groomed but marked for re-grooming. This is the default for clustered base tables.

>-RECORDS ALL – Reclaim and reorganize all records in a table. This is the default for a non-CBT.

>-PAGES ALL – clean up empty pages and free up disk extents.

>-PAGES START – Identify and mark as ‘Empty’ leading data pages in the table with no visible record, stopping when it finds a data page that is not empty.

>-VERSIONS – Migrate records to latest changes occured on the table.

Let me walk through the few examples or groom commands here.

>/nz/support/bin/nz_groom $i -records >>$GROOMGENSTATLOG
>/nz/support/bin/nz_groom $i -pages >>$GROOMGENSTATLOG
>/nz/support/bin/nz_groom $i -versions >>$GROOMGENSTATLOG

genstat can done by following way $i is the databsae name

>nzsql -d $i -c 'GENERATE STATISTICS;'

crontab can set like following.
fields are separaeted by `spaces`.

| Description  	| values 	|
| ---	| ---	|
| minute       	| 0-59   	|
| hour         	| 0-23   	|
| day of month 	| 1-31   	|
| month        	| 1-12   	|
| day of week  	| 0-7    	|



> */5 * * * * /bin/bash -lc '/home/nz/scripts/nz_groom_genstat.sh'


Finally, the script is added below

```sh
#!/bin/bash
clear
~/bashrc
cd ~/scripts/backups
GROOMGENSTATCMD="date +%Y%m%d-%H%M%S"
GROOMGENSTATLOG=./logs/groom_genstat.$($GROOMGENSTATCMD)
export email_list="email1@company.com,email2@company.com"
NOWT=$(date +"_%Y_%m_%d_%I_%M_%p")
{

#Groom Databases
echo Begin grooming databases $(date) >>$GROOMGENSTATLOG
for i in $(nzsql -q -r -t -d system -c 'SELECT Database FROM _v_database WHERE Database NOT LIKE '%STG%' AND Database NOT LIKE '%DEV%';')
do
        echo Grooming database $i >> $GROOMGENSTATLOG
        /nz/support/bin/nz_groom $i -records >>$GROOMGENSTATLOG
        #/nz/support/bin/nz_groom $i -pages >>$GROOMGENSTATLOG
        #/nz/support/bin/nz_groom $i -versions >>$GROOMGENSTATLOG
done
echo End grooming  database $i: $(date) >> $GROOMGENSTATLOG


#Generate Statistics
echo Begin generate stats $(date) >>$GROOMGENSTATLOG
for i in $(nzsql -q -r -t -d system -c 'SELECT Database FROM _v_database WHERE Database NOT LIKE '%STG%' AND Database NOT LIKE '%DEV%';')
do
        echo Generate stats for $i
        nzsql -d $i -c 'GENERATE STATISTICS;'
done
echo End generate stats: $(date) >> $GROOMGENSTATLOG
) | tee ${GROOMGENSTATCMD}${NOWT}.log

/bin/mailx -s "Groom and gen_stat completed" -a ${GROOMGENSTATLOG} -a ${GROOMGENSTATCMD}${NOWT}.log $email_list 



```
<div style="Margin:20px;">
            <img src="/assets/images/avatar_ashish.jpg" align="left" width="100" height="100" border="0" style="Margin:0 20px 20px 20px; background:#E79851;" />
            <p style="Margin:10px 20px 20px 20px; font:16px/1.25 sans-serif; color:#4CB3E8; text-align:justify;">
               Trinus Corporation is a leading provider of technology solutions and services. With over two decades of experience, we enable our clients gain competitive advantage and superior business outcomes through cutting-edge, data-driven digital transformation solutions.
            <p align="right">
              <a href="linkedin.com/in/mannara-technologies-recruitment-team-1947495b">LinkedIn</a> |
              <a href="#">Link 2</a> |
              <a href="#">Link 3</a>
              <br><br>
            </p>
			</p>
</div>
[Ashish](mailto:ashish_choudhari@trinus.com?subject=[Trinus blogg])