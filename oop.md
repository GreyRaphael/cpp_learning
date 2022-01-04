# C++ OOP

- [C++ OOP](#c-oop)
  - [oop introduction](#oop-introduction)
  - [file operation](#file-operation)

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