## 一、介绍

> C++ 中 map 提供的是一种键值对容器，里面的数据都是成对出现的,如下图：每一对中的第一个值称之为关键字(key)，每个关键字只能在 map 中出现一次；第二个称之为该关键字的对应值。


![](https://image-hosts-1305019894.file.myqcloud.com/2021/03/10/6c534b1de447f.png#alt=)

## 二、map的使用

1. 需要导入头文件

```cpp
#include <map> // STL头文件没有扩展名.h
```

2. map对象是一个模版类，需要关键字和存储对象两个模版参数

```cpp
std::map<int , std::string> person;
```

3. 可以对模版进行类型定义使其使用方便

```cpp
typedef std::map<int , std::string> MAP_INI_STRING;
MAP_INI_STRING person;
```

## 三、map的基本操作函数

- begin()           返回指向 map 头部的迭代器
- clear(）          删除所有元素
- begin()           返回指向 map 头部的迭代器
- clear(）          删除所有元素
- count()           返回指定元素出现的次数
- empty()           如果 map 为空则返回 true
- end()             返回指向 map 末尾的迭代器
- equal_range()     返回特殊条目的迭代器对
- erase()           删除一个元素
- find()            查找一个元素
- get_allocator()   返回map的配置器
- insert()          插入元素
- key_comp()        返回比较元素key的函数
- lower_bound()     返回键值>=给定元素的第一个位置
- max_size()        返回可以容纳的最大元素个数
- rbegin()          返回一个指向map尾部的逆向迭代器
- rend()            返回一个指向map头部的逆向迭代器
- size()            返回map中元素的个数
- swap()            交换两个map
- upper_bound()     返回键值>给定元素的第一个位置
- value_comp()      返回比较元素value的函数

## 四、map的构造

1. map最基本的构造函数；

```cpp
std::map<int , std::string> mapPerson;
```

2. map添加数据；

- insert 函数插入 pair 数据

```cpp
std::map < int , std::string > mapPerson;
mapPerson.insert(pair < int,string > (1,"Jim"));
```

- insert 函数插入 value_type 数据

```cpp
mapPerson.insert(std::map < int, std::string > ::value_type (2, "Tom"));
```

- 用数组方式插入数据

```cpp
mapPerson[3] = "Jerry";
```

## 五、map数据的遍历

三种最常用的遍历方法：

1. 前向迭代器

```cpp
std::map < int ,std::string > ::iterator it;
    std::map < int ,std::string > ::iterator itEnd;
    it = mapPerson.begin();
    itEnd = mapPerson.end();
    while (it != itEnd) {
    cout<<it->first<<' '<<it->second<<endl;  
    it++;
}
```

2. 反向迭代器

```cpp
std::map < int, string > ::reverse_iterator iter;  
for(iter = mapPerson.rbegin(); iter != mapPerson.rend(); iter++) 
    cout<<iter->first<<"  "<<iter->second<<endl;
```

3. 数组形式

```cpp
mapPerson.insert(std::map<int, std::string>::value_type (1, "Tom"));
mapPerson[2] = "Jim";
mapPerson[3] = "Jerry";

int nSize = mapPerson.size();
for(int n = 1; n <= nSize; n++)
    qDebug()<<QString::fromStdString(mapPerson[n]);
```

三种都是遍历，建议使用前向迭代器，慎用使用数组形成。

## 六、map中元素的查找

find() 函数返回一个迭代器指向键值为 key的元素，如果没找到就返回指向 map 尾部的迭代器。

```cpp
map<int ,string > ::iterator l_it;; 
l_it = maplive.find(112);
if(l_it == maplive.end())
   cout<<"Not find 112"<<endl;
else cout<<"Find 112"<<endl;
```

## 七、map中元素的删除

```cpp
iterator erase（iterator it)    ;//通过一个条目对象删除
iterator erase（iterator first，iterator last）； //删除一个范围
size_type erase(const Key&key);    //通过关键字删除
clear()；//就相当于enumMap.erase(enumMap.begin(),enumMap.end());
```

## 八、map中swap的用法

Map 中的 swap 不是一个容器中的元素交换，而是两个容器交换

```cpp
#include <map> 
 #include <iostream>
  using namespace std;
  int main( )
  {
    map < int, int > m1, m2, m3;
    map < int, int >::iterator m1_Iter;
    m1.insert ( pair < int, int >  ( 1, 10 ) );
    m1.insert ( pair < int, int >  ( 2, 20 ) );
    m1.insert ( pair < int, int >  ( 3, 30 ) );
    m2.insert ( pair < int, int >  ( 10, 100 ) );
    m2.insert ( pair < int, int >  ( 20, 200 ) );
    m3.insert ( pair < int, int >  ( 30, 300 ) );
    cout << "The original map m1 is:";

   for ( m1_Iter = m1.begin( ); m1_Iter != m1.end( ); m1_Iter++ )
      cout << " " << m1_Iter->second;
      cout   << "." << endl;
   // This is the member function version of swap
   //m2 is said to be the argument map; m1 the target map
   m1.swap( m2 );
   cout << "After swapping with m2, map m1 is:";

   for ( m1_Iter = m1.begin( ); m1_Iter != m1.end( ); m1_Iter++ )
      cout << " " << m1_Iter -> second;
      cout  << "." << endl;
   cout << "After swapping with m2, map m2 is:";

   for ( m1_Iter = m2.begin( ); m1_Iter != m2.end( ); m1_Iter++ )
      cout << " " << m1_Iter -> second;
      cout  << "." << endl;
   // This is the specialized template version of swap
   swap( m1, m3 );
   cout << "After swapping with m3, map m1 is:";

   for ( m1_Iter = m1.begin( ); m1_Iter != m1.end( ); m1_Iter++ )
      cout << " " << m1_Iter -> second;
      cout   << "." << endl;
}
```

运行结果

```
The original map m1 is: 10 20 30.
After swapping with m2, map m1 is: 100 200.
After swapping with m2, map m2 is: 10 20 30.
After swapping with m3, map m1 is: 300.
```

## 九、map的排序

Map 中的元素是自动按 key 升序排序

```cpp
 #include <map> 
 #include <iostream>
 using namespace std;
 int main( )
 {
   map < int, int > m1;
   map < int, int >::iterator m1_Iter;
   m1.insert ( pair < int, int >  ( 1, 20 ) );
   m1.insert ( pair < int, int >  ( 4, 40 ) );
   m1.insert ( pair < int, int >  ( 3, 60 ) );
   m1.insert ( pair < int, int >  ( 2, 50 ) );
   m1.insert ( pair < int, int >  ( 6, 40 ) );
   m1.insert ( pair < int, int >  ( 7, 30 ) );
   cout << "The original map m1 is:"<<endl;
   for ( m1_Iter = m1.begin( ); m1_Iter != m1.end( ); m1_Iter++ )
      cout <<  m1_Iter->first<<" "<<m1_Iter->second<<endl;
}
```

运行结果

```
1 20
2 50
3 60
4 40
6 40
7 30
```
