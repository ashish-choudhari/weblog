---
layout: post
title:  "Email to SMS"
author: ashish
categories: [ SMS, python,email,sms_gateway ]
image: assets/images/7.jpg
---
> This blogg is under construction,there will be changes being updated as i add. 

> Apologies for spelling  mistaks,sometimes i add/forget word in writing flow

There was a time when mobile_no@carrier_id used to send Emaail content via SMS reasonably cheaper as was avaialble to no day its very rare cost some extra.

We received the strange requiremwnt from the client they wanted to receive email contents by sms it was possible but there was something extra they needed.

that was they wnated to receive this only for morning two hours when they could not acces their mail box,and they liked to receive some emergency alerts right away,so part of solution for this requirement we developed script as POC.

better choice to achieve this we went ahead with python,

i will be adding walk through example that we managed to pull with google search(by deviding problem to chunks and then adding al-together)

> from tkinter import *	for some gui capability
> from tkinter.ttk import * 	--\\--
> import pickle 	secretly storing credential- because we were thinking keep running this script on server.
> import datetime 	for getting latest emails(date-time they received).
> import email Email opereations
> import imaplib	--\\--
> import mailbox	--\\--
> import requests	connecting SMS gateway with HTTP GET requests
> import pycron for scheduling email to sms operation for specific timeslot.
> import time
> import re	for text clean-up
> import sys	for local system operations
> import os	--\\--

some fuctions to perform cleanup opearations

```python

## clean out HTML body to plain text

def cleanhtml(raw_html):
  cleanr = re.compile('<.*?>')
  cleantext = re.sub(cleanr, '', raw_html)
  return cleantext

## cleaning JSON string to plain text
  
def cleanjson(raw_json):
  cleanr = re.compile('P {.*?}')
  cleantxt = re.sub(cleanr, '', raw_json)
  return cleantxt

## Stripping empty lines
  
def strip_empty_lines(s):
    lines = s.splitlines()
    while lines and not lines[0].strip():
        lines.pop(0)
    while lines and not lines[-1].strip():
        lines.pop()
    return '\n'.join(lines)

```

> mail.select(FOLDER_NAME)

`FOLDER_NAME` is used to scan specific folder for for cnversion.

> result, data = mail.uid('search', None, "UNSEEN")

`UNSEEN` for reading only unread mails.

> if pycron.is_now(CRONTAB):

`CRONTAB` will open if action only if current schedule fall under the cron schedule.

Below is the code snippet for SMS gateway.

```python
url = "https://www.fast2sms.com/dev/bulk"
querystring = {"authorization":"autherisation_key","sender_id":"FSTSMS","message":subject+"\n"+body_txt[0:int(character_limit)],"language":"english","route":"p","numbers":mobileto}
headers = {'cache-control': "no-cache"}
response = requests.request("GET", url, headers=headers, params=querystring)
depositLabel.config(text=""+local_message_date+"\n"+subject)
print(response.text
```
url : This endpoint for SMS gateway
querystring : is the request to sms gateway

**Table of Contents**

[TOCM]
[TOC]
#"authorization":"autherisation_key"	API key from SMS gateway
##"sender_id":"FSTSMS"	Sender Id
##"message":subject+"\n"+body_txt[0:int(character_limit)] 
### Adding subject and body text will stripped to character_limit i.e. 140 chars counted in 1 SMS
##"language":"english" This is unicode or ASCII
##"route":"p" Mode of sending promotional/Transactional (Promotional have some restrictions i.e. will not delivered in night)
##"numbers":mobileto Mobile nos separaed by by commas

Create pickle inside working directory if not created will be updated by program later

```python

with open("test.pickle", "rb") as f:
    object = pickle.load(f)
    EMAIL_ACCOUNT=object[0]
    PASSWORD=object[1]
    FOLDER_NAME=object[2]
    CRONTAB=object[5]
    mobileto=object[4]
    character_limit=object[3]

```

I will keep editing this later to add some explanation.

#Heading 1 link [Rs 50 Worth Free SMS for Dev](https://www.fast2sms.com/dashboard/settingd "SMS Gateway")

```python

from tkinter import *
from tkinter.ttk import *
import pickle
import datetime
import email
import imaplib
import mailbox
import requests
import pycron
import time
import re
import sys
import os
TAG_RE = re.compile(r'<[^>]+>')
ee1=""
ee2=""
ee3=""
ee4=""
ee6=""
ee7=""
eem=""
eeh=""
eed=""
eedow=""
eemnth="" 
FOLDER_NAME=""
CRONTAB=""
EMAIL_ACCOUNT =""
PASSWORD = ""
character_limit=50
running = True 
def cleanhtml(raw_html):
  cleanr = re.compile('<.*?>')
  cleantext = re.sub(cleanr, '', raw_html)
  return cleantext
  
def cleanjson(raw_json):
  cleanr = re.compile('P {.*?}')
  cleantxt = re.sub(cleanr, '', raw_json)
  return cleantxt
  
def strip_empty_lines(s):
    lines = s.splitlines()
    while lines and not lines[0].strip():
        lines.pop(0)
    while lines and not lines[-1].strip():
        lines.pop()
    return '\n'.join(lines)
	
def save_entry_fields():
    text_p = [e1.get(),e2.get(),e3.get(),e4.get(),e6.get(),e7.get(),em.get(),eh.get(),ed.get(),edow.get(),emnth.get()]
    with open("test.pickle","wb") as f:
        pickle.dump(text_p, f)
    Load_entry_fields()
    show_entry_fields()
    stop()
    start()

def scanning():
    if running:  # Only do this if the Stop button has not been clicked
        go_through_emails()

    # After 1 second, call scanning again (create a recursive loop)
    master.after(5000, scanning)

def go_through_emails():
                email_from=""
                email_to=""
                #mobileto=""
                local_message_date=""
                subject=""
                body_txt=""
                mail = imaplib.IMAP4_SSL('outlook.office365.com')
                #print(EMAIL_ACCOUNT)
                #print(PASSWORD)
                mail.login(EMAIL_ACCOUNT, PASSWORD)
                mail.list()
                mail.select(FOLDER_NAME) ## Sent Items inbox
                result, data = mail.uid('search', None, "UNSEEN") # (ALL/UNSEEN)
                i = len(data[0].split())
                print (i)
                for x in range(i):
                    latest_email_uid = data[0].split()[x]
                    result, email_data = mail.uid('fetch', latest_email_uid, '(RFC822)')
                    raw_email = email_data[0][1]
                    raw_email_string = raw_email.decode('utf-8')
                    email_message = email.message_from_string(raw_email_string)
                    date_tuple = email.utils.parsedate_tz(email_message['Date'])
                    if date_tuple:
                        local_date = datetime.datetime.fromtimestamp(email.utils.mktime_tz(date_tuple))
                        local_message_date = "%s" %(str(local_date.strftime("%a, %d %b %Y %H:%M:%S")))
                    email_from = str(email.header.make_header(email.header.decode_header(email_message['From'])))
                    email_to = str(email.header.make_header(email.header.decode_header(email_message['To'])))
                    subject = str(email.header.make_header(email.header.decode_header(email_message['Subject'])))
                    body_txt=subject+""
                    for part in email_message.walk():
                       # if part.get_content_type() == "text/plain":
                            body = part.get_payload(decode=True)
                            body_txt = strip_empty_lines(cleanjson(cleanhtml(body.decode('utf-8')))).strip("\n").replace("\t", "").replace("\n", "")
                            print('outside cron')
                            if pycron.is_now(CRONTAB):
                                print('inside cron')
                                url = "https://www.fast2sms.com/dev/bulk"
                                querystring = {"authorization":"auth_key","sender_id":"FSTSMS","message":subject+"\n"+body_txt[0:int(character_limit)],"language":"english","route":"p","numbers":mobileto}
                                headers = {'cache-control': "no-cache"}
                                response = requests.request("GET", url, headers=headers, params=querystring)
                                depositLabel.config(text=""+local_message_date+"\n"+subject)
                                print(response.text)
                            else:
                                continue
                            print ("From: %s\nTo: %s\nDate: %s\nSubject: %s\n\nBody: \n\n%s" %(email_from, email_to,local_message_date, subject, body.decode('utf-8')))
                            file_name = "email_" + str(x) + ".txt"
                            output_file = open(file_name, 'w')
                            output_file.write("From: %s\nTo: %s\nDate: %s\nSubject: %s\n\nBody: \n\n%s" %(email_from, email_to,local_message_date, subject, body.decode('utf-8')))
                            output_file.close()
                        #else:
                         #   continue
                #print ("From: %s\nTo: %s\nDate: %s\nSubject: %s\n\nBody:%s\n to : %s\n" %(email_from, email_to,local_message_date, subject, body_txt[0:159],""+mobileto))

def start():
    """Enable scanning by setting the global flag to True."""
    Load_entry_fields()
    disable_entry_fields()	
    global running
    running = True

def stop():
    """Stop scanning by setting the global flag to False."""
    global running
    running = False


def disable_entry_fields():
    e1.config(state='disabled')
    e2.config(state='disabled')
    e3.config(state='disabled')
    e4.config(state='disabled')
    e6.config(state='disabled')
    e7.config(state='disabled')
    em.config(state='disabled')
    eh.config(state='disabled')
    ed.config(state='disabled')
    edow.config(state='disabled')
    emnth.config(state='disabled')


def enable_entry_fields():
    e1.config(state='enabled')
    e2.config(state='enabled')
    e3.config(state='enabled')
    e4.config(state='enabled')
    e6.config(state='enabled')
    e7.config(state='enabled')
    em.config(state='enabled')
    eh.config(state='enabled')
    ed.config(state='enabled')
    edow.config(state='enabled')
    emnth.config(state='enabled')
	
        
def show_entry_fields():
    email=e1.get()
    password=e2.get()
    folder=e3.get()
    character_limit=e4.get()
    mibile_nos=e6.get()
    mobileto=mibile_nos
    request_query_entry=e7.get()
    crontab=str(""+em.get()+" "+eh.get()+" "+ed.get()+" "+edow.get()+" "+emnth.get()+"")
    print("Email: %s\nPassword: %s\nFolder: %s\nCharacter Limit: %s\nMobile Nos: %s\nReq query String: %s\n Crontab :%s" % (email,password,folder,character_limit,mibile_nos,request_query_entry,crontab))
def restart_program():
    """Restarts the current program.
    Note: this function does not return. Any cleanup action (like
    saving data) must be done before calling this function."""
    save_entry_fields()
    Load_entry_fields()
    show_entry_fields()
    python = sys.executable
    os.execl(python, python, * sys.argv)

def Load_entry_fields():
    with open("test.pickle", "rb") as f:
        object = pickle.load(f)
        show_entry_fields()
        e1.delete(0,END)
        e2.delete(0,END)
        e3.delete(0,END)
        e4.delete(0,END)
        e6.delete(0,END)
        e7.delete(0,END)
        em.delete(0,END)
        eh.delete(0,END)
        ed.delete(0,END)
        edow.delete(0,END)
        emnth.delete(0,END)
        EMAIL_ACCOUNT=object[0]
        PASSWORD=object[1]
        FOLDER_NAME=object[2]
        CRONTAB=object[5]
        mobileto=object[4]
        character_limit=object[3]
        ee1=object[0]
        ee2=object[1]
        ee3=object[2]
        ee4=object[3]
        ee6=object[4]
        ee7=object[5]
        eem=object[6]
        eeh=object[7]
        eed=object[8]
        eedow=object[9]
        eemnth=object[10]		
        e1.insert(0, ee1)
        e2.insert(0, ee2)
        e3.insert(0, ee3)
        e4.insert(0, ee4)
        e6.insert(0, ee6)
        e7.insert(0, ee7)
        em.insert(0, eem)
        eh.insert(0, eeh)
        ed.insert(0, eed)
        edow.insert(0, eedow)
        emnth.insert(0, eemnth)

master = Tk()
Label(master,text="Email").grid(row=0)
Label(master,text="Password").grid(row=1)
Label(master,text="Folder").grid(row=2)
Label(master,text="Char Limit").grid(row=3)
Label(master,text="Mobile Nos").grid(row=4)
Label(master,text="Query sring").grid(row=5)
Label(master,text="Minute").grid(row=0,column=3)
Label(master,text="Hour").grid(row=1,column=3)
Label(master,text="Day").grid(row=2,column=3)
Label(master,text="Day of Week").grid(row=3,column=3)
Label(master,text="Month").grid(row=4,column=3)
depositLabel = Label(master,text="Log...............", borderwidth=2, relief="groove")
depositLabel.grid(row=8,column=2,columnspan=4,rowspan=4)
e1 = Entry(master)
e2 = Entry(master,show="*")
e3 = Entry(master)
e4 = Combobox(master)
e4['values']= (140, 280,"SMS Char LIMI")
e4.current(1)
e6 = Entry(master)
e7 = Entry(master)
em = Combobox(master)
em['values']= ("*",0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,"Minutes")
em.current(1)
eh = Combobox(master)
eh['values']= ("*",0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,"Hour")
eh.current(1)
ed = Combobox(master)
ed['values']= ("*",1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,"Day of Month")
ed.current(1)
edow = Combobox(master)
edow['values']= ("*",1,2,3,4,5,6,7,8,9,10,11,12,"Month")
edow.current(1)
emnth = Combobox(master)
emnth['values']= ("*",0,1,2,3,4,5,6,7,"Day of the week")
emnth.current(1)
e1.grid(row=0, column=1)
e2.grid(row=1, column=1)
e3.grid(row=2, column=1)
e4.grid(row=3, column=1)
e6.grid(row=4, column=1)
e7.grid(row=5, column=1)
em.grid(row=0, column=4)
eh.grid(row=1, column=4)
ed.grid(row=2, column=4)
edow.grid(row=3, column=4)
emnth.grid(row=4, column=4)

with open("test.pickle", "rb") as f:
    object = pickle.load(f)
    EMAIL_ACCOUNT=object[0]
    PASSWORD=object[1]
    FOLDER_NAME=object[2]
    CRONTAB=object[5]
    mobileto=object[4]
    character_limit=object[3]

master.after(5000, scanning)

Button(master,text='Quit',command=master.quit).grid(row=11,column=1,pady=4,sticky = W)
Button(master,text='Show Current', command=show_entry_fields).grid(row=8,column=1,pady=4,sticky = W)
Button(master,text='Run', command=start).grid(row=8,column=0,pady=4,sticky = W)
Button(master,text='Restart', command=restart_program).grid(row=8,column=2,pady=4,sticky = W)
Button(master,text='Freeze', command=disable_entry_fields).grid(row=9,column=0,pady=4,sticky = W)
Button(master,text='Unfreeze', command=enable_entry_fields).grid(row=9,column=1,sticky=W,pady=4)
Button(master,text='Load', command=Load_entry_fields).grid(row=10,column=0,sticky=W,pady=4)
Button(master,text='Save', command=save_entry_fields).grid(row=10,column=1,sticky=W,pady=4)
Button(master,text='Stop', command=stop).grid(row=11,column=0,pady=4,sticky = W)
master.mainloop()

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