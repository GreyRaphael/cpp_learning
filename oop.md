# C++ OOP

- [C++ OOP](#c-oop)
  - [oop introduction](#oop-introduction)
  - [file operation](#file-operation)
  - [header file](#header-file)
  - [deep copy vs copy](#deep-copy-vs-copy)
  - [NULL, nullptr, void*](#null-nullptr-void)

## oop introduction

struct vs class
> struct默认成员权限是public; class默认成员权限是private

## file operation

exe, dll等是pe文件

```cpp
#include <iostream>
#include <fstream>
using namespace std;

int main()
{
    char a[]="hello";

    fstream file1("test1.txt", ios::out);
    //if (file1.fail()){
    if (!file1){  // 另一种判断方式
        cout<<"open file1 failed"<<endl;
    }

    fstream file2;
    file2.open("test2.txt"); // 无文件存在，不会创建文件，报错
    if (file2.fail()){
        cout<<"open file2 failed"<<endl;
    }

    fstream file3;
    file3.open("test3.txt", ios::app); // 会创建文件
    if (file3.fail()){
        cout<<"open file3 failed"<<endl;
    }

    for(int i=0;i<5 ;i++){
        file1<<"this line is "<<a[i]<<endl;
        file2<<"this line is "<<a[i]<<endl;
        file3<<"this line is "<<a[i]<<endl;
    }

    file1.close();
    file2.close();
    file3.close();
}
```

example: copy binary file

```cpp
#include <iostream>
#include <fstream>
using namespace std;

static const int BUFFER_LEN=2048;
bool CopyBinaryFile(const string& src, const string& dst){
    fstream file1(src, ios::in|ios::binary);
    fstream file2(dst, ios::out|ios::binary|ios::trunc); //trunc, 如果文件存在，清楚文件内容

    if(!file1||!file2){
        return false;
    }

    char tmp[BUFFER_LEN];
    while(!file1.eof()){
        file1.read(tmp, BUFFER_LEN);
        auto count=file1.gcount();
        file2.write(tmp, count);
    }
    file1.close();
    file2.close();
    return true;
}

int main()
{
    auto flag=CopyBinaryFile("Rubia.flac", "Zhoushen-Rubia.flac");
    cout<<flag<<endl;
}
```

## header file

为了避免同一个`.h`文件被include多次，采用两种方式

method1: 使用宏来防止同一个文件被include多次
- 优点：可移植性好
- 缺点：无法防止宏名重复，难以排错

> Once the header is included, it checks if a unique value (in this case `_SOMEFILE_H_`) is defined. Then if it's not defined, it defines it and continues to the rest of the page.When the code is included again, the first `ifndef` fails, resulting in a blank file.That prevents double declaration of any identifiers such as types, enums and static variables.

```cpp
#ifndef _SOMEFILE_H_
#define _SOMEFILE_H_
//...
#endif
```

method2: `#pragma once`，使用编译器来防止同一个文件被include多次
- 优点: 可以防止宏名重复，易排错
- 缺点：可移植性不好

## deep copy vs copy

浅拷贝vs深拷贝
- 浅拷贝：只拷贝指针地址，CPP默认**拷贝构造函数**和**赋值运算符重载**都是浅拷贝；节省空间，但容易引发多次释放
- 深拷贝：重新分配堆内存，拷贝指针指向堆内存；浪费空间，但是不会导致多次释放

兼顾二者优点的方案
1. 引用计数
2. C++新标准的移动语义(move)

## NULL, nullptr, void*

in C: `#define NULL ((void*)0)`

in C++

```cpp
#ifndef NULL
    #ifdef __cplusplus
        #define NULL 0
    #else
        #define NULL ((void*)0)
    #endif
#endif
```

in C++11
- nullptr: (void*)0
- NULL: 0

example

```cpp
#include <iostream>
using namespace std;

void func(void* i)
{
	cout << "func(void* i)" << endl;
}
void func(int i)
{
	cout << "func(int i)" << endl;
}

int main()
{
	int* pi = NULL;
	int* pi2 = nullptr;
	char* pc = NULL;
	char* pc2 = nullptr;
	func(NULL);                   // func(int i)
	func(nullptr);                 // func(void* i)
	func(pi);                         // func(void* i)
	func(pi2);                       // func(void* i)
	func(pc);                        // func(void* i)
	func(pc2);                      // func(void* i)

    return 0;
}
```