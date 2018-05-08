---
layout: post
category: Linux 
title: How to free up /boot
tagline: by Snail
tags: 
  - Ubuntu
  - Linux
  - Operating system
published: true
---

Sometimes it gives a error saying no enough space left when I try to install some new packages which causes dependency problem preventing kernels from being properly configured. Later I found old kernels could take up significant amount of space if you don't clean up regularly. 

Here is a simple solution to tackle this problem.
```bash
# Check the availability of each disk
sudo fdisk -l
df -h

# Check the kernel you are currently on
uname -r

# List all the old kernels available to delete
dpkg -l linux-{image,headers}-"[0-9]*" | awk '/^ii/{ print $2}' | grep -v -e `uname -r | cut -f1,2 -d"-"` | grep -e '[0-9]'

# Delete them manually
sudo dpkg --purge <results_shown_in_previous_command>
```