## 协程goruntine

### 基本概念

> 在 Go 中，应用程序并发处理的部分被称作goroutines（协程），它可以进行更有效的并发运算。在协程和操作系统线程之间并无一对一的关系：协程是根据一个或多个线程的可用性，映射（多路复用，执行于）在他们之上的；

协程调度器在 Go 运行时很好的完成了这个工作。


### 语法

在语法上，go语句是一个普通的函数或方法调用前加上关键字go。go语句会使其语句中的函数在一个新创建的goroutine中运行。

```go
f()    // call f(); wait for it to return
go f() // create a new goroutine that calls f(); don't wait
```

### 进程、线程和协程的区别

#### 概念

1. 进程

> 进程是具有一定独立功能的程序关于某个数据集合上的一次运行活动,进程是系统进行资源分配和调度的一个独立单位。每个

进程都有自己的独立内存空间，不同进程通过进程间通信来通信。由于进程比较重量，占据独立的内存，所以上下文进程间的切换开销（栈、寄存器、虚拟内存、文件句柄等）比较大，但相对比较稳定安全。


2. 线程

> 线程是进程的一个实体,是CPU调度和分派的基本单位,它是比进程更小的能独立运行的基本单位.线程自己基本上不拥有系统资源,

只拥有一点在运行中必不可少的资源(如程序计数器,一组寄存器和栈),但是它可与同属一个进程的其他的线程共享进程所拥有的全部资源。线程间通信主要通过共享内存，上下文切换很快，资源开销较少，但相比进程不够稳定容易丢失数据。


3. 协程

> 协程是一种用户态的轻量级线程，协程的调度完全由用户控制。协程拥有自己的寄存器上下文和栈。协程调度切换时，将寄存器

上下文和栈保存到其他地方，在切回来的时候，恢复先前保存的寄存器上下文和栈，直接操作栈则基本没有内核切换的开销，可以不加锁的访问全局变量，所以上下文的切换非常快。


#### 区别

1. 进程多与线程比较

线程是指进程内的一个执行单元,也是进程内的可调度实体。线程与进程的区别:

```
1)地址空间:线程是进程内的一个执行单元，进程内至少有一个线程，它们共享进程的址空间，而进程有自己独立的地址空间
2)资源拥有:进程是资源分配和拥有的单位,同一个进程内的线程共享进程的资源
3)线程是处理器调度的基本单位,但进程不是
4)二者均可并发执行
5)每个独立的线程有一个程序运行的入口、顺序执行序列和程序的出口，但是线程不能够独立执行，必须依存在应用程序中，由应用程序提供多个线程执行控制
```

2. 协程多与线程进行比较

```
1)一个线程可以多个协程，一个进程也可以单独拥有多个协程
2)线程进程都是同步机制，而协程则是异步
3)协程能保留上一次调用时的状态，每次过程重入时，就相当于进入上一次调用的状态
```

### 并发和并行

**并发：** 在操作系统中，某一时间段，几个程序在同一个CPU上运行，但在任意一个时间点上，只有一个程序在CPU上运行。

当有多个线程时，如果系统只有一个CPU，那么CPU不可能真正同时进行多个线程，CPU的运行时间会被划分成若干个时间段，每个时间段分配给各个线程去执行，一个时间段里某个线程运行时，其他线程处于挂起状态，这就是并发。并发解决了程序排队等待的问题，如果一个程序发生阻塞，其他程序仍然可以正常执行。

**并行：** 当操作系统有多个CPU时，一个CPU处理A线程，另一个CPU处理B线程，两个线程互相不抢占CPU资源，可以同时进行，这种方式成为并行。

## 通道channel

### 基本概念

> Go 有一个特殊的类型，通道（channel），像是通道（管道），可以通过它们发送类型化的数据在协程之间通信，可以避开所有内存共享导致的坑；通道的通信方式保证了同步性。数据通过通道：同一时间只有一个协程可以访问数据：所以不会出现数据竞争，设计如此。数据的归属（可以读写数据的能力）被传递。


### 语法

使用内置的make函数，可以创建一个channel：

```go
ch := make(chan int) // ch has type 'chan int'
```

一个channel有发送和接受两个主要操作，都是通信行为。一个发送语句将一个值从一个goroutine通过channel发送到另一个执行接收操作的goroutine。发送和接收两个操作都是用`<-`运算符。在发送语句中，`<-`运算符分割channel和要发送的值。在接收语句中，`<`-运算符写在channel对象之前。一个不使用接收结果的接收操作也是合法的。

```go
ch <- x  // a send statement
x = <-ch // a receive expression in an assignment statement
<-ch     // a receive statement; result is discarded
```

使用内置的close函数就可以关闭一个channel：

```go
close(ch)
```

以最简单方式调用make函数创建的是一个无缓冲的channel，但是也可以指定第二个整形参数，对应channel的容量。如果channel的容量大于零，那么该channel就是带缓冲的channel。

```go
ch = make(chan int)    // unbuffered channel
ch = make(chan int, 0) // unbuffered channel
ch = make(chan int, 3) // buffered channel with capacity 3
```

## 基于select的多路复用

语法

```go
select {
case <-ch1:
    // ...
case x := <-ch2:
    // ...use x...
case ch3 <- y:
    // ...
default:
    // ...
}
```

default 语句是可选的；fallthrough 行为，和普通的 switch 相似，是不允许的。在任何一个 case 中执行 break 或者 return，select 就结束了。

select 做的就是：选择处理列出的多个通信情况中的一个。

- 如果都阻塞了，会等待直到其中一个可以处理
- 如果多个可以处理，随机选择一个
- 如果没有通道操作可以处理并且写了 default 语句，它就会执行：default 永远是可运行的（这就是准备好了，可以执行）。

在 select 中使用发送操作并且有 default 可以确保发送不被阻塞！如果没有 case，select 就会一直阻塞。

select 语句实现了一种监听模式，通常用在（无限）循环中；在某种情况下，通过 break 语句使循环退出。

## 锁 sync/atomic

### 互斥锁sync.Mutex

通过在代码前调用 Lock 方法，在代码后调用 Unlock 方法来保证一段代码的互斥执行

**方法：**

1. 加锁

```go
func (m *Mutex) Lock()
```

2. 解锁

```go
func (m *Mutex) Unlock()
```

### 读写锁sync.RWMutex

读写锁分为读锁和写锁，读锁是允许同时执行的，但写锁是互斥的。一般来说，有如下几种情况：

- 读锁之间不互斥，没有写锁的情况下，读锁是无阻塞的，多个协程可以同时获得读锁。
- 写锁之间是互斥的，存在写锁，其他写锁阻塞。
- 写锁与读锁是互斥的，如果存在读锁，写锁阻塞，如果存在写锁，读锁阻塞。

**方法：**

1. 加写锁

```go
func (rw *RWMutex) Lock()
```

2. 释放写锁

```go
func (rw *RWMutex) Unlock ()
```

3. 加读锁

```go
func (rw *RWMutex) RLock()
```

4. 释放读锁

```go
func (rw *RWMutex) RUnlock()
```

### sync.Once

sync.Once 是 Go 标准库提供的使函数只执行一次的实现，常应用于单例模式，例如初始化配置、保持数据库连接等

作用与 init 函数类似，但有区别。

- init 函数是当所在的 package 首次被加载时执行，若迟迟未被使用，则既浪费了内存，又延长了程序加载时间。
- sync.Once 可以在代码的任意位置初始化和调用，因此可以延迟到使用时再执行，并发场景下是线程安全的。

**示例：**

```go
package main

import (
    "fmt"
    "sync"
)

func main() {
    var once sync.Once
    onceBody := func() {
        fmt.Println("Only once")
    }
    done := make(chan bool)
    for i := 0; i < 10; i++ {
        go func() {
            once.Do(onceBody)
            done <- true
        }()
    }
    for i := 0; i < 10; i++ {
        <-done
    }
}
```

### sync.Pool

保存和复用临时对象，减少内存分配，降低 GC 压力

**使用：**

1. 声明对象池

```go
var studentPool = sync.Pool{
    New: func() interface{} { 
        return new(Student) 
    },
}
```

2. Get&Put

```go
stu := studentPool.Get().(*Student)
json.Unmarshal(buf, stu)
studentPool.Put(stu)
```

### sync.Cond

sync.Cond 条件变量用来协调想要访问共享资源的那些 goroutine，当共享资源的状态发生变化的时候，它可以用来通知被互斥锁阻塞的 goroutine。

**方法：**

1. NewCond 创建实例

```go
func NewCond(l Locker) *Cond
```

2. Broadcast 广播唤醒所有

```go
func (c *Cond) Broadcast()
```

3. Signal 唤醒一个协程

```go
func (c *Cond) Signal()
```

4. Wait 等待

```go
func (c *Cond) Wait()
```

**示例：**

```go
var done = false

func read(name string, c *sync.Cond) {
    c.L.Lock()
    for !done {
        c.Wait()
    }
    log.Println(name, "starts reading")
    c.L.Unlock()
}

func write(name string, c *sync.Cond) {
    log.Println(name, "starts writing")
    time.Sleep(time.Second)
    c.L.Lock()
    done = true
    c.L.Unlock()
    log.Println(name, "wakes all")
    c.Broadcast()
}

func main() {
    cond := sync.NewCond(&sync.Mutex{})

    go read("reader1", cond)
    go read("reader2", cond)
    go read("reader3", cond)
    write("writer", cond)

    time.Sleep(time.Second * 3)
}
```
