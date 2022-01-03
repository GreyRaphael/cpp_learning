# C++ Trick

- [C++ Trick](#c-trick)
  - [Encoding](#encoding)
  - [string](#string)
  - [const](#const)
  - [pointer](#pointer)

## Encoding

example in Qt Creator
> Tools>Options>Text Editor>Default Encoding>gbk

```cpp
#include <iostream>
#include <string>

using namespace std;

int main()
{
    std::wcout.imbue(std::locale("chs")); // 这一句才能产生wcout输出
    std::wcout<<L"你好hello"<<endl;

    std::wstring s1=L"你好hello";
    std::wcout<<s1<<endl;
}
```

## string

```cpp
#include <iostream>
#include <string>

using namespace std;

int main()
{
    string s1="hello";
    string s2("hello");
    string s2=string("hello");
    // .length()与.size()等价
    cout<<s1<<' '<<s1.length()<<' '<<s1.size()<<' '<<s1.capacity()<<endl;
    // hello 5 5 15
    cout<<s2<<' '<<s2.length()<<' '<<s2.size()<<' '<<s2.capacity()<<endl;
    // hello 5 5 15
    cout<<(s1==s2)<<endl;
}
```

## const

const默认修饰离它近的，默认修饰左侧，左侧没有修饰右侧
- `const char *`: 修饰char, char不允许修改
- `char * const`: 修饰*, 指针不允许修改


```cpp
#include <iostream>

using namespace std;

int main()
{
    int a1[]={1,2,3,4,5};
    int a2[]={11,22,33,44,55};

    int const * p1=a1;
    const int * p2=a1; // p2和p1等价

    int * const p3=a1;

    int const * const p4=a1;
    const int * const p5=a1; //p5和p4等价
    p1=a2;
    // *p1=111; //not allowed
    cout<<*p1<<endl; //11

    //p3=a2; //not allowed
    cout<<*p3<<endl; //1
    cout<<*(p3+1)<<endl;//2
    *(p3+1)=222;
    cout<<*(p3+1)<<endl;//222

    cout<<*(p2+1)<<endl; //222, 虽然p2无法改变数组的值，但是通过其他指针，确实能够改变该数组中的值
    // 所以p2只具有read-only功能
    // 一般开发的时候，read-only功能分配给这种类型的指针；
    // 程序的write分配给另外的指针


    //p4=a2; //not allowed
    // *p4=999; //not allowed

}
```


```cpp
#include <iostream>

using namespace std;

int main()
{
    char s1[]={"hello"};
    char const * p1="helloworld";
    const char * p2="hellogrey";
    char * const p3="helloalpha";
    char const * const p4="hellobeta";
    p1=s1;
    //*p1='x';// not allowed
    p2=s1;// p2和p1等价
    //p3=s1; //not allowed
    //p4=s1; // not allowed
    cout<<*p1<<endl;
    cout<<*(p1+4)<<endl;
    cout<<*p2<<endl;
}
```

## pointer

```cpp
#include <iostream>

using namespace std;

int main()
{
    int a1[]={1,2,3};
    int a2[]={1,2,3};
    int* p1=a1;
    int* p2=a2;

    // ++优先级高于*
    //cout<<*(p1++)<<endl; //1
    cout<<*p1++<<endl; //1
    cout<<*p1<<endl; //2
    *p1++=22;
    cout<<a1[0]<<' '<<a1[1]<<' '<<a1[2]<<endl; //1 22 3
    cout<<*p1<<endl; // 3

    //cout<<*(++p2)<<endl; //2
    cout<<*++p2<<endl; //2
    cout<<*p2<<endl; //2
    *++p2=33;
    cout<<a2[0]<<' '<<a2[1]<<' '<<a2[2]<<endl; // 1 2 33
    cout<<*p2<<endl; // 33
}
```

```cpp
#include <iostream>

using namespace std;

int main()
{
    int a=1,b=2,c,d;
    c=a+++b; // 相当于a++     +b
    // d=a++++b; //相当于a++    ++b // error
    cout<<c<<endl; // 3
}
```