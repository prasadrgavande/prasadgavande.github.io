---
layout: post
title: Boilerplate project to debug and run CPP code in VS Code
date: 2025-05-05 15:09:00
description: Boilerplate project to debug and run CPP code in VS Code
tags: code, debug, cpp
categories: cpp
featured: false
---

I found it little difficult to run CPP project in vs code always, we need to do lot of configuration, setting to debug the code.
In this post, you can find boilerplate to run cpp code in vs code.

**Prerequisite**
- Make sure that CPP compiler already installed in your machine, I will use gcc but you can use any other compiler of your choice
- VS Code should be installed in your machine.
- (optional) Install plugin "C/C++ Extension Pack" , this will install C/C++ extension and CMAKE (We don't need CMAKE for now, I will write seperate tutorial to build project using CMAKE)

#### Create new folder for your CPP Project
I am going to follow below folder structure for my project and will follow same in rest of tutorial to write configuration files

```
Boilerplate
│   main.cpp             # CPP file contains main function
│
└───build                # Build file (Executable / Binary )
│   │   main.exe
└───include              # All header files in project
|    │   student.h
|    │   Employee.h
└───src                  # All source files in project
|   │   student.cpp
|   │   Employee.cpp
└───lib                  # Third party libraries
|   │   spdlog
|    └───include
│       │   spdlog
│   c_cpp_properties.json 
│   launch.json           
│   tasks.json            
│   settings.json         
```

### Let's see use of each JSON file mentioned above

#### c_cpp_properties.json - IntelliSense and Compiler Configuration
This is configuration file in vs code for intellisense, code suggestions and syntax highlighting for c/ c++ code.
It defines include paths, compiler settings, and standard versions.

```json
{
    "configurations": [
        {
            "name": "Win32",
            "includePath": [
                "${workspaceFolder}/**",
                "${workspaceFolder}/include",         // Add your header directory
                "${workspaceFolder}/libs/**"  // Add third party library directory
            ],
            "defines": [],
            "compilerPath": "D:\\Installation\\mingw\\mingw64\\bin\\g++.exe",   // Add path of your compiler
            "cppStandard": "c++20",
            "intelliSenseMode": "windows-gcc-x64"
        }
    ],
    "version": 4
}
```

#### tasks.json - Build Automation
This file defines how VS Code build and compiler your code and helps to automate compilation project.
We can use Ctrl + Shift + B to build cpp code.
This file contains path of header files, source files, third party libraries (if any) & output file.

```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "type": "shell",
            "label": "Build Main",
            "command": "D:\\Installation\\mingw\\mingw64\\bin\\g++.exe",  //add path of your compiler
            "args": [
                "-fdiagnostics-color=always",
                "-g",
                "main.cpp",   //by default run main file
                "${workspaceFolder}/src/*.cpp",   //additional files
                 "-I${workspaceFolder}/include",   // Include directory flag
                 "-I${workspaceFolder}/libs/spdlog/include",   // Include third party library
                "-o",
                "${workspaceFolder}/build/main.exe"
            ],
            "options": {
                "cwd": "${workspaceFolder}"
            },
            "problemMatcher": ["$gcc"],
            "group": "build",
            "detail": "Building main.exe in workspace root"
        }
    ]
}

```
> IMPORTANT
> if you create `tasks.json` using vs code function, then it will have default type as `cppbuild`, in this case you need to add path of all source files one by one.
>
> To make it more generic, we have added path of source file as `"${workspaceFolder}/src/*.cpp"`  this does not work with `cppbuild` so changed the type to `shell`
>
> Now, whenever we add new source file to project, we dont need to modify `tasks.json` file.

> NOTE
>In args, we have passed `-g`, `main.cpp` to debug file. if you don't want to pass `main.cpp` , then we can also mention `"$(file)"` to debug current file.


#### launch.json - Debugger Setup
This file conatins configuration for debugging environment, this defines executable, debugger and how to launch a program.
Without this file, vs code will not know how to debug program.

Once we add this file, we can see debug option in dropdown in vs code with name `C++ Debug` (you can give any name) in below configuation

![debug cpp](https://raw.githubusercontent.com/prasadrgavande/prasadgavande.github.io/refs/heads/master/assets/img/debug_cpp.png)

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "C++ Debug",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/build/main.exe",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "miDebuggerPath": "D:\\Installation\\mingw\\mingw64\\bin\\gdb.exe",
            "preLaunchTask": "Build Main",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ]
        }
    ]
}

```
>NOTE
>We have added `"preLaunchTask": "Build Main"`, this name should be exactly same as which we have defined in `tasks.json`
>
>`launch.json` will make sure that configuration which we have defined in `tasks.json` has been loaded completely.

#### settings.json - Workspace and Editor settings
This file defines workspace specific settings including formatting, linting and compiler warnings


```json
{
    "files.associations": {
        "iostream": "cpp",
        "ostream": "cpp"
    },
   "editor.formatOnSave": true
}
```
>TIP
> This file also can be used to customize editor behaviours i.e. tabs, spaces, auto-save etc.

That's all, now let's write some simple code.

>**main.cpp**

```c++
#include <iostream>
#include <stdio.h>
#include "school.h"

using namespace std;

int main(int argc, char const *argv[])
{
  school::student s;
  s.display(); // Call the display function from the student class
  return 0;
}

```

As mentioned above, all our header files will be inside "include" folder. so create one for `school.h`

>**school.h**

```c++
#include<iostream>
using namespace std;

namespace school{
    class student{
        public:
            void display();
    };
}
```

then create it's source file inside "src" folder, create one source file called `school.cpp`

>**school.cpp**

```c++
#include <iostream>
#include "../include/school.h"
using namespace std;
namespace school
{
    void student::display()
    {
        cout << "Hello from student class!" << endl;
    }
}
```

Our folder structure will be something as below.

![folder structure](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/cpp_boilerplate.png?raw=true)

Now, press F5 or go to Debug -> select "Debug C++" from dropdown and click on debug icon.
Project will compile and generate executable in `build` folder.
![cpp output](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/cpp-boilerplate-output.png?raw=true)


Happy Coding !
