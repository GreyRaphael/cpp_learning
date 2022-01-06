# C++ STL

- [C++ STL](#c-stl)
  - [Introduction](#introduction)
  - [Container](#container)
  - [functor](#functor)
  - [algorithm](#algorithm)
  - [iterator](#iterator)

## Introduction

Standard Tempalte Library
- 采用泛型来实现，不与任何特定数据结构和对象绑定，不必在环境类似的情况下重写代码
- 可以量身定做
- 自己可以进行扩充

STL六大组件
- 空间配置器: 管理内存空间
  - allocator
- 容器
  - string
  - vector
  - list
  - deque
  - map
  - set
  - multimap
  - multiset
- 适配器
  - stack
  - queue
  - priority_queue
- 迭代器
  - iterator
  - const_iterator
  - reverse_iterator
  - const_reverse_iterator
- 算法
  - find
  - swap
  - reverse
  - sort
  - merge
  - ...
- 仿函数
  - greater
  - less
  - ...

## Container

Container
- 序列化容器(Sequence Container): 逻辑连续结构；可以被sort, 对应的适配器有stack, queue, priority_queue
  - vector
  - deque
  - list
- 关联式容器(Associative Container): 树形结构
  - set
  - multiset
  - map
  - multimap

```cpp
#include <vector>
#include <list>
#include <queue>
#include <stack>
#include <algorithm> // foreach
#include <iostream>
using namespace std;


struct Display
{
    void operator()(int i)
    {
        cout << i << " ";
    }
};

int main()
{
    int iArr[] = { 11, 2,23,4,5 };

    vector<int> iVector(iArr, iArr + 4);
    list<int> iList(iArr, iArr + 4);
    deque<int> iDeque(iArr, iArr + 4);
    queue<int> iQueue(iDeque);     // 队列 先进先出
    stack<int> iStack(iDeque);         // 栈 先进后出
    priority_queue<int> iPQueue(iArr, iArr + 4);  // 优先队列，按优先权; 数值大的先出

    for_each( iVector.begin(), iVector.end(), Display() );
    cout << endl;
    for_each(iList.begin(), iList.end(), Display());
    cout << endl;
    for_each(iDeque.begin(), iDeque.end(), Display());
    cout << endl;

    while ( !iQueue.empty() )
    {
        cout << iQueue.front() << " ";  // 11 2 23 4
        iQueue.pop();
    }
    cout << endl;

    while (!iStack.empty())
    {
        cout << iStack.top() << " ";    // 4 23 2 11
        iStack.pop();
    }
    cout << endl;

    while (!iPQueue.empty())
    {
        cout << iPQueue.top() << " "; // 23 11 4 2
        iPQueue.pop();
    }
    cout << endl;

    return 0;
}
```


```cpp
#include <string>
#include<map>
#include <algorithm>
#include <iostream>
using namespace std;

struct Display
{
    void operator()(pair<string, double> info) // override (), 仿函数
    {
        cout << info.first << ": " << info.second << endl;
    }
};


int main()
{
    map<string, double> studentSocres;
    studentSocres["LiMing"] = 95.0;
    studentSocres["LiHong"] = 98.5;
    studentSocres.insert( pair<string, double>("zhangsan", 100.0) );
    studentSocres.insert(pair<string, double>("Lisi", 98.6));
    studentSocres.insert(pair<string, double>("wangwu", 94.5));
    studentSocres.insert(map<string, double>::value_type("zhaoliu", 95.5) );
    studentSocres["wangwu"] = 88.5;
    for_each(studentSocres.begin(), studentSocres.end(), Display());
    cout << endl;

    map<string, double>::iterator iter;
    iter = studentSocres.find("zhaoliu");
    if (iter != studentSocres.end())
    {
        cout << "Found the score is: " << iter->second << endl;
    }
    else
    {
        cout << "Didn't find the key." << endl;
    }

    // 使用迭代器完成遍历查找的过程
    iter = studentSocres.begin();
    while (iter != studentSocres.end())
    {
        if (iter->second < 98.0)  // 去除不是优秀的同学
        {
            studentSocres.erase(iter++);  // 注意：迭代器失效问题
        }
        else
        {
            iter++;
        }
    }
    for_each(studentSocres.begin(), studentSocres.end(), Display());
    cout << endl;


    for (iter = studentSocres.begin(); iter != studentSocres.end(); iter++)
    {
        if (iter->second <= 98.5)
        {
            iter = studentSocres.erase(iter);  // 注意：迭代器失效问题
        }
    }
    for_each(studentSocres.begin(), studentSocres.end(), Display());
    cout << endl;

    // find得到并删除元素
    //iter = studentSocres.find("LiHong");
    //studentSocres.erase(iter);
    //for_each(studentSocres.begin(), studentSocres.end(), Display());

    //int n = studentSocres.erase("LiHong1");
    //cout << n << endl;
    //for_each(studentSocres.begin(), studentSocres.end(), Display());

    studentSocres.erase(studentSocres.begin(), studentSocres.end());
    for_each(studentSocres.begin(), studentSocres.end(), Display());
    cout << endl;

    return 0;
}
```

## functor

仿函数：主要是为了搭配stl算法使用，本质是class重载了一个`operator()`, 创建了一个行为类似函数的对象 
> 函数指针不能满足STL对抽象性的要求，不能满足软件积木的要求，无法与STL其他组件搭配

## algorithm

STL算法分成4类：包含于<algorithm>, <numeric>, <functional>
1. 非可变序列算法
2. 可变系列算法
3. 排序算法
4. 数值算法

## iterator

迭代器是一种smart pointer, 用于访问**顺序容器**和**关联容器**中的元素，相当于容器和操作容器算法间的中介

迭代器分类
1. 正向迭代器: iterator
2. 常量正向迭代器: const_iterator, 不希望修改访问的内容
3. 反向迭代器: reverse_iterator
4. 常量反向迭代器: const_reverse_iterator

Container & Iterator
- vector: 随机访问
- deque: 随机访问
- list: 双向访问
- set/multiset: 双向访问
- map/multimap: 双向访问
- stack: 不支持迭代器
- queue: 不支持迭代器
- priority_queue: 不支持迭代器

