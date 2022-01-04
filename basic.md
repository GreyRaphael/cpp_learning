# C++ Trick

- [C++ Trick](#c-trick)
  - [Encoding](#encoding)
  - [string](#string)
  - [const](#const)
  - [pointer](#pointer)
  - [smart pointers](#smart-pointers)
    - [auto_ptr](#auto_ptr)
    - [unique_ptr](#unique_ptr)
    - [shared_ptr](#shared_ptr)
    - [weak_ptr](#weak_ptr)
  - [reference](#reference)
  - [sentence](#sentence)
  - [if, switch](#if-switch)
  - [enumerate](#enumerate)
  - [struct,union](#structunion)
  - [loop](#loop)
  - [function](#function)
  - [recursion](#recursion)

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
    // c=a++b // error
    c=a+++b; // 相当于a++     +b
    // d=a++++b; //相当于a++    ++b // error
    cout<<c<<endl; // 3

    int arr[]={11,22,33};
    int* p1=arr;
    e=++*++p1;
    f=*++p1 + 1;
    cout<<e<<endl; // 23
    cout<<f<<endl; // 34
    cout<<arr[0]<<' '<<arr[1]<<' '<<arr[2]; // 11 23 33
}
```

## smart pointers

智能指针分析
1. 应用场景
   - 对象所有权
   - 生命周期
2. 性能分析

smart pointers
- unique_ptr: 用的少
- shared_ptr: 常用
- weak_ptr: 常用
- auto_ptr: deprecated

### auto_ptr

- 由new获得对象，auto_ptr对象销毁时，它所管理的对象自动被delete
- 所有权转移: 不小心将它转移给另外的智能指针，原来的指针就不再拥有该对象；在拷贝/赋值过程中，会直接剥夺指针对原对象对内存的控制权，转交给新对象，然后将原对象指针置为nullptr. e.g. A程序员的auto_ptr指向X对象；B程序员的auto_ptr也指向X对象，B程序员的auto_ptr会剥夺X的控制权，A程序员的auto_ptr指向nullptr

```cpp
#include <iostream>
#include <string>
#include <memory>
using namespace std;
int main()
{
    {// 确定auto_ptr失效的范围, 结束之后轮流自动调用delete
        // 对int使用
        auto_ptr<int> pI(new int(10));
        cout << *pI << endl;                // 10

        // auto_ptr	C++ 17中移除	拥有严格对象所有权语义的智能指针
        // auto_ptr原理：在拷贝 / 赋值过程中，直接剥夺原对象对内存的控制权，转交给新对象，
        // 然后再将原对象指针置为nullptr（早期：NULL）。这种做法也叫管理权转移。
        // 他的缺点不言而喻，当我们再次去访问原对象时，程序就会报错，所以auto_ptr可以说实现的不好，
        // 很多企业在其库内也是要求不准使用auto_ptr。
        auto_ptr<string> languages[5] = {
            auto_ptr<string>(new string("C")),
            auto_ptr<string>(new string("Java")),
            auto_ptr<string>(new string("C++")),
            auto_ptr<string>(new string("Python")),
            auto_ptr<string>(new string("Rust"))
        };
        cout << "There are some computer languages here first time: \n";
        for (int i = 0; i < 5; ++i)
        {
            cout << *languages[i] << endl;
        }
        auto_ptr<string> pC;
        pC = languages[2]; // languges[2] loses ownership. 将所有权从languges[2]转让给pC，
        //此时languges[2]不再引用该字符串从而变成空指针
        cout << "There are some computer languages here second time: \n";
        for (int i = 0; i < 2; ++i)
        {
                cout << *languages[i] << endl;
        }
        cout << "The winner is " << *pC << endl;
        cout << "There are some computer languages here third time: \n";
        for (int i = 0; i < 5; ++i)
        {
        	cout << *languages[i] << endl;
        }
    }
    return 0;
}
```

### unique_ptr

- unique_ptr是专属所有权，只能被一个对象持有，不支持复制/赋值
- 移动语义: unique_ptr禁止拷贝语义，但有时需要转移所有权，于是提供了移动语义，即可以使用`std::move()`进行控制权的转移，原来的指针指向nullptr


```cpp
#include <memory>
#include <iostream>
using namespace std;
int main()
{
    // 在这个范围之外，unique_ptr被释放,指向的对象被销毁delete
    {
        auto i = unique_ptr<int>(new int(20));
        cout << *i << endl;
    }

    // unique_ptr
    // auto类型推断
    auto w = std::make_unique<int>(10);
    cout << *(w.get()) << endl;                             // 10
    //auto w2 = w; // 编译错误如果想要把 w 复制给 w2, 是不可以的。
    //  因为复制从语义上来说，两个对象将共享同一块内存。

    // unique_ptr 只支持移动语义, 即如下
    auto w2 = std::move(w); // w2 获得内存所有权，w 此时等于 nullptr
    cout << ((w.get() != nullptr) ? (*w.get()) : -1) << endl;       // -1
    cout << ((w2.get() != nullptr) ? (*w2.get()) : -1) << endl;   // 10
    return 0;
}
```

### shared_ptr

通过一个引用计数共享一个对象，当引用计数为0的时候，该对象没有被使用，可以进行析构
> 副作用：循环引用，导致堆里的内存无法正常回收，造成内存泄漏

```cpp
#include <iostream>
#include <memory>
using namespace std;
int main()
{
    // shared_ptr
    {
        //shared_ptr 代表的是共享所有权，即多个 shared_ptr 可以共享同一块内存。
        auto wA = shared_ptr<int>(new int(20));
        {
            auto wA2 = wA;
            cout << ((wA2.get() != nullptr) ? (*wA2.get()) : -1) << endl;       // 20
            cout << ((wA.get() != nullptr) ? (*wA.get()) : -1) << endl;           // 20
            cout << wA2.use_count() << endl;                                              // 2
            cout << wA.use_count() << endl;                                                // 2
        }
        //cout << wA2.use_count() << endl;
        cout << wA.use_count() << endl;                                                    // 1
        cout << ((wA.get() != nullptr) ? (*wA.get()) : -1) << endl;               // 20
        //shared_ptr 内部是利用引用计数来实现内存的自动管理，每当复制一个 shared_ptr，
        //	引用计数会 + 1。当一个 shared_ptr 离开作用域时，引用计数会 - 1。
        //	当引用计数为 0 的时候，则 delete 内存。
    }

    return 0;
}
```

```cpp
#include <iostream>
#include <memory>
using namespace std;
int main()
{
    // share_ptr同时支持move 语法
    // make_shared在栈上面产生对象,因为没有new; 系统默认会自动回收栈的内存空间
    auto wAA = std::make_shared<int>(30);
    auto wAA2 = std::move(wAA); // 此时 wAA 等于 nullptr，wAA2.use_count() 等于 1
    cout << ((wAA.get() != nullptr) ? (*wAA.get()) : -1) << endl;          // -1
    cout << ((wAA2.get() != nullptr) ? (*wAA2.get()) : -1) << endl;      // 30
    cout << wAA.use_count() << endl;                                                  // 0
    cout << wAA2.use_count() << endl;                                                // 1
    //将 wAA 对象 move 给 wAA2，意味着 wAA 放弃了对内存的所有权和管理，此时 wAA对象等于 nullptr。
    //而 wAA2 获得了对象所有权，但因为此时 wAA 已不再持有对象，因此 wAA2 的引用计数为 1。

    return 0;
}
```

### weak_ptr

weak_ptr设计成与shared_ptr共同工作，用一种观察者模式工作
- 协助shared_ptr工作，可获得资源的观测权
- 观察权意味着weak_ptr只对shared_ptr进行引用，而不改变其引用计数
- 当被观察的shared_ptr失效后，相应的weak_ptr也相应失效

```cpp
#include <iostream>
#include <memory>
using namespace std;

struct B;
struct A {
    shared_ptr<B> pb;
    ~A()
    {
        cout << "~A()" << endl;
    }
};
struct B {
    shared_ptr<A> pa;
    ~B()
    {
        cout << "~B()" << endl;
    }
};

// pa 和 pb 存在着循环引用，根据 shared_ptr 引用计数的原理，pa 和 pb 都无法被正常的释放。
// weak_ptr 是为了解决 shared_ptr 双向引用的问题。
struct BW;
struct AW
{
    shared_ptr<BW> pb;
    ~AW()
    {
        cout << "~AW()" << endl;
    }
};
struct BW
{
    weak_ptr<AW> pa;
    ~BW()
    {
        cout << "~BW()" << endl;
    }
};

void Test()
{
    cout << "Test shared_ptr and shared_ptr:  " << endl;
    shared_ptr<A> tA(new A());
    shared_ptr<B> tB(new B());
    cout << tA.use_count() << endl;//1
    cout << tB.use_count() << endl;//1
    tA->pb = tB;
    tB->pa = tA;
    cout << tA.use_count() << endl;// 2
    cout << tB.use_count() << endl;// 2
}
void Test2()
{
    cout << "Test weak_ptr and shared_ptr:  " << endl;
    shared_ptr<AW> tA(new AW());
    shared_ptr<BW> tB(new BW());
    cout << tA.use_count() << endl;// 1
    cout << tB.use_count() << endl;// 1
    tA->pb = tB;
    tB->pa = tA;
    cout << tA.use_count() << endl;// 1
    cout << tB.use_count() << endl;// 2
}

int main()
{
    Test(); // 循环引用导致析构~A(),~B()没有调用，产生内存泄漏
    Test2();// ~AW(),~BW()被调用，不产生内存泄漏


    return 0;
}
```

## reference

引用：特殊的指针，不允许修改的指针
> 可以认为是指定变量的别名(小名), 使用时可以认为是变量本身

使用指针的坑
- 空指针：不指向任何东西
- 野指针: 指向垃圾内存的指针
  - 指针变量没有初始化
  - 已经delete释放对象，指针没有置NULL
  - 指针超越了变量的作用范围
- 不知不觉改变了指针的值，却继续使用

使用引用
- 不存在空引用
- 必须初始化
- 一个引用永远指向它初始化的对象

有了指针为什么要用引用
> 为了支持函数的运算符重载, 使得重载看起来自然一些

有了引用为什么要用指针
> 为了兼容C语言；Java只有引用，可以看成是C++--

函数传递参数
- 对于内置的基础类型(int, double, float)，函数中参数传递pass by value更加高效
- 对于自定义的对象，函数中参数传递pass by reference to const更加高效

```cpp
// nullptr
#include <iostream>
#include <memory>
using namespace std;


int main()
{
    int* a=NULL; // c style
    cout<<a<<endl; // 0000000000000000
    // cout<<*a<<endl; // error

    int *b=nullptr; // c++ style
    cout<<b<<endl; // 0000000000000000

    return 0;
}
```

```cpp
#include <iostream>
using namespace std;

int main()
{
    int x1=1,x2=3;
    int& rx=x1;
    rx=2;
    cout<<x1<<endl; // 2
    cout<<rx<<endl; // 2

    rx=x2;
    cout<<x1<<endl; // 3
    cout<<rx<<endl; // 3

    return 0;
}
```

```cpp
// 指针 vs 引用
#include <iostream>
#include <assert.h>
using namespace std;

void swap1(int x, int y){
    // fail swap
    int temp;
    temp=x;
    x=y;
    y=temp;
}

void swap2(int& x, int& y){
    // success swap
    int temp;
    temp=x;
    x=y;
    y=temp;
}

void swap3(int* px, int* py){
    int temp;
    temp=*px;
    *px=*py;
    *py=temp;
}


int main()
{
    int a=10,b=20;

    // swap1(a,b); // swap fail
    // cout<<a<<' '<<b<<endl; // 10 20
    // assert(a==20 && b==10);

    swap2(a,b);
    assert(a==20 && b==10);
    cout<<a<<' '<<b<<endl; // 20 10

    swap3(&a,&b);
    cout<<a<<' '<<b<<endl; // 10 20

    return 0;
}
```

## sentence

## if, switch

分支过多，switch效率更高

使用场景
1. switch只支持常量值相等的判断
2. if可以判断区间
3. switch能做的，if也能; 反过来则不行

性能比较
- 分支少，性能差别不大；分支多，switch性能强
- if开始处几个分支效率高之后效率递减
- switch所有的case效率都一样

```cpp
#include <iostream>
using namespace std;

typedef enum __COLOR{
    RED,
    GREEN,
    BLUE,
    UNKNOW
} color;


int main()
{
    color c1=BLUE;
    cout<<c1<<endl; // 2

    if (c1==BLUE){
        cout<<"color blue"<<endl;
    }else if(c1==GREEN){
        cout<<"color green"<<endl;
    }else if (c1==RED){
        cour<<"color red"<<endl;
    }else{
        cout<<"other color"<<endl;
    }
}
```

```cpp
#include <iostream>
using namespace std;

typedef enum __COLOR{
    RED,
    GREEN,
    BLUE,
    UNKNOW
} color;


int main()
{
    color c1=BLUE;
    cout<<c1<<endl; // 2

    switch (c1) {
        case RED:
            cout<<"color red"<<endl;
            break;
        case GREEN:
            cout<<"color green"<<endl;
            break;
        case BLUE:
            cout<<"color blue"<<endl;
            break;
        default:
            cout<<"color red"<<endl;
            break;
    }
}
```

## enumerate

notes:
- 枚举值不能作为左值
- 非枚举值不能直接赋值给枚举变量
- 枚举变量可以直接赋值给非枚举变量

```cpp
#include <iostream>
using namespace std;

typedef enum __COLOR{
    RED,
    GREEN,
    BLUE,
    UNKNOW
} color;


int main()
{
    // 声明
    enum COLOR{RED,GREEN,BLUE};
    // 定义
    COLOR c1=BLUE;
    // c1=1; // 不能直接赋值，需要强制类型转换
    cout<<c1<<endl; // 2
    c1=COLOR(1);
    cout<<c1<<endl; //1
}
```

## struct,union

结构体，联合体

struct:没有各种private, protect, public的class

## loop

finish

## function

内联函数(inline): 直接将核心汇编代码复制到调用的地方，不用跳转；用空间换时间；
> inline函数需要编译器支持，复杂的递归逻辑，编译器会放弃调用

```cpp
#include <iostream>
using namespace std;

inline int MaxValue(int x, int y){
    return (x>y)?x:y;
}

int Fibonacci(int x){
    if (x==0){
        return 0;
    }else if(x==1){
        return 1;
    }else{
        return Fibonacci(x-1)+Fibonacci(x-2);
    }
}

int main()
{
    int a;
    a=MaxValue(3,4); //反汇编查看调用过程，发现实现inline
    cout<<a<<endl;

    int result;
    result=Fibonacci(5);//反汇编查看调用过程，发现没有实现inline
    cout<<result<<endl;
}
```

## recursion

递归的三种优化方式
1. 尾递归：所有递归形式的调用都出现在函数的尾部
2. 使用循环代替递归
3. 使用动态规划，空间换时间

```cpp
#include <iostream>
using namespace std;

//普通递归
int Fibonacci(int x){
    if (x==0){
        return 0;
    }else if(x==1){
        return 1;
    }else{
        return Fibonacci(x-1)+Fibonacci(x-2);
    }
}

//尾递归
int Fib_Tail(int n, int ret0, int ret1){
    if(n==0){
        return ret0;
    }else if(n==1){
        return ret1;
    }else{
        return Fib_Tail(n-1, ret1, ret0+ret1);
    }
}

//循环代替递归
int Fib_Loop(int n){
    if (n<2){
        return n;
    }
    int n0=0,n1=1;
    int tmp;
    for(int i=2;i<=n;i++){
        tmp=n0;
        n0=n1;
        n1=tmp+n1;
    }
    return n1;
}

//动态规划代替递归
int g_a[100]; //记录Fibnacci前100个值, 全局变量，默认数组元素都为0

int Fib_DP(int n){
    g_a[0]=0;
    g_a[1]=1;
    for (int i=2;i<=n;i++){
        if(g_a[i]==0){
            g_a[i]=g_a[i-1]+g_a[i-2];
        }
    }
    return g_a[n];
}

int main()
{
    int result;
    result=Fibonacci(5);
    cout<<result<<endl;

    result=Fib_Loop(5);
    cout<<result<<endl;

    result=Fib_Tail(5, 0, 1);
    cout<<result<<endl;

    result=Fib_DP(5);
    cout<<result<<endl;
}
```