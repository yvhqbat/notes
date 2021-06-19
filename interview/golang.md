# Golang

[TOC]

其他：
- [Golang进阶面试题整理](https://blog.csdn.net/sinat_35406909/article/details/103818364)
- [golang面试题整理](https://studygolang.com/articles/17796)

### 1. Golang的GC

参考：[垃圾收集器 ](https://draveness.me/golang/docs/part3-runtime/ch07-memory/golang-garbage-collector/)

现在的编程语言通常会使用手动和自动两种方式管理内存，C、C++ 使用手动的方式管理内存，工程师需要主动申请或者释放内存；而 Python、Java 和 Go 等语言使用自动的内存管理系统，一般都是垃圾收集机制。

**标记清除（Mark-Sweep）算法**是最常见的垃圾收集算法，执行过程可以分成标记（Mark）和清除（Sweep）两个阶段：

1. 标记阶段 — 从根对象出发查找并标记堆中所有存活的对象；
2. 清除阶段 — 遍历堆中的全部对象，回收未被标记的垃圾对象并将回收的内存加入空闲链表；

整个过程需要标记对象的存活状态，用户程序在垃圾收集的过程中也不能执行，即**暂停程序（Stop the world，STW）**。

为了解决原始标记清除算法带来的长时间 STW，golang使用**三色标记算法的变种**以缩短 STW 的时间。三色标记算法将程序中的对象分成白色、黑色和灰色三类：

- 白色对象 — 潜在的垃圾，其内存可能会被垃圾收集器回收；
- 黑色对象 — 活跃的对象，包括不存在任何引用外部指针的对象以及从根对象可达的对象；
- 灰色对象 — 活跃的对象，因为存在指向白色对象的外部指针，垃圾收集器会扫描这些对象的子对象；

三色标记算法的工作原理很简单，可以归纳成以下几个步骤：

1. 从灰色对象的集合中选择一个灰色对象并将其标记成黑色；
2. 将黑色对象指向的所有对象都标记成灰色，保证该对象和被该对象引用的对象都不会被回收；
3. 重复上述两个步骤直到对象图中不存在灰色对象；

因为用户程序可能在标记执行的过程中修改对象的指针，所以三色标记清除算法本身是不可以并发或者增量执行的，它仍然需要 STW。

想要在并发或者增量的三色标记算法中保证正确性，我们需要达成以下两种三色不变性（Tri-color invariant）中的一种：

- 强三色不变性 — 黑色对象不会指向白色对象，只会指向灰色对象或者黑色对象；
- 弱三色不变性 — 黑色对象指向的白色对象必须包含一条从灰色对象经由多个白色对象的可达路径；

遵循上述两个不变性中的任意一个，我们都能保证垃圾收集算法的正确性，而**屏障技术**就是在并发或者增量标记过程中保证三色不变性的重要技术。



传统的垃圾收集算法会在垃圾收集的执行期间暂停应用程序，而追求实时的应用程序无法接受长时间的 STW。

为了减少STW时间，我们会使用下面的策略优化现代的垃圾收集器：

- 增量垃圾收集 — 增量地标记和清除垃圾，降低应用程序暂停的最长时间；
- 并发垃圾收集 — 利用多核的计算资源，在用户程序执行时并发标记和清除垃圾；



GC触发时机：

- [`runtime.sysmon`](https://draveness.me/golang/tree/runtime.sysmon) 和 [`runtime.forcegchelper`](https://draveness.me/golang/tree/runtime.forcegchelper) — 后台运行定时检查和垃圾收集；
- [`runtime.GC`](https://draveness.me/golang/tree/runtime.GC) — 用户程序手动触发垃圾收集；
- [`runtime.mallocgc`](https://draveness.me/golang/tree/runtime.mallocgc) — 申请内存时根据堆大小触发垃圾收集；



### 2. go的调度模型：GPM调度（go为什么并发高？）

Go 语言在并发编程方面有强大的能力，这离不开语言层面对并发编程的支持。

进程、线程和Goroutine

进程是资源分配的基本单位，线程是操作系统调度时的最基本单元。

多个线程可以属于同一个进程并共享内存空间。因为多线程不需要创建新的虚拟内存空间，所以它们也不需要内存管理单元处理上下文的切换，线程之间的通信也正是基于共享的内存进行的，与重量级的进程相比，线程显得比较轻量。

虽然线程比较轻量，但是在调度时也有比较大的额外开销。每个线程会都占用 1M 以上的内存空间，在切换线程时不止会消耗较多的内存，恢复寄存器中的内容还需要向操作系统申请或者销毁资源，每一次线程上下文的切换都需要消耗 ~1us 左右的时间，但是 Go 调度器对 Goroutine 的上下文切换约为 ~0.2us，减少了 80% 的额外开销。

Go 语言的调度器通过使用与 CPU 数量相等的线程减少线程频繁切换的内存开销，同时在每一个线程上执行额外开销更低的 Goroutine 来降低操作系统和硬件的负载。



1. G — 表示 Goroutine，它是一个待执行的任务；
2. M — 表示操作系统的线程，它由操作系统的调度器调度和管理；
3. P — 表示处理器，它可以被看做运行在线程上的本地调度器；

Goroutine 是 Go 语言调度器中待执行的任务，它在运行时调度器中的地位与线程在操作系统中差不多，但是它占用了更小的内存空间，也降低了上下文切换的开销。Goroutine 只存在于 Go 语言的运行时，它是 Go 语言在用户态提供的线程，作为一种粒度更细的资源调度单元，如果使用得当能够在高并发的场景下更高效地利用机器的 CPU。



调度器最多可以创建 10000 个线程，但是其中大多数的线程都不会执行用户代码（可能陷入系统调用），最多只会有 `GOMAXPROCS` 个活跃线程能够正常运行。在默认情况下，运行时会将 `GOMAXPROCS` 设置成当前机器的核数，我们也可以在程序中使用 [`runtime.GOMAXPROCS`](https://draveness.me/golang/tree/runtime.GOMAXPROCS) 来改变最大的活跃线程数。



调度器中的处理器 P 是线程和 Goroutine 的中间层，它能提供线程需要的上下文环境，也会负责调度线程上的等待队列，通过处理器 P 的调度，每一个内核线程都能够执行多个 Goroutine，它能在 Goroutine 进行一些 I/O 操作时及时让出计算资源，提高线程的利用率。因为调度器在启动时就会创建 `GOMAXPROCS` 个处理器，所以 Go 语言程序的处理器数量一定会等于 `GOMAXPROCS`，这些处理器会绑定到不同的内核线程上。



### 3. Channel底层原理

Channel作为 Go 核心的数据结构和 Goroutine 之间的通信方式，Channel 是支撑 Go 语言高性能并发编程模型的重要结构。

Go 语言中最常见的、也是经常被人提及的设计模式就是：不要通过共享内存的方式进行通信，而是应该通过通信的方式共享内存。

带缓冲区和不带缓冲区的 Channel 都会遵循先入先出发送和接收数据。



使用 `ch <- i` 表达式向 Channel 发送数据时遇到的几种情况：

1. 如果当前 Channel 的 `recvq` 上存在已经被阻塞的 Goroutine，那么会直接将数据发送给当前 Goroutine 并将其设置成下一个运行的 Goroutine；
2. 如果 Channel 存在缓冲区并且其中还有空闲的容量，我们会直接将数据存储到缓冲区 `sendx` 所在的位置上；
3. 如果不满足上面的两种情况，会创建一个 `runtime.sudog` 结构并将其加入 Channel 的 `sendq` 队列中，当前 Goroutine 也会陷入阻塞等待其他的协程从 Channel 接收数据；



从 Channel 中接收数据时可能会发生的五种情况：

1. 如果 Channel 为空，那么会直接调用 `runtime.gopark` 挂起当前 Goroutine；
2. 如果 Channel 已经关闭并且缓冲区没有任何数据，`runtime.chanrecv` 会直接返回；
3. 如果 Channel 的 `sendq` 队列中存在挂起的 Goroutine，会将 `recvx` 索引所在的数据拷贝到接收变量所在的内存空间上并将 `sendq` 队列中 Goroutine 的数据拷贝到缓冲区；
4. 如果 Channel 的缓冲区中包含数据，那么直接读取 `recvx` 索引对应的数据；
5. 在默认情况下会挂起当前的 Goroutine，将 `runtime.sudog`结构加入 `recvq` 队列并陷入休眠等待调度器的唤醒；



### 4. 异常错误处理panic

 Go 语言中成对出现的两个关键字 — `panic` 和 `recover`。这两个关键字与 `defer` 有紧密的联系。

- `panic` 能够改变程序的控制流，调用 `panic` 后会立刻停止执行当前函数的剩余代码，并在当前 Goroutine 中递归执行调用方的 `defer`；
- `recover` 可以终止 `panic` 造成的程序崩溃。它是一个只能在 `defer` 中发挥作用的函数，在其他作用域中调用不会发挥作用；



- `panic` 只会触发当前 Goroutine 的 `defer`；
- `recover` 只有在 `defer` 中调用才会生效；
- `panic` 允许在 `defer` 中嵌套多次调用；

```go
func main() {
	defer fmt.Println("in main")
	defer func() {
		defer func() {
			panic("panic again and again")
		}()
		panic("panic again")
	}()

	panic("panic once")
}

$ go run main.go
in main
panic: panic once
	panic: panic again
	panic: panic again and again

goroutine 1 [running]:
...
exit status 2

```

我们可以确定程序多次调用 `panic` 也不会影响 `defer` 函数的正常执行，所以使用 `defer` 进行收尾工作一般来说都是安全的。

### 5. defer recover

Go 语言的 `defer` 会在当前函数返回前执行传入的函数，它会经常被用于关闭文件描述符、关闭数据库连接以及解锁资源。

- `defer` 关键字的调用时机以及多次调用 `defer` 时执行顺序是如何确定的；
- `defer` 关键字使用传值的方式传递参数时会进行预计算，导致不符合预期的结果；



小结：

- 后调用的 `defer` 函数会先执行：
  - 后调用的 `defer` 函数会被追加到 Goroutine `_defer` 链表的最前面；
  - 运行 `runtime._defer` 时是从前到后依次执行；
- 函数的参数会被预先计算；
  - 调用 `runtime.deferproc` 函数创建新的延迟调用时就会立刻拷贝函数的参数，函数的参数不会等到真正执行时计算；



### 6. 各种数据结构的介绍



### 7. 跟踪GC的工具

- [GC 追踪](https://studygolang.com/articles/21570)



### 8. 内存管理

### 9. C++和go对比

### 10. goroutine泄漏怎么处理？

参考：[goroutine泄露：原理、场景、检测和防范](https://segmentfault.com/a/1190000019644257)

### 11. make 和 new 的区别？
Go语言中的内建函数new和make是两个用于内存分配的原语（allocation primitives），其功能相似，却有本质区别。
- 内建函数 new 用来分配内存，它的第一个参数是一个类型，不是一个值，它的返回值是一个指向新分配类型零值的指针
- 内建函数 make 用来为 slice，map 或 chan 类型分配内存和初始化一个对象(注意：只能用在这三种类型上)，跟 new 类似，第一个参数也是一个类型而不是一个值，跟 new 不同的是，make 返回类型的引用而不是指针，而返回值也依赖于具体传入的类型；

为什么slice、map和channel要由make创建呢？  
三个类型的背后都引用了使用前必须初始化的数据结构，而只有make 用于可以初始化其内部的数据结构并准备好将要使用的值。

参考： [golang 内置函数new()和make()的区别](https://blog.csdn.net/MaxCoderLlj/article/details/80218025)

### 12. c++的map和go的map的区别？
红黑树和哈希表
[《go语言设计与实现》：哈希表](https://draveness.me/golang/docs/part2-foundation/ch03-datastructure/golang-hashmap/)

### 13. ctx包了解吗？有什么用？
[深入理解Golang之context](https://juejin.cn/post/6844904070667321357)
[《go语言设计与实现》：context](https://draveness.me/golang/docs/part3-runtime/ch06-concurrency/golang-context/)

在 Goroutine 构成的树形结构中对信号进行同步以减少计算资源的浪费是 context.Context 的最大作用。Go 服务的每一个请求都是通过单独的 Goroutine 处理的2，HTTP/RPC 请求的处理器会启动新的 Goroutine 访问数据库和其他服务。

我们可能会创建多个 Goroutine 来处理一次请求，而 context.Context 的作用是在不同 Goroutine 之间同步请求特定数据、取消信号以及处理请求的截止日期。

### 14. go什么情况下会发生内存泄漏？
ctx没有cancel时

参考：[goroutine泄露：原理、场景、检测和防范](https://segmentfault.com/a/1190000019644257)


### 15. 怎么实现协程完美退出？
[goroutine退出方式的总结](https://blog.csdn.net/xingwangc2014/article/details/78998727)

### 16. goroutine 和 channel 如何实现？

### 17. go的锁如何实现？用了什么cpu指令？
[《Go专家编程》GO 互斥锁实现原理剖析](https://my.oschina.net/renhc/blog/2876211)

### 18. go怎么从源码编译到二进制文件？
[《go语言设计与实现》：编译过程](https://draveness.me/golang/docs/part1-prerequisite/ch02-compile/golang-compile-intro/)

### 19. c++的模板和go的interface的区别？

### 20. 怎么理解go的interface?
[《go语言设计与实现》：接口](https://draveness.me/golang/docs/part2-foundation/ch04-basic/golang-interface/)

Go 语言中的接口是一组方法的签名，它是 Go 语言的重要组成部分。

方法接受者：结构体和指针

多态


### 21. 用 channel 实现定时器？
两个channel同步

### 22. golang的runtime如何实现？
参考：
- [万字长文带你深入浅出 Golang Runtime](https://blog.csdn.net/Tencent_TEG/article/details/103379318)

### 23. 看过sql的连接池实现吗？



### 24. go struct 是否可比较？

参考： [go语言的比较运算](https://studygolang.com/articles/11342)



### 25. go 单例模式

参考： [golang单例模式](https://studygolang.com/articles/11444)

```golang
package main

import(
	"fmt"
	"sync"
)

type Singleton struct{

}

var single *Singleton
var once sync.Once

func GetInstance()*Singleton{
	// if single == nil{
	// 	single = &Singleton{}
	// }
	once.Do(func(){
		single = &Singleton{}
	})
	return single
}

func main(){
	s := GetInstance()
	if s==nil{
		fmt.Println("error")
	}else{
		fmt.Println("get instance success.")
	}
}
```

### 26. golang 如何实现面向对象？

### 27. slice 和 数组的区别？
1. 数组

数组是由相同类型元素的集合组成的数据结构，计算机会为数组分配一块连续的内存来保存其中的元素，我们可以利用数组中元素的索引快速访问特定元素.

Go 语言数组在初始化之后大小就无法改变，存储元素类型相同、但是大小不同的数组类型在 Go 语言看来也是完全不同的，只有两个条件都相同才是同一类型。
Go 语言的数组有两种不同的创建方式，一种是显式的指定数组大小，另一种是使用 [...]T 声明数组，Go 语言会在编译期间通过源代码推导数组的大小：
```golang
arr1 := [3]int{1, 2, 3}
arr2 := [...]int{1, 2, 3}
```
上述两种声明方式在运行期间得到的结果是完全相同的，后一种声明方式在编译期间就会被转换成前一种，这也就是编译器对数组大小的推导。

2. 切片

数组在 Go 语言中没那么常用，更常用的数据结构是切片，即动态数组，其长度并不固定，我们可以向切片中追加元素，它会在容量不足时自动扩容。

在 Go 语言中，切片类型的声明方式与数组有一些相似，不过由于切片的长度是动态的，所以声明时只需要指定切片中的元素类型：
```golang
[]int
[]interface{}
```

切片引入了一个抽象层，提供了对数组中部分连续片段的引用，而作为数组的引用，我们可以在运行区间可以修改它的长度和范围。当切片底层的数组长度不足时就会触发扩容.

扩容：
- 如果期望容量大于当前容量的两倍就会使用期望容量；
- 如果当前切片的长度小于 1024 就会将容量翻倍；
- 如果当前切片的长度大于 1024 就会每次增加 25% 的容量，直到新容量大于期望容量；

### 28. defer return 执行顺序
参考： [Go ---- defer 和 return 执行的先后顺序](https://www.cnblogs.com/saryli/p/11371912.html)

- 多个defer的执行顺序为“后进先出”；
- defer、return、返回值三者的执行逻辑应该是：return最先执行，return负责将结果写入返回值中；接着defer开始执行一些收尾工作；最后函数携带当前返回值退出。



### 29. sync.Map 底层实现
参考： [Go 1.9 sync.Map揭秘](https://studygolang.com/articles/10345)