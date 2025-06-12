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

## Commands for directory operations 
### ls 
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
#### option : -la
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

#### option : -lt
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

#### option: -ltr
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

### pwd 
This command is used to print full path of current directory
```
pwd [options]
```
Example
```
root@Prabhu:/etc# pwd
/etc
```
It has several options as below 
-L, --logical: use PWD from environment, even if it contains symlinks
-P, --physical: avoid all symlinks

### cd 
This command is used for navigation 
```
cd [directory path where you want to go]
```
example, if I am inside /etc/ folder and I want to go to pm folder inside same directory then I will use following command
```
root@Prabhu:/etc# pwd
/etc
root@Prabhu:/etc# cd pm
root@Prabhu:/etc/pm#
```

if you want to go to some other folder of specific path then we can pass full directory path as below
```
root@Prabhu:/etc/pm# pwd
/etc/pm
root@Prabhu:/etc/pm# cd /var/run/log
root@Prabhu:/var/run/log#
```

cd command also provides some shortcuts as below

`cd` : returns to current user's home directory

`cd ..` : go to one level up

`cd -` : go to previous directory 


### mkdir 
This command is used to create new directory
```
mkdir dir_name
```
Example
```
root@Prabhu:~/prasad# mkdir testing
root@Prabhu:~/prasad# ls
testing
root@Prabhu:~/prasad#
```

### rmdir 
This command is used to remove empty directory
```
rmdir dir_name
```
Example
```
root@Prabhu:~/prasad# ls
testing
root@Prabhu:~/prasad# rmdir testing
root@Prabhu:~/prasad# ls
root@Prabhu:~/prasad#
```
> Note: this command can only delete empty folders, if you have any file / folder inside this folder then this command cannot be used.


## Commands for file operations 

### touch
This command is used to create new empty file in folder or to update timestamp of existing file

```
touch [option] file_name.extension
```
Example:
```
root@Prabhu:~/prasad# ls
root@Prabhu:~/prasad#
root@Prabhu:~/prasad# touch test.txt
root@Prabhu:~/prasad# ls
test.txt
root@Prabhu:~/prasad#
```

### cp
This command is used to copy files and folders from one location to another location
```
cp [options] source destination
```
Example
```
root@Prabhu:~# cp prasad/test.txt shell/
root@Prabhu:~# cd shell/
root@Prabhu:~/shell# ls -ltr
total 32
-rwxr-xr-x 1 root root  16 Jun 11 23:33 test.txt
root@Prabhu:~/shell#
```

If you want to copy whole directory including its files and sub folders then need to use option -r along with this.

Example: if I want to copy folder /etc/ssh along with its sub folders and files then execute commands as below

```
root@Prabhu:~#
root@Prabhu:~# cp -r /etc/ssh/ prasad
root@Prabhu:~# cd prasad
root@Prabhu:~/prasad# ls -ltr
total 8
-rwxrwxrwx 1 root root   16 Jun 11 23:31 test.txt
drwxr-xr-x 3 root root 4096 Jun 11 23:38 ssh
root@Prabhu:~/prasad# cd ssh
root@Prabhu:~/prasad/ssh# ls -ltr
total 8
drwxr-xr-x 2 root root 4096 Jun 11 23:38 ssh_config.d
-rw-r--r-- 1 root root 1649 Jun 11 23:38 ssh_config
root@Prabhu:~/prasad/ssh#
```

### mv 
This command is used to move the file or folder from one location to another or to rename the file 

```
mv source destination
```

Example 
if you want to move file from one folder to another
```
root@Prabhu:~# mv prasad/test.txt shell/test.txt
root@Prabhu:~# cd shell
root@Prabhu:~/shell# ls -ltr
total 12
-rwxrwxrwx 1 root root  16 Jun 11 23:31 test.txt
root@Prabhu:~/shell#
```

if you want to rename the file, then keep destnation same but change filename while execute mv command
```
root@Prabhu:~/shell# mv test.txt test2.txt
root@Prabhu:~/shell# ls -ltr
total 12
-rwxrwxrwx 1 root root  16 Jun 11 23:31 test2.txt
```

### cat
This command is used to print the content of file
```
cat [options] filename
```
Example, if you want to see content in file
```
root@Prabhu:~/shell# cat file.txt
 this is prasad
root@Prabhu:~/shell#
```

Example, if you want to read the content of one file and put it into another file as below

```
root@Prabhu:~/shell# cat test2.txt > file.txt
root@Prabhu:~/shell# less file.txt

 this is prasad
file.txt (END)
```

### grep (Global regualar expression print)
This command is very helpful if you want to search specific word from files
```
grep [options] pattern [filename]
```

Example: to search keyword in any specific file
```
root@Prabhu:~/shell# grep "prasad" file.txt
 this is prasad
```

if you want to display line number in search result
```
root@Prabhu:~/shell# grep -n "prasad" file.txt
1: this is prasad
root@Prabhu:~/shell#
```

if you want to display count of number matches your pattern then use command sa below
```
root@Prabhu:~/shell# grep -c "prasad" file.txt
1
root@Prabhu:~/shell#
```

if you want to check if any specific keyword exists in which all files in folder
```
root@Prabhu:~/shell# grep -l "prasad" *
exit.sh
file.txt
returnCode.sh
test2.txt
```

### find 
This command is used to search for file and directories based on name, type, size, data etc 
```
find [path] -options [expression]
```
where
Path : where to start for sarch 
Expression: Criteria like filename, size etc 
Example, if you want to search if file with specific name in folder "shell"
```
root@Prabhu:~# find shell -name "file.txt"
shell/file.txt
root@Prabhu:~#
```

if you want to search by type/ extension 
```
root@Prabhu:~# find shell -name *.txt
shell/file.txt
shell/test2.txt
root@Prabhu:~#
```

if  you want to search all files with specific permissions

```
root@Prabhu:~# find shell -perm 777
shell/for.sh
shell/functions.sh
shell/if.sh
shell/returnCode.sh
shell/test2.txt
shell/exit.sh
root@Prabhu:~#
```

use `grep` and `find` together to search for specific keyword in file

```
root@Prabhu:~# find ./ -type f -name "*.txt" -exec grep "prasad" {} \;
 this is prasad
root@Prabhu:~#
```

### awk
This command is used for pattern scanning and processing
```
awk options 'pattern {action}' input_file > output_file
```
Example
Print all lines in file
```
root@Prabhu:~/shell# awk '{print}' file.txt
 this is prasad
root@Prabhu:~/shell#
```

print specific column 
```
root@Prabhu:~/prasad# awk '{print $1,$4}' sample.txt

What Ipsum?

Lorem simply
Why use

It long

Where come

Contrary belief,

The of
Where get

There variations
root@Prabhu:~/prasad#
```


### sed
This command used to replace content in the file

```
sed 's/old_content/new_content/' file_name
```
Example, let's replace 'prasad' by 'prabhu' in file.txt

```
root@Prabhu:~/shell# cat file.txt
 this is prasad
```
as you see file contain `this is prasad`

now execute `sed` command to replace

```
root@Prabhu:~/shell# sed 's/prasad/prabhu/' file.txt
 this is prabhu
root@Prabhu:~/shell#
```

### less
This command is used to read / print few lines in files, then we can enter space to remaining content and so on

Below is syntax 
```
less file_name
```
> 1: enter q to exit fron reading mode
> 2: if you want to search anything, then enter / followed by keyword example `/prasad` then press enter
> 3: if you are in search mode, and you want to find next occurrence then press `n`
> 4: if you are in search mode, and you want to find previous occurrence then press shift + `n`

### head
This command is used to print first few lines in file
Below is syntax 
```
head file_name
```

### tail
This command is used to print last few lines in file
Below is syntax 
```
tail file_name
```

### diff
This command is used to check difference in two files in same folder or different folder
```
diff file1 file2
```
example, if I want to find difference in file.txt and test2.txt in same folder
```
root@Prabhu:~/shell# diff file.txt test2.txt
1c1
<  this is prasad
---
>  this is prabhu
root@Prabhu:~/shell#
```

















