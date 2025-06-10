---
layout: post
title: Trying to understand Shell Scripting
date: 2025-06-09 15:09:00
description: Tutorial for basics of Shell scripting
tags: code, shellScripting
categories: shellScripts
featured: false
---

In this post, we are trying to understand about shell scripting. As per wikipedia, A shell script is a computer program designed to be run by a Unix shell, a command-line interpreter.
A script is command line program that contains a series of commands. The commands contained in the script are executed by interpreter. In case of shell scripts, the shell acts as interpreter and executes the commands listed in the script one after another.

Let's create our first shell script

Create a new file in your Unix / Linux operating system. I have ubuntu with me, so I will create new file using touch command

```shell
touch firstScript.sh
```
Then open file with your favorite editor, I am using VIM editor.
```shell
vim firstScript.sh
```
it will open file in edit mode, press "I" to go to insert mode

```shell
#!/bin/bash
echo "This is my first shell script"
```

Save the file, and go back to folder where you have saved this file
before you try to execute the script, make sure that it is executable and have enough permission to run 
Enter following command 

```shell
chmod 755 firstScript.sh 
```

Now, this is time to run our first shell script
Enter file name directly
```shell
.\firstScript.sh
```

We have successfully run our first shell script

![First Run](https://raw.githubusercontent.com/prasadrgavande/prasadgavande.github.io/refs/heads/master/assets/img/2.%20trying%20to%20understand%20shell%20scripts/run-first-script.png)

