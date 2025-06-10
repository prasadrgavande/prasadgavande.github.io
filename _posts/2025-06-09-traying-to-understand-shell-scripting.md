---
layout: post
title: Trying to understand Shell Scripting
date: 2025-06-09 15:09:00
description: Tutorial for basics of Shell scripting
tags: code, shell
categories: scripting
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


Let's further discuss more about shell scripting

## Variables 
You can use variables in shell script. Variables simply storage locations which have name. We can assign value to those variable for later use. Example, any operations, manipulation etc.
Syntaxt to create variable is very simple

`Variable_name="some value"`

Once variable created, you can retrieve value inside variable using `${variable_name}`

let's create shell script with variable and try to retrieve that value

Create another shell script and write code as below

```shell
#!/bin/bash
MY_VAR="Bash"
echo "This is ${MY_VAR} command, running in shell scirpt"
```
Grant permission as we have given in previous example

then run this shell script

![variable](https://raw.githubusercontent.com/prasadrgavande/prasadgavande.github.io/refs/heads/master/assets/img/2.%20trying%20to%20understand%20shell%20scripts/variable.png)

## Conditional Statements
### if statement
We can use if statement same as other programming languages in shell script.
In shell scripts, we have to use if-then-fi, fi is to tell interpreter that, if condition block has been ended. 

below is syntax 
```shell
if [ condition ]
then
  command 1
  command 2
  ...
fi
```

lets take an example

```shell
#!/bin/bash
firstNumber=10
secondNumber=10

if [ $firstNumber = $secondNumber ]
then
        echo "both numbers are equal"
fi
```

save the file , grant permission and try to run this shell script
you will see output as below

`both numbers are equal`

> NOTE: Remember to add one space in between `if` and `[` otherwise you will get an error


### if-else statement
We can also add else statement along with if , below is its syntax

```shell
if [ condition ]
then
  command 1
  command 2
  ...
else
  command 3
  command 4
  ...
fi
```

let's continue to expand our previous example but here we will change value of variable `firstNumber` and `secondNumber`
```shell
#!/bin/bash
firstNumber=50
secondNumber=100

if [ $firstNumber = $secondNumber ]
then
        echo "both numbers are equal"
else
        echo "both numbers are not equal"
fi
```

Run this shell script and you will see results as below

`both numbers are not equal`

### if- else if - else statements
Shell script also allow us for else-if with below syntax
```shell
if [ condition 1 ]
then
  command 1
  command 2
  ...
elif [ condition 2 ]
then
  command 3
  command 4
  ...
else
  command 5
  command 6
  ...
fi
```

lets further expand our previous example again
```shell
#!/bin/bash
firstNumber=10
secondNumber=100

if [ $firstNumber = $secondNumber ]
then
        echo "both numbers are equal"
elif [ $firstNumber = 10 ]
then
        echo "first number is 10"
else
        echo "both numbers are not equal"
fi
```
Now, as `firstNumber` assigned to 10, it will go to `else-if` statement block and result will be 

`first number is 10` 

## Loops
### for loop
if you want to perform any action number of times then, we can make use of for loop. Below is syntax for "For" loop

```shell
for variable_name in item1 item2 ... itemN
do
  command 1
  command 2
  ...
done
```
lets take an example of this 

```shell
#!/bin/bash

for color in red green blue
do
        echo "Selected color in this iteration is: ${color}"
done
```

like before, you need to grant permission and then execute this shell script. Result will be as below

![for loop](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/2.%20trying%20to%20understand%20shell%20scripts/for.png?raw=true)

## Comments
We can add comments in shell script using `#` symbol.
Anthing after # symbol is consider as comments and this command will not get executed 
exmaple 
```shell
#!/bin/bash
for color in red green blue # define the colors here 
do
        echo "Selected color in this iteration is: ${color}"
done
```
in above script at second line we have added comment as `# define the colors here ` this is just comment, and this will not be executed 

## Return codes
Return code can be used in script for error checking, if return code is 0 meaning command executed successfully, if it is non-zero value, that means there is some error
We can use special variable `$?` to check return code if previously executed command. 

let's take an example, if I try `ls` command for any such path which does not exist , then our script will return non-zero code.

```shell
#!/bin/bash
ls /prasad/test
echo "$?"
```

`/prasad/test` this directory does not exists in my machine, so when I run script, I will get output as below

![ReturnCode](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/2.%20trying%20to%20understand%20shell%20scripts/returnCode1.png?raw=true)

As we can see it has return code as 2, as it is not zero, we can say there is some error in command execution. 

Let's take another example, in this example we are going to create some file and try to see what it will return
```shell
#!/bin/bash
touch prasad.txt
echo "text file has been created"
echo "$?"
```

Run this script 
![ReturnCode2](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/2.%20trying%20to%20understand%20shell%20scripts/returnCode2.png?raw=true)

as we can see it has returned code as `0` and  created new file with name `prasad.txt`

## Exit Command
We can defined our own exit codes in shell script, simply use `exit` command in script and follow it with number from 0 to 255.
We can add exit command anywhere in script. Whenever, script reached to exit command it will stop running.

```shell
#!/bin/bash
cat prasad.txt
if [ "$?" -eq "0" ]
then
        echo "Command success"
        exit 0
else
        echo "Command failed"
        exit 1
fi
```

In above example, we are using `cat` command to read content from file, if it is success then we are checking with return code. 
If code returned as 0 that means we have successfully executed previous command and then we are using exit command by setting exit status as 0 , similarly if it is failed to read file then we are exiting with code as 1. 

as file with name `prasad.txt` already present in directory, it will return output as 

`Command success`

## Functions 
Functions is most importent topic in any programming language, this help to avoid code duplication ana make program well strucured, easy to read, easy to understand. 

Below is syntax to create function in shell script

```shell
function Function_name() {
# do something
}
```
We can also write functions without specifying function before it's name as below

```shell
function_name(){
#do something
}
```
and simply use function name in your script file to call function 
let's take an example 

```shell
function add(){
        n1=1
        n2=2
        addition=$((n1+n2)) # add two numbers
        echo "Addition of two number is : ${addition} "
}

add  #<---function calling
```

this will return output as below

`Addition of two number is : 3` 

>NOTE: To perform arithmetic in Bash, you need to use either $(()) or the expr command. 

## Positional Parameters
We can pass parameters to our shell script and to our functions and those can be accessible using `$` symbol example $1, $2 etc 
Let's contunue to our above example of addition. 

```shell
function add(){
        n1=$1
        n2=$2
        addition=$((n1+n2)) # add two numbers
        echo "Addition of two number is : ${addition} "
}

add 10 20 #<---function calling
```
This will return output as below
`Addition of two number is : 30`

We also can pass parameters to our shell script directly. lets modify our script and then pass parameters while running script

```shell
function add(){
        n1=$1
        n2=$2
        addition=$((n1+n2)) # add two numbers
        echo "Addition of two number is : ${addition} "
}

add $1 $2 #<---function calling
```
Now, we are going to pass parameters while running script, use below command to run script

![Function parameter](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/2.%20trying%20to%20understand%20shell%20scripts/function-parameter.png?raw=true)

This is how we can pass parameter to script and to the function. 

