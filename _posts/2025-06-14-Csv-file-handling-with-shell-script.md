---
layout: post
title: Csv file handling with shell script
date: 2025-06-14 15:09:00
description: Tutorial for basics csv operations using Shell scripting
tags: code, shell
categories: scripting
featured: false
---

In this post, we are going to take a look on basic csv operations which includes read, write, find etc. 

so let's get started with this.

Make sure, you have csv file with you in folder, you can refer to [file handing post](https://prasadgavande.in/blog/2025/file-operations-with-shell-script/) to learn more about it.

Create a file using editor of your choice, I am using `vi` editor for this 

`vi csvOperations.sh`

This will create a file and open editor

###Common declaration
first declare shebang as below

`#!/bin/bash`

then create variable which will hold csv file name

`csvFile="data.csv"`

Let's create a functions for basic operations 

### Function to read CSV file
```
readCsv()
{
        echo "Content in csv file as below"
        cat $csvFile
}
```

### function count rows in file
We are going to use tail function with `wc | -l` options which can give us count of rows in file
```
countRows()
{
        echo "Number of rows in file: "
        tail data.csv | wc -l
}
```
### Extract specific column values
We can use `awk` function to get column values 

```
extractColumns(){
        echo "extracting second column"
        awk -F , '{print $2}' "$csvFile"
}
```
where:

  `-F ,` --> this will use as field seperator to comma , meaning awk will treat each comma-seperated value as different column
  
  `{print $2}` --> this will tell awk command to print second column
  
  `$csvFile` --> we are passing csv file to awk command for processing

### Append data to csv file
We are going to ask user to enter value to append data to csv

```
appendData()
{
        echo "Enter below information to add new row to csv"
        read -p "Enter Id:" id
        read -p "Enter Name:" name
        read -p "Enter Dept:" dept

        echo "$id, $name, $dept" >> "$csvFile"
        echo " data added to csv file"
}
```

### Get row by Id
We are going to ask user to enter row id which he wants to search for , We can get row by passing Id using `awk` command
```
getRowById()
{
        read -p "Enter Id of row you want to read" rowId
        awk -F , -v id="$rowId" '$1==id' "$csvFile"
}
```

Where:

`-F ,` --> this will use as field seperator to comma , meaning awk will treat each comma-seperated value as different column

`-v id="$rowId"` --> this will define variable id inside awk command and assign value of rowId which passed by user 

`'$1==id'` --> as we have stored Ids in first column in csv file, this will check if first column matched id from csv file

### Function calling
Now, its time to call functions one by one.
```
readCsv
countRows
extractColumns
appendData
echo "Read Csv again after adding new row"
readCsv
getRowById
```

That's it, lets save this file , grant required permission.
Before run this, lets take a look on our `data.csv` file

```
id,name,dept
1,abc,IT
2,pqr,HR
3,xyz,Acc
```

Now, run our executable csv opeations file
![Output](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/4-Csv-file-handling-with-shell-script/output.png?raw=true)




  




