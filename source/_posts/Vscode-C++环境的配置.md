---
title: Windows和Linux(Ubuntu)下 Vscode C++环境的配置
tags: 
  - VScoode 
  - C++ 
  - windows 
  - Linux Ubuntu
categories: 
  - 教程
abbrlink: '45745e46'
date: 2023-02-25 23:05:30
---

Vscode是一款相当强大的编程软件，其万能的通用插件系统可以让程序猿非常方（kun）便（nan）的完成工作。

为什么要加个kunnan？

因为方便的前提是经过安装的时候配置的痛苦（~~md绝了~~）

本片文章将详细的讲述Vscode之于windows和linux系统的C++环境的配置与调试，（~~以方便博主自己忘了的时候查阅~~）
<!--more-->

# 第一部分 Windows系统配置
## 1.1 MinGW展开部署

Vscode与其说是个编程软件，不如说是一个编程平台：它自己不会携带任何的语言编译软件，这些东西需要自己安装。如果说，你之前有安装过DEV-CPP或者其他的编程软件，那你的电脑上已经有了MinGW，只需要自己找出来安装地址在哪里。

在cmd中输入
```
gcc -v
```
会弹出其路径，寻找即可。

如果没有，那就继续执行下列安装步骤。

[SourceForge网站](https://sourceforge.net/projects/mingw-w64/)可以下载到全套的MinGW，但是出于外网的原因这个东西下载起来很慢，所以这里我附上了MinGW的压缩文件，下载后直接解压到C盘就可以了

[MinGW下载](https://pan.baidu.com/s/1JjwyTxVQjV2gtmrPwjFhxQ?pwd=qtsg)

提取码：qtsg

[七牛云下载](http://rqzta2283.sabkt.gdipper.com/MinGW.zip)

接下来，要在Windows里面配置环境变量，让电脑知道自己集成了C语言环境（~~毕竟它不是550W~~）

打开设置--->系统--->系统信息（win11在最底下）--->高级系统设置--->环境变量--->系统变量Path，在里面加上一句话

```
C:\MinGW\x86_64-8.1.0-release-win32-sjlj-rt_v6-rev0\bin
```
如果是直接下载的附件里面的编译器就是这句话，如果是从官网安装的就要注意自己寻找路径，路径格式和上述代码基本一致，这个bin文件夹里面是一套完整的编译器.exe

<img src="1-1.png" alt="目标文件夹内容" style="zoom:80%;" />

## 1.2 VsCode编译运行环境配置

像前文所说，Vscode只是一个平台，自身不具有任何向DEV-CPP之类集成的编译运行配置，该环境需要自己搭建。

### 1.2.1 C++插件下载

在Vscode侧边栏插件部分搜索C++，下载C/C++插件

<img src="1-2-1.png" alt="C/C++插件" style="zoom:80%;" />

### 1.2.2 编译器路径设置

新建一个文件夹，在其中再新建一个名称为“.vscode”的空文件夹，在Vscode中选择打开文件架--->你所创立的文件夹

这个将是你以后在Vscode编写C++代码时唯一可以存放并调试C++代码的文件夹，一旦离开这个文件夹区域Vscode将无法正常提供编译调试运行服务。

在.vscode中点击“新建文件”快捷键，分别命名四个文件：

<img src="1-2-2.png" alt="新建文件和新建文件夹快捷键" style="zoom:80%;" />

*** 1.c_cpp_properties.json ***
*** 2.launch.json ***
*** 3.settings.json ***
*** 4.tasks.json ***



<img src="1-2-3.png" alt="在.vscode新建的四个文件" style="zoom:80%;" />

这四个分别是C编译器配置、GDB调试配置、设置文件、编译生成文件设置

分别将下列四段代码拷贝到上述四个文件里
***注意：如果是自己下载的MinGW，需要自己寻找目标文件夹或exe路径，路径格式和代码中完全一致***

```
{
    //c_cpp_properties.json
    "configurations": [
        {
            "name": "Win32",
            "includePath": [
                "${workspaceFolder}/**",
                "C:/MinGW/x86_64-8.1.0-release-win32-sjlj-rt_v6-rev0/lib/gcc/x86_64-w64-mingw32/8.1.0/include/c++/x86_64-w64-mingw32"//如果是别的地址要自己修改,下同。
            ],
            "defines": [
                "_DEBUG",
                "UNICODE",
                "_UNICODE"
            ],
            "compilerPath": "C:\\MinGW\\x86_64-8.1.0-release-win32-sjlj-rt_v6-rev0\\bin\\gcc.exe",
            "cStandard": "gnu17",
            "cppStandard": "gnu++14",
            "intelliSenseMode": "windows-gcc-x64"
        }
    ],
    "version": 4
}
```

```
{
    //launch.json
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [

        {
            "name": "(gdb) Launch",
            "preLaunchTask": "g++.exe build active file",//调试前执行的任务，就是之前配置的tasks.json中的label字段
            "type": "cppdbg",//配置类型，只能为cppdbg
            "request": "launch",//请求配置类型，可以为launch（启动）或attach（附加）
            "program": "${fileDirname}\\${fileBasenameNoExtension}.exe",//调试程序的路径名称
            "args": [
            "/C",
            "${fileDirname}\\${fileBasenameNoExtension}.exe",
            "&",
            "echo.",
            "&",
            "pause"
        ],
//调试传递参数
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": true,//true显示外置的控制台窗口，false显示内置终端
            "MIMode": "gdb",
            "miDebuggerPath": "C:/MinGW/x86_64-8.1.0-release-win32-sjlj-rt_v6-rev0/bin/gdb.exe",
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

```
{
    settings.json
    "files.associations": {
        "ostream": "cpp",
        "iostream": "cpp",
        "iomanip": "cpp",
        "array": "cpp",
        "atomic": "cpp",
        "*.tcc": "cpp",
        "bitset": "cpp",
        "cctype": "cpp",
        "cfenv": "cpp",
        "charconv": "cpp",
        "chrono": "cpp",
        "cinttypes": "cpp",
        "clocale": "cpp",
        "cmath": "cpp",
        "codecvt": "cpp",
        "complex": "cpp",
        "condition_variable": "cpp",
        "csetjmp": "cpp",
        "csignal": "cpp",
        "cstdarg": "cpp",
        "cstddef": "cpp",
        "cstdint": "cpp",
        "cstdio": "cpp",
        "cstdlib": "cpp",
        "cstring": "cpp",
        "ctime": "cpp",
        "cuchar": "cpp",
        "cwchar": "cpp",
        "cwctype": "cpp",
        "deque": "cpp",
        "forward_list": "cpp",
        "list": "cpp",
        "unordered_map": "cpp",
        "unordered_set": "cpp",
        "vector": "cpp",
        "exception": "cpp",
        "algorithm": "cpp",
        "functional": "cpp",
        "iterator": "cpp",
        "map": "cpp",
        "memory": "cpp",
        "memory_resource": "cpp",
        "numeric": "cpp",
        "optional": "cpp",
        "random": "cpp",
        "ratio": "cpp",
        "regex": "cpp",
        "set": "cpp",
        "string": "cpp",
        "string_view": "cpp",
        "system_error": "cpp",
        "tuple": "cpp",
        "type_traits": "cpp",
        "utility": "cpp",
        "fstream": "cpp",
        "future": "cpp",
        "initializer_list": "cpp",
        "iosfwd": "cpp",
        "istream": "cpp",
        "limits": "cpp",
        "mutex": "cpp",
        "new": "cpp",
        "scoped_allocator": "cpp",
        "shared_mutex": "cpp",
        "sstream": "cpp",
        "stdexcept": "cpp",
        "streambuf": "cpp",
        "thread": "cpp",
        "typeindex": "cpp",
        "typeinfo": "cpp",
        "valarray": "cpp",
        "cassert": "cpp",
        "ccomplex": "cpp",
        "cerrno": "cpp",
        "cfloat": "cpp",
        "ciso646": "cpp",
        "climits": "cpp",
        "cstdalign": "cpp",
        "cstdbool": "cpp",
        "ctgmath": "cpp",
        "filesystem": "cpp",
        "ios": "cpp",
        "locale": "cpp",
        "queue": "cpp",
        "stack": "cpp"
    },
    "C_Cpp.errorSquiggles": "enabled"
}
```

```
{
    //tasks.json
    // See https://go.microsoft.com/fwlink/?LinkId=733558 
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
            "type": "shell",
            "label": "g++.exe build active file",//任务的名字，就是刚才在命令面板中选择的时候所看到的，可以自己设置
            "command": "C:/MinGW/x86_64-8.1.0-release-win32-sjlj-rt_v6-rev0/bin/g++.exe",
            "args": [//编译时候的参数
                "-g",//添加gdb调试选项
                "${file}",
                "-o",//指定生成可执行文件的名称
                "${fileDirname}\\${fileBasenameNoExtension}.exe"
            ],
            "options": {
                "cwd": "C:/MinGW/x86_64-8.1.0-release-win32-sjlj-rt_v6-rev0/bin"
            },
            "problemMatcher": [
                "$gcc"
            ],
            "group": {
                "kind": "build",
                "isDefault": true//表示快捷键Ctrl+Shift+B可以运行该任务
            }
        }
    ]
}
```

保存退出后，在新建文件夹下（注意不要建到.vscode里面，如果嫌麻烦可以右键.vscode选择属性：隐藏）新建cpp文件，helloworld验证，Fn+F5调试运行。如果能成功运行说明配置成功。

# 第二部分 Linux(Ubuntu)环境配置

对于Ubuntu系统来说，相关的配置大体上历程是一样的。

## 2.1MinGW展开部署（Ubuntu，CentOS略有不同）

这波相较于windows系统下会方便很多，直接通过命令行下载。

如果你的WSL之前没有操作过apt-get,要多执行一步apt-get upgrade.

```
sudo apt-get update
```
```
sudo apt-get upgrade
```
```
sudo apt-get install gcc

sudo apt-get install g++

sudo apt-get install gdb

```

安装完成后直接通过
```
gcc -v

gdb -v

g++ -v
```

验证是否完成了安装，如果完成了会提示gcc、gdb、g++的位置以及版本信息。

CentOS需要别的方法安装（或者等卑微的作者更新）

## 2.2 Vscode配置安装

启动Vscode，启动远程连接到WSL（左下角绿方框-->New WSL Window）

在新窗口里，安装C/C++插件（在Ubuntu18.04 LTS安装）。

安装完成后，快捷键Ctrl+`(即Esc下面哪一个)调出终端，输入命令：
```
mkdir program
```
新建一个program文件夹。
继续命令：
```
cd program
mkdir .vscode
```
进入program文件夹并创建".vscode"文件夹。

继续命令：
```
cd .vscode
touch launch.json c_cpp_properties.json tasks.json
```
创建完毕三个文件。

在vscode打开文件夹-->program，点击确定（不要点.vscode,提示框里的路径应为/home/*****（你的linux用户名）/program）

如果弹出是否选择信任此文件夹，选择信任。

在里面点击.vscode，分别打开三个文件，拷贝以下内容。
***launch.json***
```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        
        {
            "name": "(gdb) Launch",
            "preLaunchTask": "g++ build active file",//调试前执行的任务，就是之前配置的tasks.json中的label字段
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/${fileBasenameNoExtension}.out",
            "args": [
                "2019",
                "WSL",
                "G++",
                "programming"
            ],
            "stopAtEntry": true,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
        }
    ]
}
```

***c_cpp_properties.json***
```
{
    "configurations": [
        {
            "name": "Linux",
            "includePath": [
                "${workspaceFolder}/**"
            ],
            "defines": ["_DEBUG",
            "UNICODE",
            "_UNICODE"],
            "compilerPath": "/usr/bin/g++",
            "cStandard": "c11",
            "cppStandard": "c++17",
            "intelliSenseMode": "gcc-x64"
        }
    ],
    "version": 4
}
```

***tasks.json***
```
{
    "version": "2.0.0",
    "tasks": [
        {
            "type": "shell",
            "label": "g++ build active file",
            "command": "/usr/bin/g++",
            "args": [
                "-Og",
                "-g",
                "${file}",
                "-o",
                "${fileDirname}/${fileBasenameNoExtension}.out"
            ],
            "options": {
                "cwd": "/usr/bin"
            },
            "problemMatcher": [
                "$gcc"
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            }
        },
    ]
}
```
点击文件框下半部分空白区域（此时会有一个蓝色框将整个program文件夹框起来，表示在program文件夹下）

<img src="2-2-1.png" alt="蓝色框" style="zoom:80%;" />

点击“新建文件”自己创建一个helloworld（注意：要在program文件夹下，不要新建到.vscode里面），如果可以Fn+F5调试，Linux下VScode就可以正常运行了。

***注意，如果你的C++扩展没有安装好，你的launch.json和tasks.json会有黄色条目警告你找不到相关的配置与编译器***

至此，C++在windows和Ubuntu下全部部署完毕。
















