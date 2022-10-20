# set容器

## 一、概述

set 容器内的元素会被自动排序，set 与 map 不同，set 中的元素即是键值又是实值，set 不允许两个元素有相同的键值。不能通过 set 的迭代器去修改 set 元素，原因是修改元素会破坏 set 组织。当对容器中的元素进行插入或者删除时，操作之前的所有迭代器在操作之后依然有效。

## 二、定义及初始化

使用之前必须加相应容器的头文件：

#include  // set属于std命名域的，因此需要通过命名限定，例如using std::set;

定义的代码如下：

set a; // 定义一个int类型的集合a

// set a(10); // error，未定义这种构造函数

// set a(10, 1); // error，未定义这种构造函数

set b(a); // 定义并用集合a初始化集合b

set b(a.begin(), a.end()); // 将集合a中的所有元素作为集合b的初始值

除此之外，还可以直接使用数组来初始化向量：

int n[] = { 1, 2, 3, 4, 5 };

list a(n, n + 5); // 将数组n的前5个元素作为集合a的初值

## 三、基本操作

### 1. 容量函数

- 容器大小：st.size();
- 容器最大容量：st.max_size();
- 容器判空：st.empty();
- 查找键 key 的元素个数：st.count(key);

```cpp
#include <iostream>
#include <set>

using namespace std;

int main(int argc, char* argv[])
{
    set<int> st;
    for (int i = 0; i<6; i++)
    {
        st.insert(i);
    }

    cout << st.size() << endl; // 输出：6
    cout << st.max_size() << endl; // 输出：214748364
    cout << st.count(2) << endl; // 输出：1
    if (st.empty())
        cout << "元素为空" << endl; // 未执行

    return 0;
}
```

### 2. 添加函数

- 在容器中插入元素：st.insert(const T& x);
- 任意位置插入一个元素：st.insert(iterator it, const T& x);

```cpp
#include <iostream>
#include <set>

using namespace std;

int main(int argc, char* argv[])
{
    set<int> st;

    // 在容器中插入元素
    st.insert(4);
    // 任意位置插入一个元素
    set<int>::iterator it = st.begin();
    st.insert(it, 2);

    // 遍历显示
    for (it = st.begin(); it != st.end(); it++)
        cout << *it << " "; // 输出：2 4
    cout << endl;

    return 0;
}
```

### 3. 删除函数

- 删除容器中值为 elem 的元素：st.pop_back(const T& elem);
- 删除it迭代器所指的元素：st.erase(iterator it);
- 删除区间 [first,last] 之间的所有元素：st.erase(iterator

first, iterator last);
- 清空所有元素：st.clear();

```cpp
#include <iostream>
#include <set>

using namespace std;

int main(int argc, char* argv[])
{
    set<int> st;
    for (int i = 0; i < 8; i++)
        st.insert(i);

    // 删除容器中值为elem的元素
    st.erase(4);
    // 任意位置删除一个元素
    set<int>::iterator it = st.begin();
    st.erase(it);
    // 删除[first,last]之间的元素
    st.erase(st.begin(), ++st.begin());

    // 遍历显示
    for (it = st.begin(); it != st.end(); it++)
        cout << *it << " "; // 输出：2 3 5 6 7
    cout << endl;

    // 清空所有元素
    st.clear();

    // 判断set是否为空
    if (st.empty())
        cout << "集合为空" << endl; // 输出：集合为空

    return 0;
}
```

### 4. 访问函数

查找键 key 是否存在,若存在，返回该键的元素的迭代器；若不存在，返回set.end()： st.find(key);

```cpp
#include <iostream>
#include <set>

using namespace std;

int main(int argc, char* argv[])
{
    set<int> st;
    for (int i = 0; i < 6; i++)
        st.insert(i);

    // 通过find(key)查找键值
    set<int>::iterator it;
    it = st.find(2);
    cout << *it << endl; // 输出：2

    return 0;
}
```

### 5. 其他函数

交换两个同类型容器的元素：swap(set&, set&);或lst.swap(set&);

```cpp
#include <iostream>
#include <set>

using namespace std;

int main(int argc, char* argv[])
{
    set<int> st1;
    st1.insert(1);
    st1.insert(2);
    st1.insert(3);
    set<int> st2;
    st2.insert(4);
    st2.insert(5);
    st2.insert(6);
    // 交换两个容器的元素
    st1.swap(st2);

    // 遍历显示
    cout << "交换后的st1: ";
    set<int>::iterator it;
    for (it = st1.begin(); it != st1.end(); it++)
        cout << *it << " "; // 输出：4 5 6
    cout << endl;
    // 遍历显示
    cout << "交换后的st2: ";
    for (it = st2.begin(); it != st2.end(); it++)
        cout << *it << " "; // 输出：1 2 3
    cout << endl;

    return 0;
}
```

## 四、迭代器与算法

### 1. 迭代器

- 开始迭代器指针：st.begin();
- 末尾迭代器指针：st.end(); // 指向最后一个元素的下一个位置
- 指向常量的开始迭代器指针：st.cbegin(); // 意思就是不能通过这个指针来修改所指的内容，但还是可以通过其他方式修改的，而且指针也是可以移动的。
- 指向常量的末尾迭代器指针：lst.cend();
- 反向迭代器指针，指向最后一个元素：st.rbegin();
- 反向迭代器指针，指向第一个元素的前一个元素：st.rend();
- 返回最后一个 key<=keyElem 元素的迭代器：st.lower_bound(keyElem);
- 返回第一个 key>keyElem 元素的迭代器：st.upper_bound(keyElem);
- 返回容器中 key 与 keyElem 相等的上下限的两个迭代器，这两个迭代器被放在对组（pair）中： st.equal_range(keyElem);

```cpp
#include <iostream>
#include <set>

using namespace std;

int main(int argc, char* argv[])
{
    set<int> st;
    st.insert(1);
    st.insert(2);
    st.insert(3);

    cout << *(st.begin()) << endl; // 输出：1
    cout << *(--st.end()) << endl; // 输出：3
    cout << *(st.cbegin()) << endl; // 输出：1
    cout << *(--st.cend()) << endl; // 输出：3
    cout << *(st.rbegin()) << endl; // 输出：3
    cout << *(--st.rend()) << endl; // 输出：1
    cout << *(st.lower_bound(2)) << endl; // 输出：2
    cout << *(st.upper_bound(2)) << endl; // 输出：3
    pair<set<int>::iterator, set<int>::iterator> t_pair = st.equal_range(2);
    cout << *(t_pair.first) << endl; // 输出：2
    cout << *(t_pair.second) << endl; // 输出：3
    cout << endl;

    return 0;
}
```

### 2. 算法

遍历元素

```cpp
set<int>::iterator it;
for (it = st.begin(); it != st.end(); it++)
    cout << *it << endl;
```

## 五、总结

可以看到，set 与序列式容器的用法有以下几处不同：

set 不支持 resize() 函数；

set 容器不提供下标操作符。为了通过键从 set 中获取元素，可使用 find 运算；

set 只能使用insert的两种重载函数插入，不支持 push_back() 和 push_front() 函数；

set 不支持 STL 里的 reverse 和 sort 算法；

set 能不通过迭代器，只通过元素值来删除该元素；

set 只支持默认构造函数和拷贝构造函数，没有其它重载的构造函数。
