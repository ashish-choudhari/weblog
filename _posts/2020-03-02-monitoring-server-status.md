---
layout: post
title:  "Daily server status"
author: ashish
categories: [ bat, Netezza,Status ]
image: assets/images/12.jpg
rating: 3
---
Monitoring the server is very important task but if you can receive the
status and updates daily when you wake it can be very good.

We monitor few servers we have already setup alert script apart from
alert that from application

But it can be really helpful if we receive information ahead of
occurrence of event that if space is almost at 90% it can be disastrous
because just copying one big database can take up to 100% within hour

If we already know that we are at 90 we can decide about deleting old
copies then move forward with out waiting to understand what really
happened we have below script scheduled in our cron for daily execution
we receive daily status updates, which helps us take action as soon as
we receive events.

Let me walk through the few important commands here.

`email_list =""`

Recepints list inside `double quotes` separated by `commas`

And `CC` list followed by if required for auditing purpose.

> /nz/support/bin/nz_show_topology -l|cut -d '|' -f 1-4| sed '3d' | column -t

> Information about each Dslice which really help in case of any of them is crossed the threshold to avoid data partition is full error.

> df -hP | column -t |tr -s 't' '|'

for linux `partition` if there is case of status is needed.

The cron I added is like this

> */5 * * * * /bin/bash -lc '/home/nz/scripts/nz_system_state.sh'

![walking]({{ site.baseurl }}/assets/images/12.jpg)

Finally, the script is added below

```sh
#Shell script to Generate Table skew  Report  on IBM Netezza System(nz_show_topology).
#!/bin/sh
# =============================================================================
# Netezza System Status Script 
# =============================================================================
export email_list="emailid1@company.com,emailid2@comapany.com"
export email_listcc="auditid@comapny.com"
(echo -e 'n'

echo -e 'n'

echo -e
'########################################################################################################################'

echo 'Storage Report per Dslice.'

echo -e 'n'

/nz/support-IBM_Netezza-7.1.0.5-150324-2105/bin/nz_show_topology -l
|cut -d '|' -f 1-4| sed '3d' | column -t

echo -e 'n'

echo -e
'########################################################################################################################'

echo 'Storage Space available .'

echo -e 'n'

df -hP | column -t |tr -s 't' '|'

echo -e 'n'

echo -e
'########################################################################################################################'

echo -e 'n'

) | /bin/mailx -s "Netezza system status report" -c $email_listcc $email_list

```
<div style="Margin:20px;">
            <img src="/assets/images/avatar_ashish.jpg" align="left" width="100" height="100" border="0" style="Margin:0 20px 20px 20px; background:#E79851;" />
            <p style="Margin:10px 20px 20px 20px; font:16px/1.25 sans-serif; color:#4CB3E8; text-align:justify;">
             
            <p align="right">
              <a href="">LinkedIn</a> |
              <a href="#">Link 2</a> |
              <a href="#">Link 3</a>
              <br><br>
            </p>
			</p>
</div>
[Ashish](mailto:ashishdtl@gmail.com?subject=[log])