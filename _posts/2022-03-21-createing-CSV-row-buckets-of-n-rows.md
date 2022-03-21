---
layout: post
title:  "comma separated values buckets ofn rows TSQL Synapse"
author: Ashish
categories: [ SQL, TSQL ]
image: assets/images/20220321.gif
---

**comma separated values buckets ofn rows TSQL Synapse**


during working with ADF and synapse i have received one peculiar requirement call python script which in turns calling SOAP API.
my part was to pass values to the script and i got to know python can handle multiple values in same session so idea was to pass comma separated values to the scripts and i found one solution and tried to replicate same in synapse tsql [Source 1](https://asktom.oracle.com/pls/apex/f%3Fp%3D100:11:0::::P11_QUESTION_ID:9543894900346291667/)
i knew we can use STRING_AGG to create csv values challenge was to create group then i found we can use floor((row_number() over (order by  projectid ) - 1) / 4) to incremental groups and then in turn create buckets.

**here is the raw sql.**

~~~sql 
with selectionbucket(projectid) as (
select distinct nullif(Projectid,'') projectid
from TABLE (nolock) )
,bucketoperation(projectid,bucket) as (
select projectid, floor((row_number() over (order by  projectid ) - 1) / 4) as bucket
from selectionbucket )
SELECT  STRING_AGG (projectid, ',') as tags
FROM bucketoperation AS a
GROUP BY a.bucket
~~~

there is more pending creating sp to dynamically load table and some more to create dynammic SOAP body but will cover that later if it comes to that.
Sources:
[Source 1](https://asktom.oracle.com/pls/apex/f%3Fp%3D100:11:0::::P11_QUESTION_ID:9543894900346291667/)