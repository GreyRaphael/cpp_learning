# C++ OOP

- [C++ OOP](#c-oop)
  - [oop introduction](#oop-introduction)
  - [file operation](#file-operation)
  - [header file](#header-file)

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