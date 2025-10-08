---
layout: post
title: Trying to understand Pointers in c++
date: 2025-10-07 15:09:00
description: Trying to understand Pointers, how it works, what operations we can perform etc. 
tags: code, debug, cpp
categories: cpp
featured: false
---

In this blog post, we are trying to understand pointers. 

Many people find  this topic very confusing but actually, it is not that much complex or confusing. Let's deep dive into pointers. 

Pointers are variable that stores memory address of another variable that's it. Do not overthink about pointers. 

#### Declare pointer
`*` (asterisk) is used to declare pointer variable. We can use data type with * to declare pointer.

Data type is used only because compiler dont know what type of variable's address it has to store or we can say, this tell compiler that what kind of value the pointer is pointing to, and how much memory to read. 
Example: 

```c++
void* p1 = 0 ; // void pointer
void* p = NULL; // it is also mean to initilize as 0. 
void* p3 = nullptr  // this is also same, it is called null pointer
int* p4;  // pointer to an integer
float* p5; //pointer to  float
double * p6 ; pointer to double 
```

#### Why it is important to add data type ? 
- **Memory interpretation** : Data type tells compiler how many byte to read from the memory address . example: int* reads 4 bytes of memory
- **Pointer Arithmatic** : When increment value of pointer , then compiler use data type to calculate example: if we increment int* variable, and int take 4 bytes,  so next location will be 4 bytes forward to it. (We will look into this later in this post)
- **Type Safety** : it helps to prevent bugs, we cannot accidentially treat float* as int* unless we type cast it.

#### Create first pointer

Let's create a code with pointers. Open your favorite code editor, I am using visual studio here for demo. 

```c++
#include<iostream>
using namespace std;

int main()
{
	int i = 4;
	int* ptr = &i; 

	cin.get(); 
}
```

This is very simple code, we just have declared int variable and initilized it with some value. 

Then we have created another pointer variable which is pointing to int variable. We have used opertor `*` to declare a pointer variable. 
We use operator `&` to denote that it holds address of integer `i` . 

I put break point in source code and then debug this. 

![bebug](https://raw.githubusercontent.com/prasadrgavande/prasadgavande.github.io/refs/heads/master/assets/img/5%20-%20trying-to-understand-pointers/firstExample.png)

Then run this program. As we hit breakpoint then watch value to variable. 
![watch](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/5%20-%20trying-to-understand-pointers/firstExample-2.png?raw=true)

Now, in visual studio, go to Debug -> Windows -> Memory -> Memory 1 , it will open new window where we can see the computer memory. 
Copy value of `ptr` variable there and hit enter
![check value](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/5%20-%20trying-to-understand-pointers/firstExample-3.png?raw=true)

As we can see the value is assigned to 4 and 4 bytes if memory allocated as it is int type.

