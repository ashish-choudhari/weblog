---
layout: post
title:  "Debugging netework issue"
author: ashish
categories: [ bat, network ]
image: assets/images/9.jpg
---
Sometime random things fails with error related to network and its very hard check weather its related to network and network it's huge.

>Once you leave to device you have path from very huge web to scout.

There are few tests to narrow down issue\>

>First you can try to reach any publicly available website i.e.`google.com`

If at all you are not inside firewall few thing might not work.

But it is hard to track problems occurring during off hour you can't check at moment `what you can do?`.

You can try few commands.

>Head over cmd `press` `windows + R` and type `cmd.exe` or press `windows` and type cmd hit `enter`

**Ipconfig**\
Ipconfig is a Console Command which can be issued to the Command Line
Interpreter (or command prompt) to display the network settings
currently assigned to any or all network adapters in the machine. This
command can be utilised to verify

**netstat**\
Displays active TCP connections, ports on which the computer is
listening, Ethernet statistics,
the routing table, IPv4
statistics (for the IP, ICMP, TCP, and UDP protocols), and IPv6
statistics (for the IPv6, ICMPv6, TCP over IPv6, and UDP over IPv6
protocols). Used without parameters, netstat displays active TCP
connections.

**tracert**\
The tracert command is used to visually see a network packet being sent
and received and the number of hops required for that packet to get to
its destination.

Users with Microsoft Windows 2000 and Windows XP who need additional
information network latency and network loss should also consider using
the path ping command.

If at all there is problem inside any hop (slowness can be identified).

[**ping**]
Helps in determining TCP/IP Networks IP address as well as determine
issues with the network and assists in resolving them.

**pathping**\
Provides information about network latency and network loss at
intermediate hops between a source and destination. Path ping sends
multiple Echo Request messages to each router between a source and
destination over a period and then computes results based on the packets
returned from each router.

**telnet**\
Telnet is software that allows users to remotely access another computer
such as a server, network device, or other computer. With telnet users
can connect to a device or computer, manage a network device, setup a
device, transfer files, etc.

Telnet required to enable at source as well as server (very useful to
finding out if any specific port is open or not)

**ftp**\
FTP is short for File Transfer Protocol; this page contains additional
information about the FTP command and help using that command in Unix
and MS-DOS (Windows).

**route**\
The function and syntax of the Windows ROUTE command is similar to the
UNIX or Linux route command. Use the command to manually configure the
routes in the routing table.

**arp**\
Displays, adds, and removes arp information from network devices.

**nslookup**\
Displays information that you can use to diagnose Domain Name System
(DNS) infrastructure. Before using this tool, you should be familiar
with how DNS works. The Nslookup command-line tool is available only if
you have installed the TCP/IP protocol.

**nbtstat**\
MS-DOS utility that displays protocol statistics and current TCP/IP
connections using NBT.

**netsh**\
One common way of using netsh is to reset the TCP/IP in Windows 2k/XP

Type this in Run or DOS Window -- \"netsh int ip reset\"

In Windows XP you can run a graphical diagnostics by typing \"netsh diag
gui\" into the run dialogue box. (This may take a little time to
startup)

**getmac**

DOS command used to show both local and remote MAC addresses. When run
with no parameters (ie. getmac) it displays MAC addresses for the local
system. When run with the /s parameter (eg. getmac /s \\\\foo) it
displays MAC addresses for the remote computer. When the /v parameter is
used, it also displays the associated connection name and network
adapter name.


**Find All Active/Used IP Addresses on Your Network**\
There is a really neat way that you can quite easily find all
active/used IP Addresses on your network.

Open the Command Prompt and type in the following:

FOR /L %i IN (1,1,254) DO ping **-n 1** 192.168.10.%i \| FIND /i
\"Reply\"\>\>c:\\ipaddresses.txt

Change `192.168.10` to match you own network.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--



Below is script we scheduled during our recent netework troubleshhoting
the script was running during production cycle with ip addresses that
need to be tested along with (8.8.8.8 & 1.1.1.1)

`list` is list of ip addresses will be pinged continously 
`log` is location probably inside current relative path.
you can chage duration betwwen ping curently 2 seconds.

I have used this [**script**](http://scripts.dragon-it.co.uk/scripts.nsf/MainFrame?OpenFrameSet&Frame=East&Src=%2Fscripts.nsf%2Fdocs%2Fbatch-ping-list-of-hosts-2!OpenDocument%26AutoFramed).

Thanks to origional creater.

+==.BAT File==

```bat
\@echo off

SETLOCAL enabledelayedexpansion

set list= 1.1.1.1,8.8.8.8

REM location for script log file

set log=\"logfile.txt\"

:Start

for %%a in (%list%) do (

REM This waits upto 2 seconds for a PING response, adjust as wanted

ping -a -n 1 -w 2000 %%a \> pingres.txt

set OK=!errorlevel!

for /f \"tokens=2 delims= \" %%N in (\'find /i \"Pinging\" \^\<
pingres.txt\') do set name=%%N

if NOT \[!ok!\]==\[0\] (

set /p p=%date% %time% FAIL to %%a \... \<nul

type pingres.txt \| find /v \":\" \| find \".\"

(set /p p=%date% %time% FAIL to %%a \... \<nul

type pingres.txt \| find /v \":\" \| find \".\" )\>\>%log%

) ELSE (

for /f \"tokens=7 delims=\^\<= \" %%T in (\'find \"Reply\" \^\<
pingres.txt\') do set pingtime=%%T

echo %date% %time% OK to %%a \[!pingtime!\] - !name!

echo %date% %time% OK to %%a \[!pingtime!\] - !name! \>\>%log%

)

)

PING 127.0.0.01 -n 1 -w 5000 \> NUL

Goto Start
```

