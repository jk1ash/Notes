# Shell 基本运算符

## 算术运算符

下表列出了常用的算术运算符

在下表中，我们定义 变量 a = 7， 变量 b = 17

| 运算符 | 说明 | 示例 |
| --- | --- | --- |
| + | 加法 | expr ![](https://g.yuque.com/gr/latex?a%20%2B#card=math&code=a%20%2B&id=tcR4J)b  结果为 30 |
| - | 减法 | expr ![](https://g.yuque.com/gr/latex?a%20-#card=math&code=a%20-&id=jIxhT)b  结果为 -10 |
| * | 乘法 | expr ![](https://g.yuque.com/gr/latex?a%20%5C*#card=math&code=a%20%5C%2A&id=WcqaH)b  结果为  200 |
| / | 除法 | expr ![](https://g.yuque.com/gr/latex?b%20%2F#card=math&code=b%20%2F&id=QxuwE)a  结果为 2 |
| % | 取余 | expr ![](https://g.yuque.com/gr/latex?b%20%25#card=math&code=b%20%25&id=OGNuD)a  结果为 0 |
| = | 赋值 | a=$b 将把变量 b 的值赋给 a |
| == | 比较两个数字，相同则返回 true | [ ![](https://g.yuque.com/gr/latex?a%20%3D%3D#card=math&code=a%20%3D%3D&id=vR9cH)b ] 返回 false |
| != | 比较两个数字，不相同则返回 true | [ ![](https://g.yuque.com/gr/latex?a%20!%3D#card=math&code=a%20%21%3D&id=DuiE4)b ] 返回 true |


虽然原生 bash 不支持简单的数学运算，但是可以通过其他命令来实现，例如  awk  和  expr ，其中 expr 最常用
expr 命令
expr 是一款表达式计算工具，使用它能完成表达式的求值操作

**示例**

```bash
#!/bin/bash
# filename: demo.sh

a=7
b=17

val=`expr $a + $b`
echo "a + b : $val"

val=`expr $a - $b`
echo "a - b : $val"

val=`expr $a \* $b`
echo "a * b : $val"

val=`expr $b / $a`
echo "b / a : $val"

val=`expr $b % $a`
echo "b % a : $val"

if [ $a == $b ]
then
   echo "a 等于 b"
fi
if [ $a != $b ]
then
   echo "a 不等于 b"
fi
```

执行脚本，输出结果如下：

```bash
$ sh ./demo.sh
a + b : 24
a - b : -10
a * b : 119
b / a : 2
b % a : 3
a 不等于 b
```

**注意**

1. 表达式和运算符之间要有空格，例如 2+2 是不对的，必须写成 2 + 2，这点与绝大多数编程语言不一样
2. 完整的表达式要被一对反引号(``)包含，也就是英文输入法下 Esc 键下边那个键
条件表达式要放在方括号之间，并且方括号与表达式之间要有空格，例如: [![](https://g.yuque.com/gr/latex?a%3D%3D#card=math&code=a%3D%3D&id=QPOqQ)b] 是错误的，必须写成 [ ![](https://g.yuque.com/gr/latex?a%20%3D%3D#card=math&code=a%20%3D%3D&id=mzMEu)b ]
3. 乘号(*)前边必须加反斜杠()才能实现乘法运算

## 关系运算符

关系运算符常用操作有：检测两个数字是否相等，是否不相等，一个是否大于另一个，一个是否大于等于另一个等等 6 种

关系运算符只支持数字，不支持字符串，除非字符串的内容全部是数字

在下表中，我们定义变量 a = 7，变量 b = 17

| 运算符 | 说明 | 示例 |
| --- | --- | --- |
| -eq | 检测两个数是否相等，相等返回 true | [ ![](https://g.yuque.com/gr/latex?a%20-eq#card=math&code=a%20-eq&id=TYh87)b ] 返回 false |
| -ne | 检测两个数是否相等，不相等返回 true | [ ![](https://g.yuque.com/gr/latex?a%20-ne#card=math&code=a%20-ne&id=VQ37j)b ] 返回 true |
| -gt | 检测左边的数是否大于右边的，如果是，则返回 true | [ ![](https://g.yuque.com/gr/latex?a%20-gt#card=math&code=a%20-gt&id=f510z)b ] 返回 false |
| -lt | 检测左边的数是否小于右边的，如果是，则返回 true | [ ![](https://g.yuque.com/gr/latex?a%20-lt#card=math&code=a%20-lt&id=xBBBI)b ] 返回 true |
| -ge | 检测左边的数是否大于等于右边的，如果是，则返回 true | [ ![](https://g.yuque.com/gr/latex?a%20-ge#card=math&code=a%20-ge&id=SNy60)b ] 返回 false |
| -le | 检测左边的数是否小于等于右边的，如果是，则返回 true | [ ![](https://g.yuque.com/gr/latex?a%20-le#card=math&code=a%20-le&id=Fb3ey)b ] 返回 true |


**示例**

```bash
#!/bin/bash

a=7
b=17

if [ $a -eq $b ]
then
   echo "$a -eq $b : a 等于 b"
else
   echo "$a -eq $b: a 不等于 b"
fi
if [ $a -ne $b ]
then
   echo "$a -ne $b: a 不等于 b"
else
   echo "$a -ne $b : a 等于 b"
fi
if [ $a -gt $b ]
then
   echo "$a -gt $b: a 大于 b"
else
   echo "$a -gt $b: a 不大于 b"
fi
if [ $a -lt $b ]
then
   echo "$a -lt $b: a 小于 b"
else
   echo "$a -lt $b: a 不小于 b"
fi
if [ $a -ge $b ]
then
   echo "$a -ge $b: a 大于或等于 b"
else
   echo "$a -ge $b: a 小于 b"
fi
if [ $a -le $b ]
then
   echo "$a -le $b: a 小于或等于 b"
else
   echo "$a -le $b: a 大于 b"
fi
```

运行脚本，输出结果如下：

```bash
$ sh ./demo.sh
7 -eq 17: a 不等于 b
7 -ne 17: a 不等于 b
7 -gt 17: a 不大于 b
7 -lt 17: a 小于 b
7 -ge 17: a 小于 b
7 -le 17: a 小于或等于 b
```

## 布尔运算符

Linux Shell 布尔运算符有三个：非运算、与运算、或运算

在下表中，我们定义变量 a = 7，变量 b = 17

| 运算符 | 说明 | 示例 |
| --- | --- | --- |
| ! | 非运算，表达式为真，则返回 false，否则返回 true | [ ! false ] 返回 true |
| -o | 或运算，有一个表达式为真，则返回 true [ ![](https://g.yuque.com/gr/latex?a%20-lt%2020%20-o#card=math&code=a%20-lt%2020%20-o&id=KY0lb)b -gt 100 ] | 返回 true |
| -a | 与运算，两个表达式都为真，才返回 true [ ![](https://g.yuque.com/gr/latex?a%20-lt%2020%20-a#card=math&code=a%20-lt%2020%20-a&id=cmgF8)b -gt 100 ] | 返回 false |


**实例**

```bash
#!/bin/bash
# filename: demo.sh

a=7
b=17

if [ $a != $b ]
then
   echo "$a != $b : a 不等于 b"
else
   echo "$a != $b: a 等于 b"
fi
if [ $a -lt 100 -a $b -gt 15 ]
then
   echo "$a 小于 100 且 $b 大于 15 : 返回 true"
else
   echo "$a 小于 100 且 $b 大于 15 : 返回 false"
fi
if [ $a -lt 100 -o $b -gt 100 ]
then
   echo "$a 小于 100 或 $b 大于 100 : 返回 true"
else
   echo "$a 小于 100 或 $b 大于 100 : 返回 false"
fi
if [ $a -lt 5 -o $b -gt 100 ]
then
   echo "$a 小于 5 或 $b 大于 100 : 返回 true"
else
   echo "$a 小于 5 或 $b 大于 100 : 返回 false"
fi
```

执行脚本，输出结果如下所示：

```bash
$ sh ./demo.sh
7 != 17 : a 不等于 b
7 小于 100 且 17 大于 15 : 返回 true
7 小于 100 或 17 大于 100 : 返回 true
7 小于 5 或 17 大于 100 : 返回 false
```

逻辑运算符
Linux Shell 逻辑运算符只有两个，逻辑与  &&  和 逻辑或  ||

下表中我们定义变量 a = 7， 变量 b = 17

| 运算符 | 说明 | 示例 |
| --- | --- | --- |
| && | 逻辑与 | [[a -lt 100 &&b -gt 100]] 返回 false |
|  |  |  |


**示例**

```bash
#!/bin/bash
# filename: demo.sh

a=7
b=17

if [[ $a -lt 100 && $b -gt 100 ]]
then
   echo "返回 true"
else
   echo "返回 false"
fi

if [[ $a -lt 100 || $b -gt 100 ]]
then
   echo "返回 true"
else
   echo "返回 false"
fi
```

运行脚本，输出结果如下：

```bash
$ sh ./demo.sh
返回 false
返回 true
```

## 字符串运算符

字符串的常用操作有比较两个字符串是否相等，检测字符串长度，检测字符串是否为空等

在下表中，我们定义变量 a = "xyz" 变量 b = "lmn"

| 运算符 | 说明 | 示例 |
| --- | --- | --- |
| = | 检测两个字符串是否相等，相等返回 true | [ ![](https://g.yuque.com/gr/latex?a%20%3D#card=math&code=a%20%3D&id=AHXy1)b ] 返回 false |
| != | 检测两个字符串是否相等，不相等返回 true | [ ![](https://g.yuque.com/gr/latex?a%20!%3D#card=math&code=a%20%21%3D&id=NOPL6)b ] 返回 true |
| -z | 检测字符串长度是否为0，为0返回 true | [ -z $a ] 返回 false |
| -n | 检测字符串长度是否为0，不为0返回 true | [ -n $a ] 返回 true |
| $ | 检测字符串是否为空，不为空返回 true | [ $a ] 返回 true |


**示例**

```bash
#!/bin/bash
# filename: demo.sh

a="xyz"
b="lmn"

if [ $a = $b ]
then
   echo "$a = $b : a 等于 b"
else
   echo "$a = $b: a 不等于 b"
fi
if [ $a != $b ]
then
   echo "$a != $b : a 不等于 b"
else
   echo "$a != $b: a 等于 b"
fi
if [ -z $a ]
then
   echo "-z $a : 字符串长度为 0"
else
   echo "-z $a : 字符串长度不为 0"
fi
if [ -n $a ]
then
   echo "-n $a : 字符串长度不为 0"
else
   echo "-n $a : 字符串长度为 0"
fi
if [ $a ]
then
   echo "$a : 字符串不为空"
else
   echo "$a : 字符串为空"
fi
```

运行脚本，输出结果如下：

```bash
$ sh ./demo.sh
xyz = lmn: a 不等于 b
xyz != lmn : a 不等于 b
-z xyz : 字符串长度不为 0
-n xyz : 字符串长度不为 0
xyz : 字符串不为空
```

## 文件测试运算符

文件测试运算符用于检测 Unix 文件是否存在，是否可读可写等的各种属性

| 运算符 | 说明 | 示例 |
| --- | --- | --- |
| -b file | 检测文件是否块设备文件，是则返回 true | [ -b $file ] 返回 false |
| -c file | 检测文件是否字符设备文件，是则返回 true | [ -c $file ] 返回 false |
| -d file | 检测文件是否目录，是则返回 true | [ -d $file ] 返回 false |
| -f file | 检测文件是否普通文件，是则返回 true | [ -f $file ] 返回 true |
| -g file | 检测文件是否设置了 SGID 位，是则返回 true | [ -g $file ] 返回 false |
| -k file | 检测文件是否设置了粘着位1，是则返回 true | [ -k $file ] 返回 false |
| -p file | 检测文件是否命名管道，是则返回 true | [ -p $file ] 返回 false |
| -u file | 检测文件是否设置了 SUID 位，是则返回 true | [ -u $file ] 返回 false |
| -r file | 检测文件是否可读，是则返回 true | [ -r $file ] 返回 true |
| -w file | 检测文件是否可写，是则返回 true | [ -w $file ] 返回 true |
| -x file | 检测文件是否可执行，是则返回 true | [ -x $file ] 返回 true |
| -s file | 检测文件大小是否大于0，大于0返回 true | [ -s $file ] 返回 true |
| -e file | 检测文件或目录是否存在，是则返回 true | [ -e $file ] 返回 true |


**示例**

假设存在文件  /var/www/cn_twle_www/demo.sh ，文件大小为 181 字节，当前登录用户拥有 rwx 权限。

我们利用上表中的文件操作符测试 demo.sh 文件各种属性

```bash
#!/bin/bash
# filename: demo.sh

file="/var/www/cn_twle_www/demo.sh"
if [ -r $file ]
then
   echo "文件可读"
else
   echo "文件不可读"
fi
if [ -w $file ]
then
   echo "文件可写"
else
   echo "文件不可写"
fi
if [ -x $file ]
then
   echo "文件可执行"
else
   echo "文件不可执行"
fi
if [ -f $file ]
then
   echo "文件为普通文件"
else
   echo "文件为特殊文件"
fi
if [ -d $file ]
then
   echo "文件是个目录"
else
   echo "文件不是个目录"
fi
if [ -s $file ]
then
   echo "文件不为空"
else
   echo "文件为空"
fi
if [ -e $file ]
then
   echo "文件存在"
else
   echo "文件不存在"
fi
```

运行脚本输出

```bash
$ sh ./demo.sh
文件可读
文件可写
文件可执行
文件为普通文件
文件不是个目录
文件不为空
文件存在
```
