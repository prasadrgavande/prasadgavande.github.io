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

> NOTE
> `*` meaning value at variable
> `&` meaning address of variable 

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

Then run this program. As we hit breakpoint then watch value to variable ptr , it is showing some address in hex format. 
![watch](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/5%20-%20trying-to-understand-pointers/firstExample-2.png?raw=true)

Now, in visual studio, go to Debug -> Windows -> Memory -> Memory 1 , it will open new window where we can see the computer memory. 
Copy value of `ptr` variable there and hit enter
![check value](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/5%20-%20trying-to-understand-pointers/firstExample-3.png?raw=true)

As we can see, when we go that address in memory , it shows `04 00 00 00` which means value is 4 which we have set to int `i` and 4 byes of address assigned to this memory 

Let's modify this code to print some output to understand more

```c++
#include<iostream>
using namespace std;

int main()
{
	int i = 4;
	int* ptr = &i; 
	cout << "value of i : " << i << endl;
	cout << "address of i : " << &i << endl;
	cout << "value at " << &i << ": " << *ptr << endl; 
	cout << "Size of i: " << sizeof(i) << endl;
	cout << "Size of ptr : " << sizeof(ptr) << endl; 

	cin.get(); 

}
```

as mentioned earlier, 
- use `&` operator to know the adderss of variable in memory
- use `*` operator to get the value of that memory location , this is also called as **dereferencing**

Size of int is 4 bytes and size of pointer is 8 bytes always regardless of data type which we have assigned to that pointer. Because ultimatly it's a memory location

Run this program and check output
![output](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/5%20-%20trying-to-understand-pointers/firstExample-Output.png?raw=true)

So now, we have basic understanding of what is pointers and how it can be used, let's deep dive into pointers. 

### Why do we need pointers ?
Why we need pointers is frequently asked question. Is cpp programing can be done without pointers? and answer is yes. 

But pointers give us benefit of direct memory access. Take a simple example of incrememnt the value of variable. 

#### Pass by Value

```c++
#include<iostream>
using namespace std;

int incrementValue(int num)
{
	num++; 
	return num; 
}
int main()
{
	int i = 4;
	cout << "value of i before increment :" << i << endl; 
	cout << "get incremented value: " << incrementValue(i) << endl; 
	cout << "value of i after increment :" << i << endl;

	cin.get(); 
}
```

Debug this code and try to understand whats happened behind the scene 

When i has been initilized then check memory , enter `&i` in address then hit enter. 
![PassByValue](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/5%20-%20trying-to-understand-pointers/passByVal-1.png?raw=true)

as we can see value 04 is assigned to this memory location.

then hit F10 to go to next step and go inside the incrementValue function where we can check address of num 
![address of num](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/5%20-%20trying-to-understand-pointers/passByVal-AddressOfNum.png?raw=true)

as we can see value of num incremented by one which is showing 05 now.

and then go to next step and get back to our main function and check value of i at address `&i`

![value of I](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/5%20-%20trying-to-understand-pointers/passByVal-AddressofI.png?raw=true)

As you see value of at address of i is still 04. 

Finally, check the output.
![output](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/5%20-%20trying-to-understand-pointers/PassByValueOutput.png?raw=true)

Value of variable i is still 04 because we have passed value not actual memory location to modify, so in this case, program has created new copy of variable. 

It has not actully modify value at that memory location, it has modified the value of it's copy. 


#### Pass by Reference
Now modify the code and this time we are going to pass the value by reference meaning we will pass the address of variable instead of variable directly. 

```c++
#include<iostream>
using namespace std;

void incrementValue(int *num)
{
	(*num)++;		
}
int main()
{
	int i = 4;
	cout << "value of i before increment :" << i << endl; 
	incrementValue(&i); 
	cout << "value of i after increment :" << i << endl;

	cin.get(); 

}
```
As you see, we have passed `&i` to increamentValue function, that means we are now passing address of the variable. 
When it will go to increamentValue function, then we will dereference this first because we want to increment the value (not memory location) and then we will increment the value. 

Let's debug this code to understand what's happening behind the scene. 

![PassByRef](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/5%20-%20trying-to-understand-pointers/passByRef-1.png?raw=true)

Debug the code, go to memory and enter `&i` to see the value of that addres.

![PassByRef](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/5%20-%20trying-to-understand-pointers/passByRef-2.png?raw=true)
As we can see value 04 is assigned to this memory location, now go to next step and check out the value at adress of i . 

![PassByRef](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/5%20-%20trying-to-understand-pointers/passByRef-3.png?raw=true)

Now, value at address of i has been increased by 1 which is 05 now. 

Finally, check the output
![PassByRef-output](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/5%20-%20trying-to-understand-pointers/passByRef-4.png?raw=true)

Value of variable `i` has been changed and this time, we have modify the value at the address directly. 

This is main advantages of using pointers in cpp .

Hope you have basic understanding of pointers and how it works in cpp. 

Happy Coding. 







