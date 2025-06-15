---
layout: post
title: File Operations with shell script
date: 2025-06-15 15:09:00
description: Tutorial for basics file operations using Shell scripting
tags: code, shell
categories: scripting
featured: false
---

In this tutorial we will try to perform basic file operations like create, write, read, delete file using shell script.
Let's get started with this. 

As usual lets create new file using editor of your choice, I am using `vi` editor.

`vi fileOps.sh`

This will create new file and open editor for you.
We will create all function for common operations of file and then we will call them using switch cases, so that user can perform whatever operations he want!

Script will as user to enter file name first in which he want to perform operations 

```
#!/bin/bash

# prompt user to enter file name

read -p "Enter file name: " fileName
```

Lets create functions first. 

create function which will help us to create file

```
createFile() {
        touch "$fileName"
        echo "file created with name: $fileName"
}
```

Create function to read file
> It's good habit to check if file is exists before perform operaitons, so lets check if file exists or not, then perform read operation
> otherwise let user know that file does not exist
```
readFile(){
        if [ -f "$fileName" ]; then
                echo "content in file as below"
                cat $fileName
        else
                echo "file does not exists"
        fi
}
```
Create function to write file
```
writeFile(){
        echo "Enter content you want to write to file"
        read content
        echo "$content" >> "$fileName" ## append content mode
        echo "Content has been written to file"
}
```
> here we are using append mode to write content in file, because we dont wan to loose old data.
> if you dont want to use append mode then simply modify arraw operator as below
>  `echo "$content" > "$fileName"`

Create function to delete file
As mentioned above, first check if file exists or not before you delete the file
```
deleteFile(){
        if [ -f "$fileName" ]; then
                rm $fileName
                echo "file has been deleted"
        else
                echo "File does not exists"
        fi
}
```

Now, all our required functions are created, lets write function calling
Here we are going to as user which operations he want to perform using switch-case conditions
```
echo "Choice Operation you want to perform on file"
echo "1. Create File"
echo "2. Read file"
echo "3. Write file"
echo "4. Delete file"
read -p "Enter Choice" choice
case $choice in
        1) createFile ;;
        2) readFile ;;
        3) writeFile ;;
        4) deleteFile;;
        *) echo "Invalid choice"
esac
```

That's it, we are ready to execute this script
save the file, give required permissions to file

`chmod 777 fileOps.sh`

Now, run this executable using below command

`./fileOps.sh`
Once we run below command, it will run executable, let's create new file with name `sample.txt`

```
Enter file name: sample.txt
Choice Operation you want to perform on file
1. Create File
2. Read file
3. Write file
4. Delete file
Enter Choice 1
file created with name: sample.txt
```
check if file get created or not
```
root@Prabhu:~/shell# ls -ltr
total 12
-rwxrwxrwx 1 root root 916 Jun 15 23:15 fileOps.sh
-rw-r--r-- 1 root root   0 Jun 15 23:19 sample.txt
root@Prabhu:~/shell#

```

Run again to perform write operations 
```
root@Prabhu:~/shell# ./fileOps.sh
Enter file name: sample.txt
Choice Operation you want to perform on file
1. Create File
2. Read file
3. Write file
4. Delete file
Enter Choice3
Enter content you want to write to file
this is sample content added in file
Content has been written to file
root@Prabhu:~/shell#
```

Run again to perform read operations
```
root@Prabhu:~/shell# ./fileOps.sh
Enter file name: sample.txt
Choice Operation you want to perform on file
1. Create File
2. Read file
3. Write file
4. Delete file
Enter Choice2
content in file as below
this is sample content added in file
root@Prabhu:~/shell#
```

Run again to perform delete operations 
```
root@Prabhu:~/shell# ./fileOps.sh
Enter file name: sample.txt
Choice Operation you want to perform on file
1. Create File
2. Read file
3. Write file
4. Delete file
Enter Choice4
file has been deleted
```
check if file get deleted or not 
```
root@Prabhu:~/shell# ls -ltr
total 11
-rwxrwxrwx 1 root root 916 Jun 15 23:15 fileOps.sh
root@Prabhu:~/shell#
```






