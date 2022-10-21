> lambda 源自希腊字母表中第 11 位的 λ，在计算机科学领域，它则是被用来表示一种匿名函数。所谓匿名函数，简单地理解就是没有名称的函数，又常被称为 lambda 函数或者 lambda 表达式。


## 定义

```cpp
[capture list] (params list) mutable noexcept/throw() -> return type { function body }
```

- capture list：捕获外部变量列表
- params list：形参列表
- mutable指示符：用来说用是否可以修改捕获的变量
- noexcept/throw()：异常设定。标注 noexcept 关键字，则表示函数体内不会抛出任何异常；使用 throw() 可以指定 lambda 函数内部可以抛出的异常类型
- return type：返回类型
- function body：函数体

外部变量的定义方式：

| **外部变量格式** | **功能** |
| --- | --- |
| [] | 空方括号表示当前 lambda 匿名函数中不导入任何外部变量 |
| [=] | 只有一个 = 等号，表示以值传递的方式导入所有外部变量 |
| [&] | 只有一个 & 符号，表示以引用传递的方式导入所有外部变量； |
| [val1,val2,...] | 表示以值传递的方式导入 val1、val2 等指定的外部变量，同时多个变量之间没有先后次序 |
| [&val1,&val2,...] | 表示以引用传递的方式导入 val1、val2等指定的外部变量，多个变量之间没有前后次序 |
| [val,&val2,...] | 以上 2 种方式还可以混合使用，变量之间没有前后次序 |
| [=,&val1,...] | 表示除 val1 以引用传递的方式导入外，其它外部变量都以值传递的方式导入 |
| [this] | 表示以值传递的方式导入当前的 this 指针 |


## 示例

### 值传递和引用传递的区别

值传递不可以改变外部变量的值

引用传递可以改变外部变量的值

```cpp
#include <iostream>
using namespace std;
//全局变量
int all_num = 0;
int main()
{
    //局部变量
    int num_1 = 1;
    int num_2 = 2;
    int num_3 = 3;
    cout << "lambda1:\n";
    auto lambda1 = [=]{
        //全局变量可以访问甚至修改
        all_num = 10;
        //函数体内只能使用外部变量，而无法对它们进行修改
        cout << num_1 << " "
             << num_2 << " "
             << num_3 << endl;
    };
    lambda1();
    cout << all_num <<endl;

    cout << "lambda2:\n";
    auto lambda2 = [&]{
        all_num = 100;
        num_1 = 10;
        num_2 = 20;
        num_3 = 30;
        cout << num_1 << " "
             << num_2 << " "
             << num_3 << endl;
    };
    lambda2();
    cout << all_num << endl;
    return 0;
}
```

使用`mutable`关键字可修改外部变量，但是修改的是外部变量的拷贝，无法修改实际外部变量的值

```cpp
auto lambda1 = [=]() mutable{
    num_1 = 10;
    num_2 = 20;
    num_3 = 30;
    //函数体内只能使用外部变量，而无法对它们进行修改
    cout << num_1 << " "
         << num_2 << " "
         << num_3 << endl;
};
```

### 抛出异常

```cpp
#include <iostream>
using namespace std;
int main()
{
    auto except = []()throw(int) {
        throw 10;
    };
    try {
        except();
    }
    catch (int) {
        cout << "捕获到了整形异常";
    }
    return 0;
}
```
