# Go 面试题

## 基础语法

### new和make的区别

都是用于内存的分配（堆上），但是make只用于slice、map以及channel的初始化（非零值）；而new用于类型的内存分配，并且内存置为零。

### `=` 和 `:=` 的区别

= 仅赋值

:= 声明+赋值

```go
var foo int
foo = 10
// 等价于
foo := 10
```

### 指针的作用

指针用来保存变量的地址。

例如

```go
var x =  5
var p *int = &x
fmt.Printf("x = %d",  *p) // x 可以用 *p 访问
```

- 运算符，也称为解引用运算符，用于访问地址中的值。

＆运算符，也称为地址运算符，用于返回变量的地址。

### Go 允许多个返回值吗

允许

```go
func swap(x, y string) (string, string) {
   return y, x
}

func main() {
   a, b := swap("A", "B")
   fmt.Println(a, b) // B A
}
```

### Go 有异常类型吗？

Go 没有异常类型，只有错误类型（Error），通常使用返回值来表示异常状态。

```go
f, err := os.Open("test.txt")
if err != nil {
    log.Fatal(err)
}
```

### 什么是协程（Goroutine）

Goroutine 是与其他函数或方法同时运行的函数或方法。 Goroutines 可以被认为是轻量级的线程。 与线程相比，创建 Goroutine 的开销很小。 Go应用程序同时运行数千个 Goroutine 是非常常见的做法。

### 如何高效地拼接字符串

Go 语言中，字符串是只读的，也就意味着每次修改操作都会创建一个新的字符串。如果需要拼接多次，应使用 strings.Builder，最小化内存拷贝次数。

```go
var str strings.Builder
for i := 0; i < 1000; i++ {
    str.WriteString("a")
}
fmt.Println(str.String())
```

### 什么是 rune 类型

ASCII 码只需要 7 bit 就可以完整地表示，但只能表示英文字母在内的128个字符，为了表示世界上大部分的文字系统，发明了 Unicode， 它是ASCII的超集，包含世界上书写系统中存在的所有字符，并为每个代码分配一个标准编号（称为Unicode CodePoint），在 Go 语言中称之为 rune，是 int32 类型的别名。

Go 语言中，字符串的底层表示是 byte (8 bit) 序列，而非 rune (32 bit) 序列。例如下面的例子中 语 和 言 使用 UTF-8 编码后各占 3 个 byte，因此 len("Go语言") 等于 8，当然我们也可以将字符串转换为 rune 序列。

```go
fmt.Println(len("Go语言")) // 8
fmt.Println(len([]rune("Go语言"))) // 4
```

### 如何判断 map 中是否包含某个 key ？

```go
if val, ok := dict["foo"]; ok {
    //do something here
}
```

dict["foo"] 有 2 个返回值，val 和 ok，如果 ok 等于 true，则说明 dict 包含 key "foo"，val 将被赋予 "foo" 对应的值。

### Go 支持默认参数或可选参数吗？

Go 语言不支持可选参数（python 支持），也不支持方法重载（java支持）。

### defer 的执行顺序

多个 defer 语句，遵从后进先出(Last In First Out，LIFO)的原则，最后声明的 defer 语句，最先得到执行。

defer 在 return 语句之后执行，但在函数退出之前，defer 可以修改返回值。

```go
func test() int {
    i := 0
    defer func() {
        fmt.Println("defer1")
    }()
    defer func() {
        i += 1
        fmt.Println("defer2")
    }()
    return i
}

func main() {
    fmt.Println("return", test())
}
// defer2
// defer1
// return 0
```

这个例子中，可以看到 defer 的执行顺序：后进先出。但是返回值并没有被修改，这是由于 Go 的返回机制决定的，执行 return 语句后，Go 会创建一个临时变量保存返回值，因此，defer 语句修改了局部变量 i，并没有修改返回值。那如果是有名的返回值呢？

```go
func test() (i int) {
    i = 0
    defer func() {
        i += 1
        fmt.Println("defer2")
    }()
    return i
}

func main() {
    fmt.Println("return", test())
}
// defer2
// return 1
```

这个例子中，返回值被修改了。对于有名返回值的函数，执行 return 语句时，并不会再创建临时变量保存，因此，defer 语句修改了 i，即对返回值产生了影响。

### 如何交换 2 个变量的值？

```go
a, b := "A", "B"
a, b = b, a
fmt.Println(a, b) // B A
```

### Go 语言 tag 的用处？

tag 可以理解为 struct 字段的注解，可以用来定义字段的一个或多个属性。框架/工具可以通过反射获取到某个字段定义的属性，采取相应的处理方式。tag 丰富了代码的语义，增强了灵活性。

```go
package main

import "fmt"
import "encoding/json"

type Stu struct {
    Name string `json:"stu_name"`
    ID   string `json:"stu_id"`
    Age  int    `json:"-"`
}

func main() {
    buf, _ := json.Marshal(Stu{"Tom", "t001", 18})
    fmt.Printf("%s\n", buf)
}
```

这个例子使用 tag 定义了结构体字段与 json 字段的转换关系，Name -> stu_name, ID -> stu_id，忽略 Age 字段。很方便地实现了 Go 结构体与不同规范的 json 文本之间的转换。

### 如何判断 2 个字符串切片（slice) 是相等的？

go 语言中可以使用反射 reflect.DeepEqual(a, b) 判断 a、b 两个切片是否相等，但是通常不推荐这么做，使用反射非常影响性能。

通常采用的方式如下，遍历比较切片中的每一个元素（注意处理越界的情况）。

```go
func StringSliceEqualBCE(a, b []string) bool {
    if len(a) != len(b) {
        return false
    }

    if (a == nil) != (b == nil) {
        return false
    }

    b = b[:len(a)]
    for i, v := range a {
        if v != b[i] {
            return false
        }
    }

    return true
}
```

### 字符串打印时，%v 和 %+v 的区别

%v 和 %+v 都可以用来打印 struct 的值，区别在于 %v 仅打印各个字段的值，%+v 还会打印各个字段的名称。

```go
type Stu struct {
    Name string
}

func main() {
    fmt.Printf("%v\n", Stu{"Tom"}) // {Tom}
    fmt.Printf("%+v\n", Stu{"Tom"}) // {Name:Tom}
}
```

### 语言中如何表示枚举值(enums)

通常使用常量(const) 来表示枚举值。

```go
type StuType int32

const (
    Type1 StuType = iota
    Type2
    Type3
    Type4
)

func main() {
    fmt.Println(Type1, Type2, Type3, Type4) // 0, 1, 2, 3
}
```

### 空 struct{} 的用途

使用空结构体 struct{} 可以节省内存，一般作为占位符使用，表明这里并不需要一个值。

```go
fmt.Println(unsafe.Sizeof(struct{}{})) // 0
```

比如使用 map 表示集合时，只关注 key，value 可以使用 struct{} 作为占位符。如果使用其他类型作为占位符，例如 int，bool，不仅浪费了内存，而且容易引起歧义。

```go
type Set map[string]struct{}

func main() {
    set := make(Set)

    for _, item := range []string{"A", "A", "B", "C"} {
        set[item] = struct{}{}
    }
    fmt.Println(len(set)) // 3
    if _, ok := set["A"]; ok {
        fmt.Println("A exists") // A exists
    }
}
```

再比如，使用信道(channel)控制并发时，我们只是需要一个信号，但并不需要传递值，这个时候，也可以使用 struct{} 代替。

```go
func main() {
    ch := make(chan struct{}, 1)
    go func() {
        <-ch
        // do something
    }()
    ch <- struct{}{}
    // ...
}
```

再比如，声明只包含方法的结构体。

```go
type Lamp struct{}

func (l Lamp) On() {
        println("On")

}
func (l Lamp) Off() {
        println("Off")
}
```

### 通道关闭后是否可以继续使用通道

```go
close(ch)
```

不可以发送，会触发 panic

可以接收，接收剩余数据

### select可以用于什么

golang 的 select 就是监听 IO 操作，当 IO 操作发生时，触发相应的动作

每个case语句里必须是一个IO操作，确切的说，应该是一个面向channel的IO操作

### context包的用途

Context 包提供上下文机制在 goroutine 之间传递 deadline、取消信号（cancellation signals）或者其他请求相关的信息。

## 实现原理

### 主协程如何等其余协程完再操作

使用channel进行通信实现同步，context,select

### init() 函数是什么时候执行的？

init() 函数是 Go 程序初始化的一部分。Go 程序初始化先于 main 函数，由 runtime 初始化每个导入的包，初始化顺序不是按照从上到下的导入顺序，而是按照解析的依赖关系，没有依赖的包最先初始化。

每个包首先初始化包作用域的常量和变量（常量优先于变量），然后执行包的 init() 函数。同一个包，甚至是同一个源文件可以有多个 init() 函数。init() 函数没有入参和返回值，不能被其他函数调用，同一个包内多个 init() 函数的执行顺序不作保证。

一句话总结： import –> const –> var –> init() –> main()

示例：

```go
package main

import "fmt"

func init()  {
    fmt.Println("init1:", a)
}

func init()  {
    fmt.Println("init2:", a)
}

var a = 10
const b = 100

func main() {
    fmt.Println("main:", a)
}
// 执行结果
// init1: 10
// init2: 10
// main: 10
```

### Go 语言的局部变量分配在栈上还是堆上？

由编译器决定。Go 语言编译器会自动决定把一个变量放在栈还是放在堆，编译器会做逃逸分析(escape analysis)，当发现变量的作用域没有超出函数范围，就可以在栈上，反之则必须分配在堆上。

```go
// 栈
func foo() *int {
    v := 11
    return &v
}

// 堆
func main() {
    m := foo()
    println(*m) // 11
}
```

foo() 函数中，如果 v 分配在栈上，foo 函数返回时，&v 就不存在了，但是这段函数是能够正常运行的。Go 编译器发现 v 的引用脱离了 foo 的作用域，会将其分配在堆上。因此，main 函数中仍能够正常访问该值。

### 2 个 interface 可以比较吗？

Go 语言中，interface 的内部实现包含了 2 个字段，类型 T 和 值 V，interface 可以使用 == 或 != 比较。2 个 interface 相等有以下 2 种情况

1. 两个 interface 均等于 nil（此时 V 和 T 都处于 unset 状态）
2. 类型 T 相同，且对应的值 V 相等。

```go
type Stu struct {
    Name string
}

type StuInt interface{}

func main() {
    var stu1, stu2 StuInt = &Stu{"Tom"}, &Stu{"Tom"}
    var stu3, stu4 StuInt = Stu{"Tom"}, Stu{"Tom"}
    fmt.Println(stu1 == stu2) // false
    fmt.Println(stu3 == stu4) // true
}
```

stu1 和 stu2 对应的类型是 *Stu，值是 Stu 结构体的地址，两个地址不同，因此结果为 false。

stu3 和 stu4 对应的类型是 Stu，值是 Stu 结构体，且各字段相等，因此结果为 true。

### 两个 nil 可能不相等吗？

可能。

接口(interface) 是对非接口值(例如指针，struct等)的封装，内部实现包含 2 个字段，类型 T 和 值 V。一个接口等于 nil，当且仅当 T 和 V 处于 unset 状态（T=nil，V is unset）。

两个接口值比较时，会先比较 T，再比较 V。

接口值与非接口值比较时，会先将非接口值尝试转换为接口值，再比较。

```go
func main() {
    var p *int = nil
    var i interface{} = p
    fmt.Println(i == p) // true
    fmt.Println(p == nil) // true
    fmt.Println(i == nil) // false
}
```

上面这个例子中，将一个 nil 非接口值 p 赋值给接口 i，此时，i 的内部字段为(T=*int, V=nil)，i 与 p 作比较时，将 p 转换为接口后再比较，因此 i == p，p 与 nil 比较，直接比较值，所以 p == nil。

但是当 i 与 nil 比较时，会将 nil 转换为接口 (T=nil, V=nil)，与i (T=*int, V=nil) 不相等，因此 i != nil。因此 V 为 nil ，但 T 不为 nil 的接口不等于 nil。

### 简述 Go 语言GC(垃圾回收)的工作原理

最常见的垃圾回收算法有标记清除(Mark-Sweep) 和引用计数(Reference Count)，Go 语言采用的是标记清除算法。并在此基础上使用了三色标记法和写屏障技术，提高了效率。

标记清除收集器是跟踪式垃圾收集器，其执行过程可以分成标记（Mark）和清除（Sweep）两个阶段：

- 标记阶段 — 从根对象出发查找并标记堆中所有存活的对象；
- 清除阶段 — 遍历堆中的全部对象，回收未被标记的垃圾对象并将回收的内存加入空闲链表。

标记清除算法的一大问题是在标记期间，需要暂停程序（Stop the world，STW），标记结束之后，用户程序才可以继续执行。为了能够异步执行，减少 STW 的时间，Go 语言采用了三色标记法。

三色标记算法将程序中的对象分成白色、黑色和灰色三类。

- 白色：不确定对象。
- 灰色：存活对象，子对象待处理。
- 黑色：存活对象。

标记开始时，所有对象加入白色集合（这一步需 STW ）。首先将根对象标记为灰色，加入灰色集合，垃圾搜集器取出一个灰色对象，将其标记为黑色，并将其指向的对象标记为灰色，加入灰色集合。重复这个过程，直到灰色集合为空为止，标记阶段结束。那么白色对象即可需要清理的对象，而黑色对象均为根可达的对象，不能被清理。

三色标记法因为多了一个白色的状态来存放不确定对象，所以后续的标记阶段可以并发地执行。当然并发执行的代价是可能会造成一些遗漏，因为那些早先被标记为黑色的对象可能目前已经是不可达的了。所以三色标记法是一个 false negative（假阴性）的算法。

三色标记法并发执行仍存在一个问题，即在 GC 过程中，对象指针发生了改变。比如下面的例子：

```
A (黑) -> B (灰) -> C (白) -> D (白)
```

正常情况下，D 对象最终会被标记为黑色，不应被回收。但在标记和用户程序并发执行过程中，用户程序删除了 C 对 D 的引用，而 A 获得了 D 的引用。标记继续进行，D 就没有机会被标记为黑色了（A 已经处理过，这一轮不会再被处理）。

```
A (黑) -> B (灰) -> C (白) 
  ↓
 D (白)
```

为了解决这个问题，Go 使用了内存屏障技术，它是在用户程序读取对象、创建新对象以及更新对象指针时执行的一段代码，类似于一个钩子。垃圾收集器使用了写屏障（Write Barrier）技术，当对象新增或更新时，会将其着色为灰色。这样即使与用户程序并发执行，对象的引用发生改变时，垃圾收集器也能正确处理了。

一次完整的 GC 分为四个阶段：

1. 标记准备(Mark Setup，需 STW)，打开写屏障(Write Barrier)
2. 使用三色标记法标记（Marking, 并发）
3. 标记结束(Mark Termination，需 STW)，关闭写屏障。
4. 清理(Sweeping, 并发)

### 函数返回局部变量的指针是否安全?

这在 Go 中是安全的，Go 编译器将会对每个局部变量进行逃逸分析。如果发现局部变量的作用域超出该函数，则不会将内存分配在栈上，而是分配在堆上。

### 非接口非接口的任意类型 T() 都能够调用 *T 的方法吗？反过来呢?

- 一个T类型的值可以调用为_T类型声明的方法，但是仅当此T的值是可寻址(addressable) 的情况下。编译器在调用指针属主方法前，会自动取此T值的地址。因为不是任何T值都是可寻址的，所以并非任何T值都能够调用为类型_T声明的方法。
- 反过来，一个_T类型的值可以调用为类型T声明的方法，这是因为解引用指针总是合法的。事实上，你可以认为对于每一个为类型 T 声明的方法，编译器都会为类型_T自动隐式声明一个同名和同签名的方法。

哪些值是不可寻址的呢？
- 字符串中的字节；
- map 对象中的元素（slice 对象中的元素是可寻址的，slice的底层是数组）；
- 常量；
- 包级别的函数等。

举一个例子，定义类型 T，并为类型 *T 声明一个方法 hello()，变量 t1 可以调用该方法，但是常量 t2 调用该方法时，会产生编译错误。

```go
type T string

func (t *T) hello() {
    fmt.Println("hello")
}

func main() {
    var t1 T = "ABC"
    t1.hello() // hello
    const t2 T = "ABC"
    t2.hello() // error: cannot call pointer method on t
}
```
