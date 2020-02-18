---
layout: post
title:  "Creating daily working folder structure .bat."
author: john
categories: [ .BAT, tutorial ]
image: assets/images/16.jpg
beforetoc: "Often we one on daily repetative things that involve doing boring stuff, if this involve creating datewise folder tructure,then i got script for you"
toc: true
---
This script creates folders based on date,you can create multiple hierachy by date using variables.

Creating folder structure is not big deal but if involves creating multiple folder then its panfull.

Copy content save to `.bat` file at root ot directory where you need to create folder structure and then simply double click.

+===Use `%foldername%` as per requirement this script can be `task scheduler` run condition can be on `user login` then no need run at all ===


```bat
for /f "skip=1" %%i in ('wmic os get localdatetime') do if not defined fulldate set fulldate=%%i
set year=%fulldate:~0,4%
set month=%fulldate:~4,2%
set day=%fulldate:~6,2%
set foldername=%day%.%month%.%year%
md %foldername%

md %foldername%\_Documentation
md %foldername%\_ProjectManagement
md %foldername%\Releases
md %foldername%\Source
md %foldername%\Source\Trunk
md %foldername%\Source\Trunk\Library
md %foldername%\Source\Trunk\Projects
md %foldername%\Source\Trunk\Tests
```
