---
layout: post
title:  "Git Auto Push"
author: Ashish
categories: [ git, bash ]
image: assets/images/4.jpg
---

**Git auto push.sh**

>#!/bin/sh

>cd /mnt/LOCAL_REPO_FOLDER/NAME.github.io

>git status

>git add .

>git commit -am "made changes"

>git push https://github.com/REMOTE_USER/YOUR.git BRANCH

>echo Press Enter...

