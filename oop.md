# C++ OOP

- [C++ OOP](#c-oop)
  - [oop introduction](#oop-introduction)
  - [file operation](#file-operation)
  - [header file](#header-file)
  - [deep copy vs copy](#deep-copy-vs-copy)
  - [NULL, nullptr, void*](#null-nullptr-void)
  - [change type](#change-type)
  - [Adapter](#adapter)

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

## change type

`const_cast`: 用于转换指针或引用，去掉类型的const

```cpp
int main()
{
	// C++ const_cast
	const int a = 10;
	//int* pA = &a; //error
	int* pA = const_cast<int*>(&a);
	*pA = 100;
}
```

`reinterpret_cast`: 很危险;重新解释类型，既不检查指向的内容，也不检查指针类型本身；但要求转换前后的类型所占的用内存的大小一致，否则引发编译器错误。

```cpp
#include <iostream>
using namespace std;

int Test(){
    return 10;
}

int main()
{
    //C++ reinterpret_cast
    typedef int(*FuncPtr)() ;
    FuncPtr p1=&Test;
    cout<<p1()<<endl; // 10

    typedef void(*FPtr)() ;
    FPtr p2;
    p2=reinterpret_cast<FPtr>(&Test);
    p2();// p2没有输出了
}
```

`static_cast`: 用于基本类型转换；有继承关系类对象和类指针之间转换；由程序员来确保转换是安全的；不会产生动态转换的类型安全检查的开销；

```cpp
int main()
{
    //C++ static_cast
    int a=5;
    double b=static_cast<int>(a);
    cout<<a<<' '<<b<<endl;

    double x=6.6;
    int y=static_cast<int>(x);
    cout<<x<<' '<<y<<endl; // 6.6 6
}
```

`dynamic_cast`: 只能用于含有虚函数的类，必须用在多态体系中，用于类层次间的向上和向下转化；向下转化时(父类转换为子类)，如果是非法的，对于指针返回NULL;
> 类对象的转换建议使用`dynamic_cast`, 不使用`static_cast`

```cpp
#include <iostream>
using namespace std;

class Base{
public:
    Base():_i(0){;}
    // 父类虚函数
    virtual void T(){cout<<"Base:T "<<_i<<endl;}
private:
    int _i;
};

class Derive:public Base{
public:
    Derive():_j(1){;}
    // 子类虚函数
    virtual void T(){cout<<"Derive:T "<<_j<<endl;}

private:
    int _j;
};

int main()
{
    //C++ static_cast vs dynamic_cast
    Base b1;
    Derive d1;
    Base* pb;
    Derive* pd;

    // 子类→父类
    pb=static_cast<Base*>(&d1);//编译器不检查
    if(pb==NULL){
        cout<<"unsafe static_cast from child to father"<<endl;
    }

    pb=dynamic_cast<Base*>(&d1);//因为有虚函数，就会检查
    if(pb==NULL){
        cout<<"unsafe dynamic_cast from child to father"<<endl;
    }

    // 父类→子类
    pd=static_cast<Derive*>(&b1);//不检查，后果自负
    if (pd==NULL){
        cout<<"unsafe static_cast from father to child"<<endl;
    }

    pd=dynamic_cast<Derive*>(&b1);
    if (pd==NULL){
        cout<<"unsafe dynamic_cast from father to child"<<endl;
    }

}
```

## Adapter

适配器模式

```cpp
#include <string>
#include<iostream>
using namespace std;

class LegacyRectangle
{
public:
	LegacyRectangle(double x1, double y1, double x2, double y2)
	{
		_x1 = x1;
		_y1 = y1;
		_x2 = x2;
		_y2 = y2;
	}

	void LegacyDraw()
	{
		cout << "LegacyRectangle:: LegacyDraw()" << _x1 << " " << _y1 << " " << _x2 << " " << _y2 << endl;
	}

private:
	double _x1;
	double _y1;
	double _x2;
	double _y2;
};

class Rectangle
{
public:
	virtual void Draw(string str) = 0;
};

// 第一种适配的方式：使用多重继承
class RectangleAdapter: public Rectangle, public LegacyRectangle
{
public:
	RectangleAdapter(double x, double y, double w, double h) :
		LegacyRectangle(x, y, x + w, y + h)
	{
		cout << "RectangleAdapter(int x, int y, int w, int h)" << endl;
	}

	virtual void Draw(string str)
	{
		cout << "RectangleAdapter::Draw()" << endl;
		LegacyDraw();
	}
};

// 第二种适配方式：组合方式的Adapter
class RectangleAdapter2 :public Rectangle
{
public:
	RectangleAdapter2(double x, double y, double w, double h) :
		_lRect(x, y, x + w, y + h)
	{
		cout << "RectangleAdapter2(int x, int y, int w, int h)" << endl;
	}

	virtual void Draw(string str)
	{
		cout << "RectangleAdapter2::Draw()" << endl;
		_lRect.LegacyDraw();
	}
private:
	LegacyRectangle _lRect;
};

int main()
{
	double x = 20.0, y = 50.0, w = 300.0, h = 200.0;
	RectangleAdapter ra(x, y, w, h);
	Rectangle* pR = &ra;
	pR->Draw("Testing Adapter");

	cout << endl;
	RectangleAdapter2 ra2(x, y, w, h);
	Rectangle* pR2 = &ra2;
	pR2->Draw("Testing2 Adapter");

    return 0;
}
```