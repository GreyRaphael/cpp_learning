# C++ String

- [C++ String](#c-string)
  - [Encoding](#encoding)
  - [string](#string)

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

const默认修饰离它近的，默认修饰左侧，左侧没有修饰右侧
- `const char *`: 修饰char, char不允许修改
- `char * const`: 修饰*, 指针不允许修改