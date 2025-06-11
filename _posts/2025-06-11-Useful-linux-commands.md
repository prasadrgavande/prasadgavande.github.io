---
layout: post
title: Useful linux commands
date: 2025-06-11 15:09:00
description: Some essential and useful linux commands for day to day operations
tags: linux, unix, commands
categories: commands
featured: false
---

In this post, we are going to take a look on some essential and useful linux / unix commands that are being used for day to day operations

## ls command
This command is use to list content in folder

```
ls [options] [directory or path]
```
example , if I am inside /etc/ folder, and if I execute command then I can see all folders inside /etc/ folder as below

```
root@Prabhu:/etc# ls
PackageKit              cron.daily      fstab       kernel          mime.types           polkit-1      selinux            sysctl.d
X11                     cron.hourly     fuse.conf   landscape       mke2fs.conf          profile       sensors.d          systemd
adduser.conf            cron.monthly    gai.conf    ld.so.cache     modprobe.d           profile.d     sensors3.conf      terminfo
alternatives            cron.weekly     glvnd       ld.so.conf      modules              protocols     services           timezone
apparmor                cron.yearly     gnutls      ld.so.conf.d    modules-load.d       python3       sgml               tmpfiles.d
apparmor.d              crontab         gprofng.rc  ldap            mtab                 python3.12    shadow             ubuntu-advantage
```
### option : -la
ls command have many options, some useful options are as below
```
ls -la
```
where  
-l : use a long listing format
-a, --all: do not ignore entries starting with .

Example: 
```
root@Prabhu:/etc# ls -la
total 808
drwxr-xr-x 88 root  root       4096 Jun 11 22:51 .
drwxr-xr-x 22 root  root       4096 Jun 11 22:46 ..
-rw-------  1 root  root          0 Sep 27  2024 .pwd.lock
-rw-r--r--  1 root  root        837 Sep 27  2024 .resolv.conf.systemd-resolved.bak
-rw-r--r--  1 root  root        208 Sep 27  2024 .updated
drwxr-xr-x  2 root  root       4096 Sep 27  2024 PackageKit
drwxr-xr-x  7 root  root       4096 Sep 27  2024 X11
-rw-r--r--  1 root  root       3444 Jul  5  2023 adduser.conf
drwxr-xr-x  2 root  root       4096 Sep 27  2024 alternatives

```

### option : -lt
if you want to sort the list content by time then use -lt option
```
ls -lt
```
where
-t: sort by time, newest first; see --time

Example: 
```
root@Prabhu:/etc# ls -lt
total 792
-rw-r--r-- 1 root  root      17607 Jun 11 22:51 ld.so.cache
drwxr-xr-x 4 root  root       4096 Jun 11 22:51 ssl
drwxr-xr-x 2 root  root       4096 Jun 11 22:51 gnutls
drwxr-xr-x 2 root  root       4096 Jun 11 22:50 init.d
```

### option: -ltr
if you want to sort the list content by time in reverse order then use -ltr option
```
ls -ltr
```
where 

Example: 
```
root@Prabhu:/etc# ls -ltr
total 792
-rw-r--r-- 1 root  root         45 Jan 24  2020 bash_completion
-rw-r--r-- 1 root  root      12813 Mar 27  2021 services
-rw-r--r-- 1 root  root         34 Aug  2  2022 ld.so.conf
-rw-r--r-- 1 root  root        367 Aug  2  2022 bindresvport.blacklist
-rw-r--r-- 1 root  root        552 Oct 13  2022 pam.conf
-rw-r--r-- 1 root  root       3144 Oct 17  2022 protocols
```
where: 
-r, --reverse: reverse order while sorting

There are many other options, but I use above options in my day to day life 



