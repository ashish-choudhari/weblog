---
layout: post
title:  "comma separated values to columns TSQL Synapse"
author: Ashish
categories: [ sql, bash ]
image: assets/images/18.gif
---

**CSV to columns sql**


Recently I face one interesting problem where values columns where passing values in, separated values in double quoted strings we had to separate those into the different columns as a part of a SQL transformation so basically  I tried to find solutions based on existing material that is available in online post or blogs so we were using SQL Server as our transformation tool and we had more than 4 or 5 values to be separated. first thing we tried was to use parsename function inside SQL but we failed to get desired output once we realized we had around 8 columns to be separated out of that transformation whereas parsename only separate 4 columns and it returns null afterwards we passed that threshold

~~~sql 
SELECT FeedbackID,
SUBSTRING(levelsFeed,0,CHARINDEX(',',levelsFeed)) AS level1,
PARSENAME(REPLACE(SUBSTRING(levelsFeed,CHARINDEX(',',levelsFeed)+1,LEN(levelsFeed)),',','.'),4) AS level2,
PARSENAME(REPLACE(SUBSTRING(levelsFeed,CHARINDEX(',',levelsFeed)+1,LEN(levelsFeed)),',','.'),3) AS level3,
PARSENAME(REPLACE(SUBSTRING(levelsFeed,CHARINDEX(',',levelsFeed)+1,LEN(levelsFeed)),',','.'),2) AS level4,
PARSENAME(REPLACE(SUBSTRING(levelsFeed,CHARINDEX(',',levelsFeed)+1,LEN(levelsFeed)),',','.'),1) AS level5
FROM Feedbacks
~~~


second scenario we try to use cross apply to separate stage in 2 separate columns basically we have to do cross apply over column while passing previous column as new output to next cross apply block joining same string again and again does have some performance penalty so I decided to venture further for another solution while it was I was doing that I tried to use string split functionality but I wasn't aware of for the story I will cover that next scenario

~~~sql 
select ParsedData.* 
from MyTable mt
cross apply ( select str = mt.String + ',,' ) f1
cross apply ( select p1 = charindex( ',', str ) ) ap1
cross apply ( select p2 = charindex( ',', str, p1 + 1 ) ) ap2
cross apply ( select Nmame = substring( str, 1, p1-1 )                   
                 , Surname = substring( str, p1+1, p2-p1-1 )
          ) ParsedData
~~~		  
		  
well, some solutions were using XML as data type transformation based on that we can separate columns into multiple but we are using online solution as data analytics and it does not support some of the SQL Server functionality so XML as data type was out of picture here


~~~sql 
SELECT Distinct FeedbackID, 
, S.a.value('(/H/r)[1]', 'INT') AS level1
, S.a.value('(/H/r)[2]', 'INT') AS level2
, S.a.value('(/H/r)[3]', 'INT') AS level3
, S.a.value('(/H/r)[4]', 'INT') AS level4
, S.a.value('(/H/r)[5]', 'INT') AS level5
FROM (            
    SELECT *,CAST (N'<H><r>' + REPLACE(levelsFeed, ',', '</r><r>')  + '</r> </H>' AS XML) AS [vals]
    FROM Feedbacks 
)  as d
CROSS APPLY d.[vals].nodes('/H/r') S(a)
~~~

then we decided to use cross apply and pivot with row_number as cardinal. it worked work well for some time but as i gathered from souces cardinal gives correct values only 99% and still did not feel that right cross apply and compute to get out to get output

~~~sql 

SELECT FeedbackID, [1],[2],[3],[4],[5]
FROM (
    SELECT *, ROW_NUMBER() OVER (PARTITION BY feedbackID ORDER BY (SELECT  null)) as rn 
FROM (
    SELECT FeedbackID, levelsFeed
    FROM Feedbacks 
) as a
CROSS APPLY dbo.Split(levelsFeed, ',')
) as SourceTable
PIVOT
(
    MAX(data)
    FOR rn IN ([1],[2],[3],[4],[5])
)as pivotable

~~~

at last we found one solution I was browsing the more into split string functionality I went through Microsoft blogs to learn more but it was mentioned there this will be the part of future releases and they would  support the Cardinals with split string functionality and so I decided to venture again inside Microsoft documentation to find out and it was there like using split_string split functionality passing the CSV column passing the delimiter and passing was 1 as a 3rd parameter it will return each column along with its cardinal source table is and TABL and we're close applying the on the CSV values and, is our delimiter and one is the cardinal value I don't have much knowledge while we're putting one is the 3rd value but we are getting at least desired output you can just cross apply on stream Anne pause the delimiter Anne po turn Max of values value is the strings splitted out of the comma separated values and for ordinal will be the output column that will be the how much number of columns you need out of this separation and we can just call P dot individual columns as separate values outside in our select statement there was a one interesting option to use user defined function but we sort of need to save some more deployment steps and use this are inbuilt functionality over built in user defined functions because built in functionality will give some advantage in performance and interpolated meeting so that was ours last output our result


~~~sql 
SELECT 
column1
,p.[1]
,p.[2]
,p.[3]
,p.[4]
,p.[5]
,p.[6]
,p.[7]
,p.[7]
FROM [ELT].[TABL] AS t
                CROSS APPLY STRING_SPLIT(t.[POV],',',1)
                PIVOT(MAX(value) FOR ordinal in ([1],[2],[3],[4],[5],[6],[7],[8])) P

~~~
