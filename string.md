# C++ String

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

