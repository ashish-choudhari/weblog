---
layout: post
title:  "Creating sendmail.exe with python and password inside .EXE "
author: ashish
categories: [ Python, EXE ]
image: assets/images/16.jpg
---
There are lots of powerful things you can do with the python.sometime we are in need to automate the things like sending email and sometimes it requires to have this script scheduled and we can can not leave password expoes.
Sometimes lots many ports are blockeked only thing left to use send email with reqired information.

>what we can do?

we can schedule one script which can send automated emails.

== we have developed on python script and converted to EXE==

we can embed this `exe` inside `.bat`,`.cmd` or `.ps1` script. 

Assuming you have python installed on your machine

copy and edit you username and password.

Copy below python code to `EXE` whith help of `pyinstaller`.
==Some thing like this==

>C:\location\of\your_py_file>`pyinstaller` `--onefile` `--noconsole` `--icon=\path_to_ico_file\Log.ico` `\path_to_py_file\Log_reporter.py`


Once you have single exe file copy file to you windows script location

include inside you script like this:

`log_reporter.exe` `"pass the subject inside double quotes"` `"body inside double quotes"` `"receiver_email_id@domain.com"` `C:\location of all files to be attached\`

+ ~~strike through~~
+ ==highlight==
+ \*escaped characters\*


+==PY File==

```python
import smtplib
from email.mime.multipart import MIMEMultipart
from email.mime.base import MIMEBase
from email.mime.text import MIMEText
from email import encoders
import os
import sys
script_name= sys.argv[0]
subject = sys.argv[1]
body = sys.argv[2]
to_email = sys.argv[3]
folder_path = sys.argv[4]
print (subject)
print (body)
print (to_email)
print (folder_path)
## FILE TO SEND AND ITS PATH
filename = 'some_file.csv'
SourcePath = folder_path

msg = MIMEMultipart()
msg['From'] = 'user@company.com'
msg['To'] = to_email
msg['Subject'] = subject
#body = 'Body of the message goes in here'
msg.attach(MIMEText(body, 'plain'))

## ATTACHMENT PART OF THE CODE IS HERE
for file in os.listdir(SourcePath):
    attachment = open(os.path.join(SourcePath,file), 'rb')
    part = MIMEBase('application', "octet-stream")
    part.set_payload((attachment).read())
    encoders.encode_base64(part)
    part.add_header('Content-Disposition', "attachment; filename= %s" % file)
    msg.attach(part)

server = smtplib.SMTP('smtp.office365.com', 587)  ### put your relevant SMTP here
server.ehlo()
server.starttls()
server.ehlo()
server.login('user@company.com', 'Password@1')  ### if applicable
server.send_message(msg)
server.quit()
```

<script src="https://utteranc.es/client.js"
        repo="trinus-ashish/trinus-ashish.github.io"
        issue-term="pathname"
        theme="github-light"
        crossorigin="anonymous"
        async>
</script>
