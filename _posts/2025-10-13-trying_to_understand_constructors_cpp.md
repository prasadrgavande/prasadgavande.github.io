---
layout: post
title: Trying to understand Constructors in c++
date: 2025-10-13 15:09:00
description: Trying to understand constructors, how it works, what operations we can perform etc. 
tags: code, debug, cpp
categories: cpp
featured: false
---

In this post we are trying to understand what is construtors in c++. 

Lets create a basic example of class in c++ 

```c++
class Student
{
private:
	int myId;
	string myName;

public:		
	void printData()
	{
		cout << "Id : " << myId << " & Name: " << myName << endl; 
	}
};
```

Student class just have one method called printData which is just printing its variables. 

Now, create an object in main function and try to run program.
```c++
int main()
{
	Student s; 	
	s.printData();

	cin.get();
}
```

![output1](https://raw.githubusercontent.com/prasadrgavande/prasadgavande.github.io/refs/heads/master/assets/img/6-trying-to-understand-constructors/output1.png)

As you see, it has some random value assigned for `int id` which is not accepted value. 

That means compiler has assigned some remaining or random value to our `int` variable.

To overcome this issue, lets assigned some value to our variables.

Modify out `Student` class and add `init()` method inside this as below

```c++

class Student
{
private:
	int myId;
	string myName;

public:
	void init(int id, string name) {		
		myId = id;
		myName = name;
	}	
	void printData()
	{
		cout << "Id : " << myId << " & Name: " << myName << endl; 
	}
};
```

In our init method, we have assigned value of our member variables. 

Now, try to call `printData()` function in our `main` function. 

```c++
int main()
{
	Student s; 	
	s.init(1, "prasad");
	
	s.printData();

	cin.get();
}
```

Run program and check output.
![output2](https://raw.githubusercontent.com/prasadrgavande/prasadgavande.github.io/refs/heads/master/assets/img/6-trying-to-understand-constructors/output2.png)

this seems works well, as we can see values to variable `id` and `name` assiend correctly. 

but there is an issue. 
Everytime, when we are going to create an object of `Student` class, we need to call `init` function as below

```c++
int main()
{
	Student s1; 	
	s1.init(1, "prasad");
	s1.printData();

  Student s2; 	
	s2.init(2, "Saurabh");
	s2.printData();
  
	cin.get();
}
```

This is not the good way to handle and hence constructors come into picture. 

### What is Constructors? 

A constructors is a special member function of class that is automatically called when object of that class created. 

It's main job is to initilize the object by setting default values or allocating resources. 

### Key characteristics 
- Name of constructor function should be same as class name.
- It does not have any return type. 
- It is automatically get invoked when an object is initilized
- We can have overloaded method for constructors.

Let's continue to our example, modify our `student` class and add constructor function to it

```c++

class Student
{
private:
	int myId;
	string myName;

public:
	Student() {  // <--- this is constructor function
		myId = 1;
		myName = "Prasad Gavande";
	}
	void printData()
	{
		cout << "Id : " << myId << " & Name: " << myName << endl; 
	}
};


int main()
{
	Student s; 	 // <--- constructor function called when object is initilized 
	s.printData();

	cin.get();
}
```
As we can see, we have new public function with same name as of class, `student`

In that function we have just assiged values to our member functions.

Now, run this program to check output
![output3](https://raw.githubusercontent.com/prasadrgavande/prasadgavande.github.io/refs/heads/master/assets/img/6-trying-to-understand-constructors/output3.png)

As we can see, we can assign value without init function. 



### Types of constructors 
- Default constructor - it does not have any parameters
- Parameterized constructor - it has arguments , it is overloaded method of default constructor
- Copy constructor - it creates a copy of existing objects
- Move constructor - Transfer resources from one object to another

I found that parameterized constructor is widely used, lets extend our example with parameterized constructor

```c++

class Student
{
private:
	int myId;
	string myName;

public:	
	Student() {  // <-- default constructor
		myId = 1;
		myName = "Prasad Gavande";
	}
	Student( int id, string name) // <-- parameterized constructor
	{
		myId = id;
		myName = name; 
	}
	void printData()
	{
		cout << "Id : " << myId << " & Name: " << myName << endl; 
	}
};
```
As we can see, we have added one more overloaded method of default constructor. Which has two parameters, `id` and `name`.

We have passed parameters to this function that's why it is called parameterized constructor.

Now, lets create an object in `main` method.

```c++
int main()
{	
	Student s(1, "Sachin");  //<-- initilize object 
	s.printData();

	cin.get();
}
```
Run this program and check output
![output4](https://raw.githubusercontent.com/prasadrgavande/prasadgavande.github.io/refs/heads/master/assets/img/6-trying-to-understand-constructors/output4.png)

This is how we can create constructor , use them to initilize the value and keep our code clean. 

Hope you understand basics of constructors. 

Happy Coding. 

