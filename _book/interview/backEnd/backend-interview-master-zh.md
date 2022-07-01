# Go语言的GPM调度器是什么？

相信很多人都听说过Go语言天然支持高并发，原因是内部有协程（goroutine）加持，可以在一个进程中启动成千上万个协程。那么，它凭什么做到如此高的并发呢？那就需要先了解什么是并发模型。


## 并发模型

著名的C++专家Herb Sutter曾经说过“免费的午餐已经终结”。为了让代码运行的更快，单纯依靠更快的硬件已经无法得到满足，我们需要利用多核来挖掘并行的价值，而并发模型的目的就是来告诉你不同执行实体之间是如何协作的。

当然，不同的并发模型的协作方式也不尽相同，常见的并发模型有七种：

- 线程与锁
- 函数式编程
- Clojure之道
- actor
- 通讯顺序进程（CSP）
- 数据级并行
- Lambda架构

而今天，我们只讲与Go语言相关的并发模型CSP，感兴趣的同学可以自行查阅书籍《七周七并发模型》。


### CSP篇

CSP，全称Communicating Sequential Processes，意为通讯顺序进程，它是七大并发模型中的一种，它的核心观念是将两个并发执行的实体通过通道channel连接起来，所有的消息都通过channel传输。其实CSP概念早在1978年就被东尼·霍尔提出，由于近来Go语言的兴起，CSP又火了起来。
那么CSP与Go语言有什么关系呢？接下来我们来看Go语言对CSP并发模型的实现——GPM调度模型。


### GPM调度模型

GPM代表了三个角色，分别是Goroutine、Processor、Machine。

![](../images/17188139512ef9a8.jpg)

- Goroutine：就是咱们常用的用go关键字创建的执行体，它对应一个结构体g，结构体里保存了goroutine的堆栈信息
- Machine：表示操作系统的线程
- Processor：表示处理器，有了它才能建立G、M的联系

### Goroutine

Goroutine就是代码中使用go关键词创建的执行单元，也是大家熟知的有“轻量级线程”之称的协程，协程是不为操作系统所知的，它由编程语言层面实现，上下文切换不需要经过内核态，再加上协程占用的内存空间极小，所以有着非常大的发展潜力。

```go
go func() {}()
```

复制代码在Go语言中，Goroutine由一个名为runtime.go的结构体表示，该结构体非常复杂，有40多个成员变量，主要存储执行栈、状态、当前占用的线程、调度相关的数据。还有玩大家很想获取的goroutine标识，但是很抱歉，官方考虑到Go语言的发展，设置成私有了，不给你调用😏。

```go
type g struct {
	stack struct {
		lo uintptr
		hi uintptr
	} 							// 栈内存：[stack.lo, stack.hi)
	stackguard0	uintptr
	stackguard1 uintptr

	_panic       *_panic
	_defer       *_defer
	m            *m				// 当前的 m
	sched        gobuf
	stktopsp     uintptr		// 期望 sp 位于栈顶，用于回溯检查
	param        unsafe.Pointer // wakeup 唤醒时候传递的参数
	atomicstatus uint32
	goid         int64
	preempt      bool       	// 抢占信号，stackguard0 = stackpreempt 的副本
	timer        *timer         // 为 time.Sleep 缓存的计时器

	...
}
```
Goroutine调度相关的数据存储在sched，在协程切换、恢复上下文的时候用到。

```go
type gobuf struct {
	sp   uintptr
	pc   uintptr
	g    guintptr
	ret  sys.Uintreg
	...
}
```

M就是对应操作系统的线程，最多会有GOMAXPROCS个活跃线程能够正常运行，默认情况下GOMAXPROCS被设置为内核数，假如有四个内核，那么默认就创建四个线程，每一个线程对应一个runtime.m结构体。线程数等于CPU个数的原因是，每个线程分配到一个CPU上就不至于出现线程的上下文切换，可以保证系统开销降到最低。

```go
type m struct {
	g0   *g 
	curg *g
	...
}
```

M里面存了两个比较重要的东西，一个是g0，一个是curg。

- g0：会深度参与运行时的调度过程，比如goroutine的创建、内存分配等
- curg：代表当前正在线程上执行的goroutine。

刚才说P是负责M与G的关联，所以M里面还要存储与P相关的数据。

```go
type m struct {
  ...
	p             puintptr
	nextp         puintptr
	oldp          puintptr
}
```

- p：正在运行代码的处理器
- nextp：暂存的处理器
- oldp：系统调用之前的线程的处理器

### Processor

Proccessor负责Machine与Goroutine的连接，它能提供线程需要的上下文环境，也能分配G到它应该去的线程上执行，有了它，每个G都能得到合理的调用，每个线程都不再浑水摸鱼，真是居家必备之良品。

同样的，处理器的数量也是默认按照GOMAXPROCS来设置的，与线程的数量一一对应。

```go
type p struct {
	m           muintptr

	runqhead uint32
	runqtail uint32
	runq     [256]guintptr
	runnext guintptr
	...
}
```

结构体P中存储了性能追踪、垃圾回收、计时器等相关的字段外，还存储了处理器的待运行队列，队列中存储的是待执行的Goroutine列表。

### 三者的关系

首先，默认启动四个线程四个处理器，然后互相绑定。

![](../images/17188139b47a27ca.jpg)

这个时候，一个Goroutine结构体被创建，在进行函数体地址、参数起始地址、参数长度等信息以及调度相关属性更新之后，它就要进到一个处理器的队列等待发车。

![](../images/17188139f32b2558.jpg)

啥，又创建了一个G？那就轮流往其他P里面放呗，相信你排队取号的时候看到其他窗口没人排队也会过去的。

![](../images/1718813a3359a1c0.jpg)

假如有很多G，都塞满了怎么办呢？那就不把G塞到处理器的私有队列里了，而是把它塞到全局队列里（候车大厅）。

![](../images/1718813a71f700a1.jpg)

除了往里塞之外，M这边还要疯狂往外取，首先去处理器的私有队列里取G执行，如果取完的话就去全局队列取，如果全局队列里也没有的话，就去其他处理器队列里偷，哇，这么饥渴，简直是恶魔啊！

![](../images/1718813abb679031.jpg)

如果哪里都没找到要执行的G呢？那M就会因为太失望和P断开关系，然后去睡觉（idle）了。

![](../images/1718813afa26977e.jpg)

那如果两个Goroutine正在通过channel做一些恩恩爱爱的事阻塞住了怎么办，难道M要等他们完事了再继续执行？显然不会，M并不稀罕这对Go男女，而会转身去找别的G执行。

![](../images/1718813b326b7c73.jpg)


## 系统调用

如果G进行了系统调用syscall，M也会跟着进入系统调用状态，那么这个P留在这里就浪费了，怎么办呢？这点精妙之处在于，P不会傻傻的等待G和M系统调用完成，而会去找其他比较闲的M执行其他的G。

![](../images/1718813b73c47064.jpg)

当G完成了系统调用，因为要继续往下执行，所以必须要再找一个空闲的处理器发车。

![](../images/1718813bad18d998.jpg)

如果没有空闲的处理器了，那就只能把G放回全局队列当中等待分配。

![](../images/1718813bec3d6674.jpg)


## sysmon

sysmon是我们的保洁阿姨，它是一个M，又叫监控线程，不需要P就可以独立运行，每20us~10ms会被唤醒一次出来打扫卫生，主要工作就是回收垃圾、回收长时间系统调度阻塞的P、向长时间运行的G发出抢占调度等等。

> 作者：平也
> 链接：https://juejin.im/post/5e999ead518825739b2d44d7
> 来源：掘金
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。





















# goroutine调度器概述

## goroutine简介

goroutine是Go语言实现的用户态线程，主要用来解决操作系统线程太“重”的问题，所谓的太重，主要表现在以下两个方面：

- 创建和切换太重：操作系统线程的创建和切换都需要进入内核，而进入内核所消耗的性能代价比较高，开销较大；
- 内存使用太重：一方面，为了尽量避免极端情况下操作系统线程栈的溢出，内核在创建操作系统线程时默认会为其分配一个较大的栈内存（虚拟地址空间，内核并不会一开始就分配这么多的物理内存），然而在绝大多数情况下，系统线程远远用不了这么多内存，这导致了浪费；另一方面，栈内存空间一旦创建和初始化完成之后其大小就不能再有变化，这决定了在某些特殊场景下系统线程栈还是有溢出的风险。

而相对的，用户态的goroutine则轻量得多：

- goroutine是用户态线程，其创建和切换都在用户代码中完成而无需进入操作系统内核，所以其开销要远远小于系统线程的创建和切换；
- goroutine启动时默认栈大小只有2k，这在多数情况下已经够用了，即使不够用，goroutine的栈也会自动扩大，同时，如果栈太大了过于浪费它还能自动收缩，这样既没有栈溢出的风险，也不会造成栈内存空间的大量浪费。

正是因为Go语言中实现了如此轻量级的线程，才使得我们在Go程序中，可以轻易的创建成千上万甚至上百万的goroutine出来并发的执行任务而不用太担心性能和内存等问题。

**注意：** 为了避免混淆，从现在开始，后面出现的所有的线程一词均是指操作系统线程，而goroutine我们不再称之为什么什么线程而是直接使用goroutine这个词。

## 线程模型与调度器

第一章讨论操作系统线程调度的时候我们曾经提到过，goroutine建立在操作系统线程基础之上，它与操作系统线程之间实现了一个多对多(M:N)的两级线程模型。

这里的 M:N 是指M个goroutine运行在N个操作系统线程之上，内核负责对这N个操作系统线程进行调度，而这N个系统线程又负责对这M个goroutine进行调度和运行。

所谓的对goroutine的调度，是指程序代码按照一定的算法在适当的时候挑选出合适的goroutine并放到CPU上去运行的过程，这些负责对goroutine进行调度的程序代码我们称之为goroutine调度器。用极度简化了的伪代码来描述goroutine调度器的工作流程大概是下面这个样子：

```go
// 程序启动时的初始化代码
......
for i := 0; i < N; i++ { // 创建N个操作系统线程执行schedule函数
    create_os_thread(schedule) // 创建一个操作系统线程执行schedule函数
}

//schedule函数实现调度逻辑
func schedule() {
   for { //调度循环
         // 根据某种算法从M个goroutine中找出一个需要运行的goroutine
         g := find_a_runnable_goroutine_from_M_goroutines()
         run_g(g) // CPU运行该goroutine，直到需要调度其它goroutine才返回
         save_status_of_g(g) // 保存goroutine的状态，主要是寄存器的值
    }
}
```

这段伪代码表达的意思是，程序运行起来之后创建了N个由内核调度的操作系统线程（为了方便描述，我们称这些系统线程为工作线程）去执行shedule函数，而schedule函数在一个调度循环中反复从M个goroutine中挑选出一个需要运行的goroutine并跳转到该goroutine去运行，直到需要调度其它goroutine时才返回到schedule函数中通过save_status_of_g保存刚刚正在运行的goroutine的状态然后再次去寻找下一个goroutine。

需要强调的是，这段伪代码对goroutine的调度代码做了高度的抽象、修改和简化处理，放在这里只是为了帮助我们从宏观上了解goroutine的两级调度模型，具体的实现原理和细节将从本章开始进行全面介绍。

## 调度器数据结构概述

第一章我们讨论操作系统线程及其调度时还说过，可以把内核对系统线程的调度简单的归纳为：在执行操作系统代码时，内核调度器按照一定的算法挑选出一个线程并把该线程保存在内存之中的寄存器的值放入CPU对应的寄存器从而恢复该线程的运行。

万变不离其宗，系统线程对goroutine的调度与内核对系统线程的调度原理是一样的，实质都是通过保存和修改CPU寄存器的值来达到切换线程/goroutine的目的。

因此，为了实现对goroutine的调度，需要引入一个数据结构来保存CPU寄存器的值以及goroutine的其它一些状态信息，在Go语言调度器源代码中，这个数据结构是一个名叫g的结构体，它保存了goroutine的所有信息，该结构体的每一个实例对象都代表了一个goroutine，调度器代码可以通过g对象来对goroutine进行调度，当goroutine被调离CPU时，调度器代码负责把CPU寄存器的值保存在g对象的成员变量之中，当goroutine被调度起来运行时，调度器代码又负责把g对象的成员变量所保存的寄存器的值恢复到CPU的寄存器。

要实现对goroutine的调度，仅仅有g结构体对象是不够的，至少还需要一个存放所有（可运行）goroutine的容器，便于工作线程寻找需要被调度起来运行的goroutine，于是Go调度器又引入了schedt结构体，一方面用来保存调度器自身的状态信息，另一方面它还拥有一个用来保存goroutine的运行队列。因为每个Go程序只有一个调度器，所以在每个Go程序中schedt结构体只有一个实例对象，该实例对象在源代码中被定义成了一个共享的全局变量，这样每个工作线程都可以访问它以及它所拥有的goroutine运行队列，我们称这个运行队列为全局运行队列。

既然说到全局运行队列，读者可能猜想到应该还有一个局部运行队列。确实如此，因为全局运行队列是每个工作线程都可以读写的，因此访问它需要加锁，然而在一个繁忙的系统中，加锁会导致严重的性能问题。于是，调度器又为每个工作线程引入了一个私有的局部goroutine运行队列，工作线程优先使用自己的局部运行队列，只有必要时才会去访问全局运行队列，这大大减少了锁冲突，提高了工作线程的并发性。在Go调度器源代码中，局部运行队列被包含在p结构体的实例对象之中，每一个运行着go代码的工作线程都会与一个p结构体的实例对象关联在一起。

除了上面介绍的g、schedt和p结构体，Go调度器源代码中还有一个用来代表工作线程的m结构体，每个工作线程都有唯一的一个m结构体的实例对象与之对应，m结构体对象除了记录着工作线程的诸如栈的起止位置、当前正在执行的goroutine以及是否空闲等等状态信息之外，还通过指针维持着与p结构体的实例对象之间的绑定关系。于是，通过m既可以找到与之对应的工作线程正在运行的goroutine，又可以找到工作线程的局部运行队列等资源。下面是g、p、m和schedt之间的关系图：

![](../images/640.webp)

上图中圆形图案代表g结构体的实例对象，三角形代表m结构体的实例对象，正方形代表p结构体的实例对象，其中红色的g表示m对应的工作线程正在运行的goroutine，而灰色的g表示处于运行队列之中正在等待被调度起来运行的goroutine。

从上图可以看出，每个m都绑定了一个p，每个p都有一个私有的本地goroutine队列，m对应的线程从本地和全局goroutine队列中获取goroutine并运行之。

前面我们说每个工作线程都有一个m结构体对象与之对应，但并未详细说明它们之间是如何对应起来的，工作线程执行的代码是如何找到属于自己的那个m结构体实例对象的呢？

如果只有一个工作线程，那么就只会有一个m结构体对象，问题就很简单，定义一个全局的m结构体变量就行了。可是我们有多个工作线程和多个m需要一一对应，怎么办呢？还记得第一章我们讨论过的线程本地存储吗？当时我们说过，线程本地存储其实就是线程私有的全局变量，这不正是我们所需要的吗？！只要每个工作线程拥有了各自私有的m结构体全局变量，我们就能在不同的工作线程中使用相同的全局变量名来访问不同的m结构体对象，这完美的解决我们的问题。

具体到goroutine调度器代码，每个工作线程在刚刚被创建出来进入调度循环之前就利用线程本地存储机制为该工作线程实现了一个指向m结构体实例对象的私有全局变量，这样在之后的代码中就使用该全局变量来访问自己的m结构体对象以及与m相关联的p和g对象。

有了上述数据结构以及工作线程与数据结构之间的映射机制，我们可以把前面的调度伪代码写得更丰满一点：

```go
// 程序启动时的初始化代码
......
for i := 0; i < N; i++ { // 创建N个操作系统线程执行schedule函数
     create_os_thread(schedule) // 创建一个操作系统线程执行schedule函数
}


// 定义一个线程私有全局变量，注意它是一个指向m结构体对象的指针
// ThreadLocal用来定义线程私有全局变量
ThreadLocal self *m
//schedule函数实现调度逻辑
func schedule() {
    // 创建和初始化m结构体对象，并赋值给私有全局变量self
    self = initm()  
    for { //调度循环
          if (self.p.runqueue is empty) {
                 // 根据某种算法从全局运行队列中找出一个需要运行的goroutine
                 g := find_a_runnable_goroutine_from_global_runqueue()
           } else {
                 // 根据某种算法从私有的局部运行队列中找出一个需要运行的goroutine
                 g := find_a_runnable_goroutine_from_local_runqueue()
           }
          run_g(g) // CPU运行该goroutine，直到需要调度其它goroutine才返回
          save_status_of_g(g) // 保存goroutine的状态，主要是寄存器的值
     }
}
```


仅仅从上面这个伪代码来看，我们完全不需要线程私有全局变量，只需在schedule函数中定义一个局部变量就行了。但真实的调度代码错综复杂，不光是这个schedule函数会需要访问m，其它很多地方还需要访问它，所以需要使用全局变量来方便其它地方对m的以及与m相关的g和p的访问。

在简单的介绍了Go语言调度器以及它所需要的数据结构之后，下面我们来看一下Go的调度代码中对上述的几个结构体的定义。

## 重要的结构体

下面介绍的这些结构体中的字段非常多，牵涉到的细节也很庞杂，光是看这些结构体的定义我们没有必要也无法真正理解它们的用途，所以在这里我们只需要大概了解一下就行了，看不懂记不住都没有关系，随着后面对代码逐步深入的分析，我们也必将会对这些结构体有越来越清晰的认识。为了节省篇幅，下面各结构体的定义略去了跟调度器无关的成员。另外，这些结构体的定义全部位于Go语言的源代码路径下的[runtime/runtime2.go](https://github.com/golang/go/blob/master/src/runtime/runtime2.go)文件之中。

### stack结构体

stack结构体主要用来记录goroutine所使用的栈的信息，包括栈顶和栈底位置：

```go
// Stack describes a Go execution stack.
// The bounds of the stack are exactly [lo, hi),
// with no implicit data structures on either side.
//用于记录goroutine使用的栈的起始和结束位置
type stack struct {  
    lo uintptr    // 栈顶，指向内存低地址
    hi uintptr    // 栈底，指向内存高地址
}
```

### gobuf结构体

gobuf结构体用于保存goroutine的调度信息，主要包括CPU的几个寄存器的值：

```go
type gobuf struct {
    // The offsets of sp, pc, and g are known to (hard-coded in) libmach.
    //
    // ctxt is unusual with respect to GC: it may be a
    // heap-allocated funcval, so GC needs to track it, but it
    // needs to be set and cleared from assembly, where it's
    // difficult to have write barriers. However, ctxt is really a
    // saved, live register, and we only ever exchange it between
    // the real register and the gobuf. Hence, we treat it as a
    // root during stack scanning, which means assembly that saves
    // and restores it doesn't need write barriers. It's still
    // typed as a pointer so that any other writes from Go get
    // write barriers.
    sp   uintptr  // 保存CPU的rsp寄存器的值
    pc   uintptr  // 保存CPU的rip寄存器的值
    g    guintptr // 记录当前这个gobuf对象属于哪个goroutine
    ctxt unsafe.Pointer
 
    // 保存系统调用的返回值，因为从系统调用返回之后如果p被其它工作线程抢占，
    // 则这个goroutine会被放入全局运行队列被其它工作线程调度，其它线程需要知道系统调用的返回值。
    ret  sys.Uintreg  
    lr   uintptr
 
    // 保存CPU的rip寄存器的值
    bp   uintptr // for GOEXPERIMENT=framepointer
}
```

### g结构体
    
g结构体用于代表一个goroutine，该结构体保存了goroutine的所有信息，包括栈，gobuf结构体和其它的一些状态信息：

```go
// 前文所说的g结构体，它代表了一个goroutine
type g struct {
    // Stack parameters.
    // stack describes the actual stack memory: [stack.lo, stack.hi).
    // stackguard0 is the stack pointer compared in the Go stack growth prologue.
    // It is stack.lo+StackGuard normally, but can be StackPreempt to trigger a preemption.
    // stackguard1 is the stack pointer compared in the C stack growth prologue.
    // It is stack.lo+StackGuard on g0 and gsignal stacks.
    // It is ~0 on other goroutine stacks, to trigger a call to morestackc (and crash).
 
    // 记录该goroutine使用的栈
    stack       stack   // offset known to runtime/cgo
    // 下面两个成员用于栈溢出检查，实现栈的自动伸缩，抢占调度也会用到stackguard0
    stackguard0 uintptr // offset known to liblink
    stackguard1 uintptr // offset known to liblink

    ......
 
    // 此goroutine正在被哪个工作线程执行
    m              *m      // current m; offset known to arm liblink
    // 保存调度信息，主要是几个寄存器的值
    sched          gobuf
 
    ......
    // schedlink字段指向全局运行队列中的下一个g，
    //所有位于全局运行队列中的g形成一个链表
    schedlink      guintptr

    ......
    // 抢占调度标志，如果需要抢占调度，设置preempt为true
    preempt        bool       // preemption signal, duplicates stackguard0 = stackpreempt

   ......
}
```

### m结构体

m结构体用来代表工作线程，它保存了m自身使用的栈信息，当前正在运行的goroutine以及与m绑定的p等信息，详见下面定义中的注释：

```go
type m struct {
    // g0主要用来记录工作线程使用的栈信息，在执行调度代码时需要使用这个栈
    // 执行用户goroutine代码时，使用用户goroutine自己的栈，调度时会发生栈的切换
    g0      *g     // goroutine with scheduling stack

    // 通过TLS实现m结构体对象与工作线程之间的绑定
    tls           [6]uintptr   // thread-local storage (for x86 extern register)
    mstartfn      func()
    // 指向工作线程正在运行的goroutine的g结构体对象
    curg          *g       // current running goroutine
 
    // 记录与当前工作线程绑定的p结构体对象
    p             puintptr // attached p for executing go code (nil if not executing go code)
    nextp         puintptr
    oldp          puintptr // the p that was attached before executing a syscall
   
    // spinning状态：表示当前工作线程正在试图从其它工作线程的本地运行队列偷取goroutine
    spinning      bool // m is out of work and is actively looking for work
    blocked       bool // m is blocked on a note
   
    // 没有goroutine需要运行时，工作线程睡眠在这个park成员上，
    // 其它线程通过这个park唤醒该工作线程
    park          note
    // 记录所有工作线程的一个链表
    alllink       *m // on allm
    schedlink     muintptr

    // Linux平台thread的值就是操作系统线程ID
    thread        uintptr // thread handle
    freelink      *m      // on sched.freem

    ......
}
```

### p结构体

p结构体用于保存工作线程执行go代码时所必需的资源，比如goroutine的运行队列，内存分配用到的缓存等等。

```go
type p struct {
    lock mutex

    status       uint32 // one of pidle/prunning/...
    link            puintptr
    schedtick   uint32     // incremented on every scheduler call
    syscalltick  uint32     // incremented on every system call
    sysmontick  sysmontick // last tick observed by sysmon
    m                muintptr   // back-link to associated m (nil if idle)

    ......

    // Queue of runnable goroutines. Accessed without lock.
    //本地goroutine运行队列
    runqhead uint32  // 队列头
    runqtail uint32     // 队列尾
    runq     [256]guintptr  //使用数组实现的循环队列
    // runnext, if non-nil, is a runnable G that was ready'd by
    // the current G and should be run next instead of what's in
    // runq if there's time remaining in the running G's time
    // slice. It will inherit the time left in the current time
    // slice. If a set of goroutines is locked in a
    // communicate-and-wait pattern, this schedules that set as a
    // unit and eliminates the (potentially large) scheduling
    // latency that otherwise arises from adding the ready'd
    // goroutines to the end of the run queue.
    runnext guintptr

    // Available G's (status == Gdead)
    gFree struct {
        gList
        n int32
    }

    ......
}
```

### schedt结构体

schedt结构体用来保存调度器的状态信息和goroutine的全局运行队列：

```go
type schedt struct {
    // accessed atomically. keep at top to ensure alignment on 32-bit systems.
    goidgen  uint64
    lastpoll uint64

    lock mutex

    // When increasing nmidle, nmidlelocked, nmsys, or nmfreed, be
    // sure to call checkdead().

    // 由空闲的工作线程组成链表
    midle        muintptr // idle m's waiting for work
    // 空闲的工作线程的数量
    nmidle       int32    // number of idle m's waiting for work
    nmidlelocked int32    // number of locked m's waiting for work
    mnext        int64    // number of m's that have been created and next M ID
    // 最多只能创建maxmcount个工作线程
    maxmcount    int32    // maximum number of m's allowed (or die)
    nmsys        int32    // number of system m's not counted for deadlock
    nmfreed      int64    // cumulative number of freed m's

    ngsys uint32 // number of system goroutines; updated atomically

    // 由空闲的p结构体对象组成的链表
    pidle      puintptr // idle p's
    // 空闲的p结构体对象的数量
    npidle     uint32
    nmspinning uint32 // See "Worker thread parking/unparking" comment in proc.go.

    // Global runnable queue.
    // goroutine全局运行队列
    runq     gQueue
    runqsize int32

    ......

    // Global cache of dead G's.
    // gFree是所有已经退出的goroutine对应的g结构体对象组成的链表
    // 用于缓存g结构体对象，避免每次创建goroutine时都重新分配内存
    gFree struct {
        lock          mutex
        stack        gList // Gs with stacks
        noStack   gList // Gs without stacks
        n              int32
    }
 
    ......
}
```

## 重要的全局变量

```go
allgs     []*g     // 保存所有的g
allm       *m    // 所有的m构成的一个链表，包括下面的m0
allp       []*p    // 保存所有的p，len(allp) == gomaxprocs

ncpu             int32   // 系统中cpu核的数量，程序启动时由runtime代码初始化
gomaxprocs int32   // p的最大值，默认等于ncpu，但可以通过GOMAXPROCS修改

sched      schedt     // 调度器结构体对象，记录了调度器的工作状态

m0  m       // 代表进程的主线程
g0   g        // m0的g0，也就是m0.g0 = &g0
```

在程序初始化时，这些全变量都会被初始化为0值，指针会被初始化为nil指针，切片初始化为nil切片，int被初始化为数字0，结构体的所有成员变量按其本类型初始化为其类型的0值。所以程序刚启动时allgs，allm和allp都不包含任何g,m和p。

> 原文： <https://mp.weixin.qq.com/s?__biz=MzU1OTg5NDkzOA==&mid=2247483761&idx=1&sn=15949fbeb48985c6c3781583df6956e6&scene=19#wechat_redirect>











## 常见语法题目 二


### 1、写出下面代码输出内容。

```go
package main

import (
	"fmt"
)

func main() {
	defer_call()
}

func defer_call() {
	defer func() { fmt.Println("打印前") }()
	defer func() { fmt.Println("打印中") }()
	defer func() { fmt.Println("打印后") }()

	panic("触发异常")
}
```

**解析：**

`defer` 关键字的实现跟go关键字很类似，不同的是它调用的是`runtime.deferproc`而不是`runtime.newproc`。

在`defer`出现的地方，插入了指令`call runtime.deferproc`，然后在函数返回之前的地方，插入指令`call runtime.deferreturn`。

goroutine的控制结构中，有一张表记录`defer`，调用`runtime.deferproc`时会将需要defer的表达式记录在表中，而在调用`runtime.deferreturn`的时候，则会依次从defer表中出栈并执行。

因此，题目最后输出顺序应该是`defer` 定义顺序的倒序。`panic` 错误并不能终止 `defer` 的执行。

### 2、 以下代码有什么问题，说明原因

```go
type student struct {
	Name string
	Age  int
}

func pase_student() {
	m := make(map[string]*student)
	stus := []student{
		{Name: "zhou", Age: 24},
		{Name: "li", Age: 23},
		{Name: "wang", Age: 22},
	}
	for _, stu := range stus {
		m[stu.Name] = &stu
	}
}
```

**解析：**

golang 的 `for ... range` 语法中，`stu` 变量会被复用，每次循环会将集合中的值复制给这个变量，因此，会导致最后`m`中的`map`中储存的都是`stus`最后一个`student`的值。


### 3、下面的代码会输出什么，并说明原因

```go
func main() {
	runtime.GOMAXPROCS(1)
	wg := sync.WaitGroup{}
	wg.Add(20)
	for i := 0; i < 10; i++ {
		go func() {
			fmt.Println("i: ", i)
			wg.Done()
		}()
	}
	for i := 0; i < 10; i++ {
		go func(i int) {
			fmt.Println("i: ", i)
			wg.Done()
		}(i)
	}
	wg.Wait()
}

```

**解析：**

这个输出结果决定来自于调度器优先调度哪个G。从runtime的源码可以看到，当创建一个G时，会优先放入到下一个调度的`runnext`字段上作为下一次优先调度的G。因此，最先输出的是最后创建的G，也就是9.

```go
func newproc(siz int32, fn *funcval) {
	argp := add(unsafe.Pointer(&fn), sys.PtrSize)
	gp := getg()
	pc := getcallerpc()
	systemstack(func() {
		newg := newproc1(fn, argp, siz, gp, pc)

		_p_ := getg().m.p.ptr()
        //新创建的G会调用这个方法来决定如何调度
		runqput(_p_, newg, true)

		if mainStarted {
			wakep()
		}
	})
}
...

	if next {
	retryNext:
		oldnext := _p_.runnext
        //当next是true时总会将新进来的G放入下一次调度字段中
		if !_p_.runnext.cas(oldnext, guintptr(unsafe.Pointer(gp))) {
			goto retryNext
		}
		if oldnext == 0 {
			return
		}
		// Kick the old runnext out to the regular run queue.
		gp = oldnext.ptr()
	}
```


### 4、下面代码会输出什么？

```go
type People struct{}

func (p *People) ShowA() {
	fmt.Println("showA")
	p.ShowB()
}
func (p *People) ShowB() {
	fmt.Println("showB")
}

type Teacher struct {
	People
}

func (t *Teacher) ShowB() {
	fmt.Println("teacher showB")
}

func main() {
	t := Teacher{}
	t.ShowA()
}


```


**解析：**

输出结果为`showA`、`showB`。golang 语言中没有继承概念，只有组合，也没有虚方法，更没有重载。因此，`*Teacher` 的 `ShowB` 不会覆写被组合的 `People` 的方法。


### 5、下面代码会触发异常吗？请详细说明

```go
func main() {
	runtime.GOMAXPROCS(1)
	int_chan := make(chan int, 1)
	string_chan := make(chan string, 1)
	int_chan <- 1
	string_chan <- "hello"
	select {
	case value := <-int_chan:
		fmt.Println(value)
	case value := <-string_chan:
		panic(value)
	}
}
```

**解析：**

结果是随机执行。golang 在多个`case` 可读的时候会公平的选中一个执行。

### 6、下面代码输出什么？

```go
func calc(index string, a, b int) int {
	ret := a + b
	fmt.Println(index, a, b, ret)
	return ret
}

func main() {
	a := 1
	b := 2
	defer calc("1", a, calc("10", a, b))
	a = 0
	defer calc("2", a, calc("20", a, b))
	b = 1
}
```

**解析：**

输出结果为：

```
10 1 2 3
20 0 2 2
2 0 2 2
1 1 3 4
```

`defer` 在定义的时候会计算好调用函数的参数，所以会优先输出`10`、`20` 两个参数。然后根据定义的顺序倒序执行。


### 7、请写出以下输入内容

```go
func main() {
	s := make([]int, 5)
	s = append(s, 1, 2, 3)
	fmt.Println(s)
}

```

**解析：**

输出为 `0 0 0 0 0 1 2 3`。

`make` 在初始化切片时指定了长度，所以追加数据时会从`len(s)` 位置开始填充数据。


### 8、下面的代码有什么问题?

```go
type UserAges struct {
	ages map[string]int
	sync.Mutex
}

func (ua *UserAges) Add(name string, age int) {
	ua.Lock()
	defer ua.Unlock()
	ua.ages[name] = age
}

func (ua *UserAges) Get(name string) int {
	if age, ok := ua.ages[name]; ok {
		return age
	}
	return -1
}
```

**解析：**

 在执行 Get方法时可能被panic。
 
 虽然有使用sync.Mutex做写锁，但是map是并发读写不安全的。map属于引用类型，并发读写时多个协程见是通过指针访问同一个地址，即访问共享变量，此时同时读写资源存在竞争关系。会报错误信息:“fatal error: concurrent map read and map write”。
 
因此，在 `Get` 中也需要加锁，因为这里只是读，建议使用读写锁 `sync.RWMutex`。


### 9、下面的迭代会有什么问题？

```go
func (set *threadSafeSet) Iter() <-chan interface{} {
	ch := make(chan interface{})
	go func() {
		set.RLock()

		for elem := range set.s {
			ch <- elem
		}

		close(ch)
		set.RUnlock()

	}()
	return ch
}
```

**解析：**

默认情况下 `make` 初始化的 `channel` 是无缓冲的，也就是在迭代写时会阻塞。

### 10、以下代码能编译过去吗？为什么？

```go
package main

import (
	"fmt"
)

type People interface {
	Speak(string) string
}

type Student struct{}

func (stu *Student) Speak(think string) (talk string) {
	if think == "bitch" {
		talk = "You are a good boy"
	} else {
		talk = "hi"
	}
	return
}

func main() {
	var peo People = Student{}
	think := "bitch"
	fmt.Println(peo.Speak(think))
}
```

**解析：**


编译失败，值类型 `Student{}` 未实现接口`People`的方法，不能定义为 `People`类型。

在 golang 语言中，`Student` 和 `*Student` 是两种类型，第一个是表示 `Student` 本身，第二个是指向 `Student` 的指针。




### 11、以下代码打印出来什么内容，说出为什么。。。

```go
package main

import (
	"fmt"
)

type People interface {
	Show()
}

type Student struct{}

func (stu *Student) Show() {

}

func live() People {
	var stu *Student
	return stu
}

func main() {
	if live() == nil {
		fmt.Println("AAAAAAA")
	} else {
		fmt.Println("BBBBBBB")
	}
}
```

**解析：**

跟上一题一样，不同的是`*Student` 的定义后本身没有初始化值，所以 `*Student` 是 `nil`的，但是`*Student` 实现了 `People` 接口，接口不为 `nil`。
# 在 golang 协程和channel配合使用

> 写代码实现两个 goroutine，其中一个产生随机数并写入到 go channel 中，另外一个从 channel 中读取数字并打印到标准输出。最终输出五个随机数。

**解析**

这是一道很简单的golang基础题目，实现方法也有很多种，一般想答让面试官满意的答案还是有几点注意的地方。

1. `goroutine` 在golang中式非阻塞的
2. `channel` 无缓冲情况下，读写都是阻塞的，且可以用`for`循环来读取数据，当管道关闭后，`for` 退出。
3.  golang 中有专用的`select case` 语法从管道读取数据。

示例代码如下：

```go
func main() {
    out := make(chan int)
    wg := sync.WaitGroup{}
    wg.Add(2)
    go func() {
        defer wg.Done()
        for i := 0; i < 5; i++ {
            out <- rand.Intn(5)
        }
        close(out)
    }()
    go func() {
        defer wg.Done()
        for i := range out {
            fmt.Println(i)
        }
    }()
    wg.Wait()
}
```# 实现阻塞读且并发安全的map

GO里面MAP如何实现key不存在 get操作等待 直到key存在或者超时，保证并发安全，且需要实现以下接口：

```go
type sp interface {
    Out(key string, val interface{})  //存入key /val，如果该key读取的goroutine挂起，则唤醒。此方法不会阻塞，时刻都可以立即执行并返回
    Rd(key string, timeout time.Duration) interface{}  //读取一个key，如果key不存在阻塞，等待key存在或者超时
}
```

**解析：**

看到阻塞协程第一个想到的就是`channel`，题目中要求并发安全，那么必须用锁，还要实现多个`goroutine`读的时候如果值不存在则阻塞，直到写入值，那么每个键值需要有一个阻塞`goroutine` 的 `channel`。

[实现如下：](../src/q010.go) 

```go
type Map struct {
	c   map[string]*entry
	rmx *sync.RWMutex
}
type entry struct {
	ch      chan struct{}
	value   interface{}
	isExist bool
}

func (m *Map) Out(key string, val interface{}) {
	m.rmx.Lock()
	defer m.rmx.Unlock()
	item, ok := m.c[key]
	if !ok {
		m.c[key] = &entry{
			value: val,
			isExist: true,
		}
		return
	}
	item.value = val
	if !item.isExist {
		if item.ch != nil {
			close(item.ch)
			item.ch = nil
		}
	}
	return
}
```
# 高并发下的锁与map的读写

场景：在一个高并发的web服务器中，要限制IP的频繁访问。现模拟100个IP同时并发访问服务器，每个IP要重复访问1000次。

每个IP三分钟之内只能访问一次。修改以下代码完成该过程，要求能成功输出 success:100

```go
package main
 
import (
	"fmt"
	"time"
)
 
type Ban struct {
	visitIPs map[string]time.Time
}
 
func NewBan() *Ban {
	return &Ban{visitIPs: make(map[string]time.Time)}
}
func (o *Ban) visit(ip string) bool {
	if _, ok := o.visitIPs[ip]; ok {
		return true
	}
	o.visitIPs[ip] = time.Now()
	return false
}
func main() {
	success := 0
	ban := NewBan()
	for i := 0; i < 1000; i++ {
		for j := 0; j < 100; j++ {
			go func() {
				ip := fmt.Sprintf("192.168.1.%d", j)
				if !ban.visit(ip) {
					success++
				}
			}()
		}
 
	}
	fmt.Println("success:", success)
}
```

**解析**

该问题主要考察了并发情况下map的读写问题，而给出的初始代码，又存在`for`循环中启动`goroutine`时变量使用问题以及`goroutine`执行滞后问题。

因此，首先要保证启动的`goroutine`得到的参数是正确的，然后保证`map`的并发读写，最后保证三分钟只能访问一次。

多CPU核心下修改`int`的值极端情况下会存在不同步情况，因此需要原子性的修改int值。

下面给出的实例代码，是启动了一个协程每分钟检查一下`map`中的过期`ip`，`for`启动协程时传参。

```go
package main

import (
	"context"
	"fmt"
	"sync"
	"sync/atomic"
	"time"
)

type Ban struct {
	visitIPs map[string]time.Time
	lock      sync.Mutex
}

func NewBan(ctx context.Context) *Ban {
	o := &Ban{visitIPs: make(map[string]time.Time)}
	go func() {
		timer := time.NewTimer(time.Minute * 1)
		for {
			select {
			case <-timer.C:
				o.lock.Lock()
				for k, v := range o.visitIPs {
					if time.Now().Sub(v) >= time.Minute*1 {
						delete(o.visitIPs, k)
					}
				}
				o.lock.Unlock()
				timer.Reset(time.Minute * 1)
			case <-ctx.Done():
				return
			}
		}
	}()
	return o
}
func (o *Ban) visit(ip string) bool {
	o.lock.Lock()
	defer o.lock.Unlock()
	if _, ok := o.visitIPs[ip]; ok {
		return true
	}
	o.visitIPs[ip] = time.Now()
	return false
}
func main() {
	success := int64(0)
	ctx, cancel := context.WithCancel(context.Background())
	defer cancel()

	ban := NewBan(ctx)

	wait := &sync.WaitGroup{}

	wait.Add(1000 * 100)
	for i := 0; i < 1000; i++ {
		for j := 0; j < 100; j++ {
			go func(j int) {
				defer wait.Done()
				ip := fmt.Sprintf("192.168.1.%d", j)
				if !ban.visit(ip) {
					atomic.AddInt64(&success, 1)
				}
			}(j)
		}

	}
	wait.Wait()

	fmt.Println("success:", success)
}
```## 1. 写出以下逻辑，要求每秒钟调用一次proc并保证程序不退出?

```go
package main

func main() {
    go func() {
        // 1 在这里需要你写算法
        // 2 要求每秒钟调用一次proc函数
        // 3 要求程序不能退出
    }()

    select {}
}

func proc() {
    panic("ok")
}
```

**解析**

题目主要考察了两个知识点：

1. 定时执行执行任务
2. 捕获 panic 错误

题目中要求每秒钟执行一次，首先想到的就是 `time.Ticker`对象，该函数可每秒钟往`chan`中放一个`Time`,正好符合我们的要求。

在 `golang` 中捕获 `panic` 一般会用到 `recover()` 函数。  

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	go func() {
		// 1 在这里需要你写算法
		// 2 要求每秒钟调用一次proc函数
		// 3 要求程序不能退出

		t := time.NewTicker(time.Second * 1)
		for {
			select {
			case <-t.C:
				go func() {
					defer func() {
						if err := recover(); err != nil {
							fmt.Println(err)
						}
					}()
					proc()
				}()
			}
		}
	}()

	select {}
}

func proc() {
	panic("ok")
}

```## 为 sync.WaitGroup 中Wait函数支持 WaitTimeout 功能.

```go
package main

import (
    "fmt"
    "sync"
    "time"
)

func main() {
    wg := sync.WaitGroup{}
    c := make(chan struct{})
    for i := 0; i < 10; i++ {
        wg.Add(1)
        go func(num int, close <-chan struct{}) {
            defer wg.Done()
            <-close
            fmt.Println(num)
        }(i, c)
    }

    if WaitTimeout(&wg, time.Second*5) {
        close(c)
        fmt.Println("timeout exit")
    }
    time.Sleep(time.Second * 10)
}

func WaitTimeout(wg *sync.WaitGroup, timeout time.Duration) bool {
    // 要求手写代码
    // 要求sync.WaitGroup支持timeout功能
    // 如果timeout到了超时时间返回true
    // 如果WaitGroup自然结束返回false
}
```


**解析**

首先 `sync.WaitGroup` 对象的 `Wait` 函数本身是阻塞的，同时，超时用到的`time.Timer` 对象也需要阻塞的读。

同时阻塞的两个对象肯定要每个启动一个协程,每个协程去处理一个阻塞，难点在于怎么知道哪个阻塞先完成。

目前我用的方式是声明一个没有缓冲的`chan`，谁先完成谁优先向管道中写入数据。

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

func main() {
	wg := sync.WaitGroup{}
	c := make(chan struct{})
	for i := 0; i < 10; i++ {
		wg.Add(1)
		go func(num int, close <-chan struct{}) {
			defer wg.Done()
			<-close
			fmt.Println(num)
		}(i, c)
	}

	if WaitTimeout(&wg, time.Second*5) {
		close(c)
		fmt.Println("timeout exit")
	}
	time.Sleep(time.Second * 10)
}

func WaitTimeout(wg *sync.WaitGroup, timeout time.Duration) bool {
	// 要求手写代码
	// 要求sync.WaitGroup支持timeout功能
	// 如果timeout到了超时时间返回true
	// 如果WaitGroup自然结束返回false
	ch := make(chan bool, 1)

	go time.AfterFunc(timeout, func() {
		ch <- true
	})

	go func() {
		wg.Wait()
		ch <- false
	}()
	
	return <- ch
}
```
# 语法找错题

## 写出以下代码出现的问题

```go
package main
import (
    "fmt"
)
func main() {
    var x string = nil
    if x == nil {
        x = "default"
    }
    fmt.Println(x)
}
```

golang 中字符串是不能赋值 `nil` 的，也不能跟 `nil` 比较。

## 写出以下打印内容
   
```go
   package main
   import "fmt"
   const (
       a = iota
       b = iota
   )
   const (
       name = "menglu"
       c    = iota
       d    = iota
   )
   func main() {
       fmt.Println(a)
       fmt.Println(b)
       fmt.Println(c)
       fmt.Println(d)
   }
```

## 找出下面代码的问题

```go
package main
import "fmt"
type query func(string) string

func exec(name string, vs ...query) string {
    ch := make(chan string)
    fn := func(i int) {
        ch <- vs[i](name)
    }
    for i, _ := range vs {
        go fn(i)
    }
    return <-ch
}

func main() {
    ret := exec("111", func(n string) string {
        return n + "func1"
    }, func(n string) string {
        return n + "func2"
    }, func(n string) string {
        return n + "func3"
    }, func(n string) string {
        return n + "func4"
    })
    fmt.Println(ret)
}
```

上面的代码有严重的内存泄漏问题，出错的位置是 `go fn(i)`，实际上代码执行后会启动 4 个协程，但是因为 `ch` 是非缓冲的，只可能有一个协程写入成功。而其他三个协程会一直在后台等待写入。

## 写出以下打印结果，并解释下为什么这么打印的。
   
```go
package main
import (
    "fmt"
)
func main() {
    str1 := []string{"a", "b", "c"}
    str2 := str1[1:]
    str2[1] = "new"
    fmt.Println(str1)
    str2 = append(str2, "z", "x", "y")
    fmt.Println(str1)
}
```

golang 中的切片底层其实使用的是数组。当使用`str1[1:]` 使，`str2` 和 `str1` 底层共享一个数组，这回导致 `str2[1] = "new"` 语句影响 `str1`。

而 `append` 会导致底层数组扩容，生成新的数组，因此追加数据后的 `str2` 不会影响 `str1`。

但是为什么对 `str2` 复制后影响的确实 `str1` 的第三个元素呢？这是因为切片  `str2` 是从数组的第二个元素开始，`str2` 索引为 1 的元素对应的是 `str1` 索引为 2 的元素。

## 写出以下打印结果
   
```go
package main

import (
    "fmt"
)

type Student struct {
    Name string
}

func main() {
    fmt.Println(&Student{Name: "menglu"} == &Student{Name: "menglu"})
    fmt.Println(Student{Name: "menglu"} == Student{Name: "menglu"})
}
```

个人理解：指针类型比较的是指针地址，非指针类型比较的是每个属性的值。

## 写出以下代码的问题

```go
package main

import (
    "fmt"
)

func main() {
    fmt.Println([...]string{"1"} == [...]string{"1"})
    fmt.Println([]string{"1"} == []string{"1"})
}
```

数组只能与相同纬度长度以及类型的其他数组比较，切片之间不能直接比较。。

## 下面代码写法有什么问题？

```go
package main
import (
    "fmt"
)
type Student struct {
    Age int
}
func main() {
    kv := map[string]Student{"menglu": {Age: 21}}
    kv["menglu"].Age = 22
    s := []Student{{Age: 21}}
    s[0].Age = 22
    fmt.Println(kv, s)
}
```

golang中的`map` 通过`key`获取到的实际上是两个值，第一个是获取到的值，第二个是是否存在该`key`。因此不能直接通过`key`来赋值对象。
## golang 并发题目测试

题目来源： [Go并发编程小测验： 你能答对几道题？](https://colobu.com/2019/04/28/go-concurrency-quizzes/)

### 1 Mutex

```go
package main
import (
	"fmt"
	"sync"
)
var mu sync.Mutex
var chain string
func main() {
	chain = "main"
	A()
	fmt.Println(chain)
}
func A() {
	mu.Lock()
	defer mu.Unlock()
	chain = chain + " --> A"
	B()
}
func B() {
	chain = chain + " --> B"
	C()
}
func C() {
	mu.Lock()
	defer mu.Unlock()
	chain = chain + " --> C"
}
```

- A: 不能编译
- B: 输出 main --> A --> B --> C
- C: 输出 main
- D: panic

### 2 RWMutex

```go
package main
import (
	"fmt"
	"sync"
	"time"
)
var mu sync.RWMutex
var count int
func main() {
	go A()
	time.Sleep(2 * time.Second)
	mu.Lock()
	defer mu.Unlock()
	count++
	fmt.Println(count)
}
func A() {
	mu.RLock()
	defer mu.RUnlock()
	B()
}
func B() {
	time.Sleep(5 * time.Second)
	C()
}
func C() {
	mu.RLock()
	defer mu.RUnlock()
}
```

- A: 不能编译
- B: 输出 1
- C: 程序hang住
- D: panic

### 3 Waitgroup

```go
package main
import (
	"sync"
	"time"
)
func main() {
	var wg sync.WaitGroup
	wg.Add(1)
	go func() {
		time.Sleep(time.Millisecond)
		wg.Done()
		wg.Add(1)
	}()
	wg.Wait()
}
```

- A: 不能编译
- B: 无输出，正常退出
- C: 程序hang住
- D: panic

### 4 双检查实现单例

```go
package doublecheck
import (
	"sync"
)
type Once struct {
	m    sync.Mutex
	done uint32
}
func (o *Once) Do(f func()) {
	if o.done == 1 {
		return
	}
	o.m.Lock()
	defer o.m.Unlock()
	if o.done == 0 {
		o.done = 1
		f()
	}
}
```

- A: 不能编译
- B: 可以编译，正确实现了单例
- C: 可以编译，有并发问题，f函数可能会被执行多次
- D: 可以编译，但是程序运行会panic

### 5 Mutex

```go
package main
import (
	"fmt"
	"sync"
)
type MyMutex struct {
	count int
	sync.Mutex
}
func main() {
	var mu MyMutex
	mu.Lock()
	var mu2 = mu
	mu.count++
	mu.Unlock()
	mu2.Lock()
	mu2.count++
	mu2.Unlock()
	fmt.Println(mu.count, mu2.count)
}
```

- A: 不能编译
- B: 输出 1, 1
- C: 输出 1, 2
- D: panic

### 6 Pool

```go
package main
import (
	"bytes"
	"fmt"
	"runtime"
	"sync"
	"time"
)
var pool = sync.Pool{New: func() interface{} { return new(bytes.Buffer) }}
func main() {
	go func() {
		for {
			processRequest(1 << 28) // 256MiB
		}
	}()
	for i := 0; i < 1000; i++ {
		go func() {
			for {
				processRequest(1 << 10) // 1KiB
			}
		}()
	}
	var stats runtime.MemStats
	for i := 0; ; i++ {
		runtime.ReadMemStats(&stats)
		fmt.Printf("Cycle %d: %dB\n", i, stats.Alloc)
		time.Sleep(time.Second)
		runtime.GC()
	}
}
func processRequest(size int) {
	b := pool.Get().(*bytes.Buffer)
	time.Sleep(500 * time.Millisecond)
	b.Grow(size)
	pool.Put(b)
	time.Sleep(1 * time.Millisecond)
}
```

- A: 不能编译
- B: 可以编译，运行时正常，内存稳定
- C: 可以编译，运行时内存可能暴涨
- D: 可以编译，运行时内存先暴涨，但是过一会会回收掉

### 7 channel

```go
package main
import (
	"fmt"
	"runtime"
	"time"
)
func main() {
	var ch chan int
	go func() {
		ch = make(chan int, 1)
		ch <- 1
	}()
	go func(ch chan int) {
		time.Sleep(time.Second)
		<-ch
	}(ch)
	c := time.Tick(1 * time.Second)
	for range c {
		fmt.Printf("#goroutines: %d\n", runtime.NumGoroutine())
	}
}
```

- A: 不能编译
- B: 一段时间后总是输出 `#goroutines: 1`
- C: 一段时间后总是输出 `#goroutines: 2`
- D: panic

### 8 channel

```go
package main
import "fmt"
func main() {
	var ch chan int
	var count int
	go func() {
		ch <- 1
	}()
	go func() {
		count++
		close(ch)
	}()
	<-ch
	fmt.Println(count)
}
```

- A: 不能编译
- B: 输出 1
- C: 输出 0
- D: panic

### 9 Map

```go
package main
import (
	"fmt"
	"sync"
)
func main() {
	var m sync.Map
	m.LoadOrStore("a", 1)
	m.Delete("a")
	fmt.Println(m.Len())
}
```

- A: 不能编译
- B: 输出 1
- C: 输出 0
- D: panic

### 10 happens before

```go
package main
var c = make(chan int)
var a int
func f() {
	a = 1
	<-c
}
func main() {
	go f()
	c <- 0
	print(a)
}
```

- A: 不能编译
- B: 输出 1
- C: 输出 0
- D: panic





## 答案

### 1. D

会产生死锁`panic`，因为`Mutex` 是互斥锁。

### 2. D

会产生死锁`panic`，根据`sync/rwmutex.go` 中注释可以知道，读写锁当有一个协程在等待写锁时，其他协程是不能获得读锁的，而在`A`和`C`中同一个调用链中间需要让出读锁，让写锁优先获取，而`A`的读锁又要求`C`调用完成，因此死锁。


### 3. D

`WaitGroup` 在调用 `Wait` 之后是不能再调用 `Add` 方法的。

### 4. C

在多核CPU中，因为CPU缓存会导致多个核心中变量值不同步。

### 5. D

加锁后复制变量，会将锁的状态也复制，所以`mu1` 其实是已经加锁状态，再加锁会死锁。

### 6. C

个人理解，在单核CPU中，内存可能会稳定在`256MB`，如果是多核可能会暴涨。

### 7. C

因为 `ch` 未初始化，写和读都会阻塞，之后被第一个协程重新赋值，导致写的`ch` 都阻塞。

### 8. D

`ch` 未有被初始化，关闭时会报错。

### 9. A

`sync.Map` 没有 `Len` 方法。

### 10. B

`c <- 0` 会阻塞依赖于 `f()` 的执行。







# 记一道字节跳动的算法面试题

## 题目

这其实是一道变形的链表反转题，大致描述如下
给定一个单链表的头节点 head,实现一个调整单链表的函数，使得每K个节点之间为一组进行逆序，并且从链表的尾部开始组起，头部剩余节点数量不够一组的不需要逆序。（不能使用队列或者栈作为辅助）

**例如：**

链表:`1->2->3->4->5->6->7->8->null, K = 3`。那么 `6->7->8`，`3->4->5`，`1->2`各位一组。调整后：`1->2->5->4->3->8->7->6->null`。其中 1，2不调整，因为不够一组。

**解析**

原文： <https://juejin.im/post/5d4f76325188253b49244dd0># 多协程查询切片问题

## 题目

假设有一个超长的切片，切片的元素类型为int，切片中的元素为乱序排序。限时5秒，使用多个goroutine查找切片中是否存在给定的值，在查找到目标值或者超时后立刻结束所有goroutine的执行。

比如，切片 `[23,32,78,43,76,65,345,762,......915,86]`，查找目标值为 345 ，如果切片中存在，则目标值输出`"Found it!"`并立即取消仍在执行查询任务的`goroutine`。

如果在超时时间未查到目标值程序，则输出`"Timeout！Not Found"`，同时立即取消仍在执行的查找任务的`goroutine`。

> 答案: <https://mp.weixin.qq.com/s/GhC2WDw3VHP91DrrFVCnag>













# Goroutine调度策略

> 原文： [第三章 Goroutine调度策略（16）](https://mp.weixin.qq.com/s?__biz=MzU1OTg5NDkzOA==&mid=2247483801&idx=1&sn=ef7f872afccf148661cbd5a3d3b5b0a2&scene=19#wechat_redirect)


在调度器概述一节我们提到过，所谓的goroutine调度，是指程序代码按照一定的算法在适当的时候挑选出合适的goroutine并放到CPU上去运行的过程。这句话揭示了调度系统需要解决的三大核心问题：

- 调度时机：什么时候会发生调度？
- 调度策略：使用什么策略来挑选下一个进入运行的goroutine？
- 切换机制：如何把挑选出来的goroutine放到CPU上运行？

对这三大问题的解决构成了调度器的所有工作，因而我们对调度器的分析也必将围绕着它们所展开。

第二章我们已经详细的分析了调度器的初始化以及goroutine的切换机制，本章将重点讨论调度器如何挑选下一个goroutine出来运行的策略问题，而剩下的与调度时机相关的内容我们将在第4～6章进行全面的分析。

## 再探schedule函数

在讨论main goroutine的调度时我们已经见过schedule函数，因为当时我们的主要关注点在于main goroutine是如何被调度到CPU上运行的，所以并未对schedule函数如何挑选下一个goroutine出来运行做深入的分析，现在是重新回到schedule函数详细分析其调度策略的时候了。

[runtime/proc.go : 2467](https://github.com/golang/go/blob/bcda68447b31b86bc3829fca80454ca1a2a572e0/src/runtime/proc.go#L2551)

```go
// One round of scheduler: find a runnable goroutine and execute it.
// Never returns.
func schedule() {
    _g_ := getg()   //_g_ = m.g0

    ......

    var gp *g

    ......
   
    if gp == nil {
    // Check the global runnable queue once in a while to ensure fairness.
    // Otherwise two goroutines can completely occupy the local runqueue
    // by constantly respawning each other.
       //为了保证调度的公平性，每个工作线程每进行61次调度就需要优先从全局运行队列中获取goroutine出来运行，
       //因为如果只调度本地运行队列中的goroutine，则全局运行队列中的goroutine有可能得不到运行
        if _g_.m.p.ptr().schedtick%61 == 0 && sched.runqsize > 0 {
            lock(&sched.lock) //所有工作线程都能访问全局运行队列，所以需要加锁
            gp = globrunqget(_g_.m.p.ptr(), 1) //从全局运行队列中获取1个goroutine
            unlock(&sched.lock)
        }
    }
    if gp == nil {
        //从与m关联的p的本地运行队列中获取goroutine
        gp, inheritTime = runqget(_g_.m.p.ptr())
        if gp != nil && _g_.m.spinning {
            throw("schedule: spinning with local work")
        }
    }
    if gp == nil {
        //如果从本地运行队列和全局运行队列都没有找到需要运行的goroutine，
        //则调用findrunnable函数从其它工作线程的运行队列中偷取，如果偷取不到，则当前工作线程进入睡眠，
        //直到获取到需要运行的goroutine之后findrunnable函数才会返回。
        gp, inheritTime = findrunnable() // blocks until work is available
    }

    ......

    //当前运行的是runtime的代码，函数调用栈使用的是g0的栈空间
    //调用execte切换到gp的代码和栈空间去运行
    execute(gp, inheritTime)  
}
```

schedule函数分三步分别从各运行队列中寻找可运行的goroutine：

- 第一步，从全局运行队列中寻找goroutine。为了保证调度的公平性，每个工作线程每经过61次调度就需要优先尝试从全局运行队列中找出一个goroutine来运行，这样才能保证位于全局运行队列中的goroutine得到调度的机会。全局运行队列是所有工作线程都可以访问的，所以在访问它之前需要加锁。

- 第二步，从工作线程本地运行队列中寻找goroutine。如果不需要或不能从全局运行队列中获取到goroutine则从本地运行队列中获取。

- 第三步，从其它工作线程的运行队列中偷取goroutine。如果上一步也没有找到需要运行的goroutine，则调用findrunnable从其他工作线程的运行队列中偷取goroutine，findrunnable函数在偷取之前会再次尝试从全局运行队列和当前线程的本地运行队列中查找需要运行的goroutine。

下面我们先来看如何从全局运行队列中获取goroutine。

## 从全局运行队列中获取goroutine

从全局运行队列中获取可运行的goroutine是通过globrunqget函数来完成的，该函数的第一个参数是与当前工作线程绑定的p，第二个参数max表示最多可以从全局队列中拿多少个g到当前工作线程的本地运行队列中来。

[runtime/proc.go : 4663](https://github.com/golang/go/blob/bcda68447b31b86bc3829fca80454ca1a2a572e0/src/runtime/proc.go#L4996)

```go
// Try get a batch of G's from the global runnable queue.
// Sched must be locked.
func globrunqget(_p_ *p, max int32) *g {
    if sched.runqsize == 0 {  //全局运行队列为空
        return nil
    }

    //根据p的数量平分全局运行队列中的goroutines
    n := sched.runqsize / gomaxprocs + 1
    if n > sched.runqsize { //上面计算n的方法可能导致n大于全局运行队列中的goroutine数量
        n = sched.runqsize
    }
    if max > 0 && n > max {
        n = max   //最多取max个goroutine
    }
    if n > int32(len(_p_.runq)) / 2 {
        n = int32(len(_p_.runq)) / 2  //最多只能取本地队列容量的一半
    }

    sched.runqsize -= n

    //直接通过函数返回gp，其它的goroutines通过runqput放入本地运行队列
    gp := sched.runq.pop()  //pop从全局运行队列的队列头取
    n--
    for ; n > 0; n-- {
        gp1 := sched.runq.pop()  //从全局运行队列中取出一个goroutine
        runqput(_p_, gp1, false)  //放入本地运行队列
    }
    return gp
}
```

globrunqget函数首先会根据全局运行队列中goroutine的数量，函数参数max以及_p_的本地队列的容量计算出到底应该拿多少个goroutine，然后把第一个g结构体对象通过返回值的方式返回给调用函数，其它的则通过runqput函数放入当前工作线程的本地运行队列。这段代码值得一提的是，计算应该从全局运行队列中拿走多少个goroutine时根据p的数量（gomaxprocs）做了负载均衡。

如果没有从全局运行队列中获取到goroutine，那么接下来就在工作线程的本地运行队列中寻找需要运行的goroutine。

## 从工作线程本地运行队列中获取goroutine

从代码上来看，工作线程的本地运行队列其实分为两个部分，一部分是由p的runq、runqhead和runqtail这三个成员组成的一个无锁循环队列，该队列最多可包含256个goroutine；另一部分是p的runnext成员，它是一个指向g结构体对象的指针，它最多只包含一个goroutine。

从本地运行队列中寻找goroutine是通过`runqget`函数完成的，寻找时，代码首先查看`runnext`成员是否为空，如果不为空则返回runnext所指的goroutine，并把runnext成员清零，如果runnext为空，则继续从循环队列中查找goroutine。

[runtime/proc.go : 4825](https://github.com/golang/go/blob/bcda68447b31b86bc3829fca80454ca1a2a572e0/src/runtime/proc.go#L5192:1)

```go
// Get g from local runnable queue.
// If inheritTime is true, gp should inherit the remaining time in the
// current time slice. Otherwise, it should start a new time slice.
// Executed only by the owner P.
func runqget(_p_ *p) (gp *g, inheritTime bool) {
    // If there's a runnext, it's the next G to run.
    //从runnext成员中获取goroutine
    for {
        //查看runnext成员是否为空，不为空则返回该goroutine
        next := _p_.runnext  
        if next == 0 {
            break
        }
        if _p_.runnext.cas(next, 0) {
            return next.ptr(), true
        }
    }

    //从循环队列中获取goroutine
    for {
        h := atomic.LoadAcq(&_p_.runqhead) // load-acquire, synchronize with other consumers
        t := _p_.runqtail
        if t == h {
            return nil, false
        }
        gp := _p_.runq[h%uint32(len(_p_.runq))].ptr()
        if atomic.CasRel(&_p_.runqhead, h, h+1) { // cas-release, commits consume
            return gp, false
        }
    }
}
```

这里首先需要注意的是不管是从runnext还是从循环队列中拿取goroutine都使用了cas操作，这里的cas操作是必需的，因为可能有其他工作线程此时此刻也正在访问这两个成员，从这里偷取可运行的goroutine。

其次，代码中对runqhead的操作使用了`atomic.LoadAcq`和`atomic.CasRel`，它们分别提供了`load-acquire`和`cas-release`语义。

**对于atomic.LoadAcq来说，其语义主要包含如下几条：**

- 原子读取，也就是说不管代码运行在哪种平台，保证在读取过程中不会有其它线程对该变量进行写入；
- 位于`atomic.LoadAcq`之后的代码，对内存的读取和写入必须在`atomic.LoadAcq`读取完成后才能执行，编译器和CPU都不能打乱这个顺序；
- 当前线程执行`atomic.LoadAcq`时可以读取到其它线程最近一次通过`atomic.CasRel`对同一个变量写入的值，与此同时，位于`atomic.LoadAcq`之后的代码，不管读取哪个内存地址中的值，都可以读取到其它线程中位于atomic.CasRel（对同一个变量操作）之前的代码最近一次对内存的写入。

**对于atomic.CasRel来说，其语义主要包含如下几条：**

- 原子的执行比较并交换的操作；
- 位于`atomic.CasRel`之前的代码，对内存的读取和写入必须在`atomic.CasRel`对内存的写入之前完成，编译器和CPU都不能打乱这个顺序；
- 线程执行`atomic.CasRel`完成后其它线程通过`atomic.LoadAcq`读取同一个变量可以读到最新的值，与此同时，位于`atomic.CasRel`之前的代码对内存写入的值，可以被其它线程中位于`atomic.LoadAcq`（对同一个变量操作）之后的代码读取到。

因为可能有多个线程会并发的修改和读取`runqhead`，以及需要依靠runqhead的值来读取runq数组的元素，所以需要使用atomic.LoadAcq和atomic.CasRel来保证上述语义。

我们可能会问，为什么读取p的runqtail成员不需要使用atomic.LoadAcq或atomic.load？因为runqtail不会被其它线程修改，只会被当前工作线程修改，此时没有人修改它，所以也就不需要使用原子相关的操作。

最后，由`p`的`runq`、`runqhead`和`runqtail`这三个成员组成的这个无锁循环队列非常精妙，我们会在后面的章节对这个循环队列进行分析。

## CAS操作与ABA问题

我们知道使用cas操作需要特别注意ABA的问题，那么runqget函数这两个使用cas的地方会不会有问题呢？答案是这两个地方都不会有ABA的问题。原因分析如下：

首先来看对runnext的cas操作。只有跟_p_绑定的当前工作线程才会去修改runnext为一个非0值，其它线程只会把runnext的值从一个非0值修改为0值，然而跟_p_绑定的当前工作线程正在此处执行代码，所以在当前工作线程读取到值A之后，不可能有线程修改其值为B(0)之后再修改回A。

再来看对runq的cas操作。当前工作线程操作的是_p_的本地队列，只有跟_p_绑定在一起的当前工作线程才会因为往该队列里面添加goroutine而去修改runqtail，而其它工作线程不会往该队列里面添加goroutine，也就不会去修改runqtail，它们只会修改runqhead，所以，当我们这个工作线程从runqhead读取到值A之后，其它工作线程也就不可能修改runqhead的值为B之后再第二次把它修改为值A（因为runqtail在这段时间之内不可能被修改，runqhead的值也就无法越过runqtail再回绕到A值），也就是说，代码从逻辑上已经杜绝了引发ABA的条件。

到此，我们已经分析完工作线程从全局运行队列和本地运行队列获取goroutine的代码，由于篇幅的限制，我们下一节再来分析从其它工作线程的运行队列偷取goroutine的流程。






































# 对已经关闭的的chan进行读写，会怎么样？为什么？

## 题目

对已经关闭的的 chan 进行读写，会怎么样？为什么？

## 回答

- 读已经关闭的 chan 能一直读到东西，但是读到的内容根据通道内关闭前是否有元素而不同。
    - 如果 chan 关闭前，buffer 内有元素还未读 , 会正确读到 chan 内的值，且返回的第二个 bool 值（是否读成功）为 true。
    - 如果 chan 关闭前，buffer 内有元素已经被读完，chan 内无值，接下来所有接收的值都会非阻塞直接成功，返回 channel 元素的零值，但是第二个 bool 值一直为 false。
- 写已经关闭的 chan 会 panic


## 示例

### 1. 写已经关闭的 chan

```go
func main(){
    c := make(chan int,3)
    close(c)
    c <- 1
}
//输出结果
panic: send on closed channel

goroutine 1 [running]
main.main()
...
```

- 注意这个 send on closed channel，待会会提到。

### 2. 读已经关闭的 chan

```go
package main
import "fmt"

func main()  {
    fmt.Println("以下是数值的chan")
    ci:=make(chan int,3)
    ci<-1
    close(ci)
    num,ok := <- ci
    fmt.Printf("读chan的协程结束，num=%v， ok=%v\n",num,ok)
    num1,ok1 := <-ci
    fmt.Printf("再读chan的协程结束，num=%v， ok=%v\n",num1,ok1)
    num2,ok2 := <-ci
    fmt.Printf("再再读chan的协程结束，num=%v， ok=%v\n",num2,ok2)
    
    fmt.Println("以下是字符串chan")
    cs := make(chan string,3)
    cs <- "aaa"
    close(cs)
    str,ok := <- cs
    fmt.Printf("读chan的协程结束，str=%v， ok=%v\n",str,ok)
    str1,ok1 := <-cs
    fmt.Printf("再读chan的协程结束，str=%v， ok=%v\n",str1,ok1)
    str2,ok2 := <-cs
    fmt.Printf("再再读chan的协程结束，str=%v， ok=%v\n",str2,ok2)

    fmt.Println("以下是结构体chan")
    type MyStruct struct{
        Name string
    }
    cstruct := make(chan MyStruct,3)
    cstruct <- MyStruct{Name: "haha"}
    close(cstruct)
    stru,ok := <- cstruct
    fmt.Printf("读chan的协程结束，stru=%v， ok=%v\n",stru,ok)
    stru1,ok1 := <-cs
    fmt.Printf("再读chan的协程结束，stru=%v， ok=%v\n",stru1,ok1)
    stru2,ok2 := <-cs
    fmt.Printf("再再读chan的协程结束，stru=%v， ok=%v\n",stru2,ok2)
}

```

输出结果

```bash
以下是数值的chan
读chan的协程结束，num=1， ok=true
再读chan的协程结束，num=0， ok=false
再再读chan的协程结束，num=0， ok=false
以下是字符串chan
读chan的协程结束，str=aaa， ok=true
再读chan的协程结束，str=， ok=false
再再读chan的协程结束，str=， ok=false
以下是结构体chan
读chan的协程结束，stru={haha}， ok=true
再读chan的协程结束，stru=， ok=false
再再读chan的协程结束，stru=， ok=false
```


## 多问一句

### 1. 为什么写已经关闭的 `chan` 就会 `panic` 呢？

```go
//在 src/runtime/chan.go
func chansend(c *hchan,ep unsafe.Pointer,block bool,callerpc uintptr) bool {
    //省略其他
    if c.closed != 0 {
        unlock(&c.lock)
        panic(plainError("send on closed channel"))
    }   
    //省略其他
}
```

- 当 `c.closed != 0` 则为通道关闭，此时执行写，源码提示直接 `panic`，输出的内容就是上面提到的 `"send on closed channel"`。

### 2. 为什么读已关闭的 chan 会一直能读到值？

```go
func chanrecv(c *hchan,ep unsafe.Pointer,block bool) (selected,received bool) {
    //省略部分逻辑
    lock(&c.lock)
    //当chan被关闭了，而且缓存为空时
    //ep 是指 val,ok := <-c 里的val地址
    if c.closed != 0 && c.qcount == 0 {
        if receenabled {
            raceacquire(c.raceaddr())
        }
        unlock(&c.lock)
        //如果接受之的地址不空，那接收值将获得一个该值类型的零值
        //typedmemclr 会根据类型清理响应的内存
        //这就解释了上面代码为什么关闭的chan 会返回对应类型的零值
        if ep != null {
            typedmemclr(c.elemtype,ep)
        }   
        //返回两个参数 selected,received
        // 第二个采纳数就是 val,ok := <- c 里的 ok
        //也就解释了为什么读关闭的chan会一直返回false
        return true,false
    }   
}
```
- `c.closed != 0 && c.qcount == 0` 指通道已经关闭，且缓存为空的情况下（已经读完了之前写到通道里的值）
- 如果接收值的地址 `ep` 不为空
    - 那接收值将获得是一个该类型的零值
    - `typedmemclr` 会根据类型清理相应地址的内存
    - 这就解释了上面代码为什么关闭的 chan 会返回对应类型的零值





# 简单聊聊内存逃逸？

## 问题

知道golang的内存逃逸吗？什么情况下会发生内存逃逸？

## 回答

golang程序变量会携带有一组校验数据，用来证明它的整个生命周期是否在运行时完全可知。如果变量通过了这些校验，它就可以在栈上分配。否则就说它 逃逸 了，必须在堆上分配。

能引起变量逃逸到堆上的典型情况：

- **在方法内把局部变量指针返回** 局部变量原本应该在栈中分配，在栈中回收。但是由于返回时被外部引用，因此其生命周期大于栈，则溢出。
- **发送指针或带有指针的值到 channel 中。**  在编译时，是没有办法知道哪个 `goroutine` 会在 `channel` 上接收数据。所以编译器没法知道变量什么时候才会被释放。
- **在一个切片上存储指针或带指针的值。** 一个典型的例子就是 `[]*string` 。这会导致切片的内容逃逸。尽管其后面的数组可能是在栈上分配的，但其引用的值一定是在堆上。
- **slice 的背后数组被重新分配了，因为 append 时可能会超出其容量( cap )。** slice 初始化的地方在编译时是可以知道的，它最开始会在栈上分配。如果切片背后的存储要基于运行时的数据进行扩充，就会在堆上分配。
- **在 interface 类型上调用方法。**  在 interface 类型上调用方法都是动态调度的 —— 方法的真正实现只能在运行时知道。想像一个 io.Reader 类型的变量 r , 调用 r.Read(b) 会使得 r 的值和切片b 的背后存储都逃逸掉，所以会在堆上分配。

## 举例

**通过一个例子加深理解，接下来尝试下怎么通过 `go build -gcflags=-m` 查看逃逸的情况。**

```go
package main
import "fmt"
type A struct {
 s string
}
// 这是上面提到的 "在方法内把局部变量指针返回" 的情况
func foo(s string) *A {
 a := new(A) 
 a.s = s
 return a //返回局部变量a,在C语言中妥妥野指针，但在go则ok，但a会逃逸到堆
}
func main() {
 a := foo("hello")
 b := a.s + " world"
 c := b + "!"
 fmt.Println(c)
}
```

执行go build -gcflags=-m main.go

```bash
go build -gcflags=-m main.go
# command-line-arguments
./main.go:7:6: can inline foo
./main.go:13:10: inlining call to foo
./main.go:16:13: inlining call to fmt.Println
/var/folders/45/qx9lfw2s2zzgvhzg3mtzkwzc0000gn/T/go-build409982591/b001/_gomod_.go:6:6: can inline init.0
./main.go:7:10: leaking param: s
./main.go:8:10: new(A) escapes to heap
./main.go:16:13: io.Writer(os.Stdout) escapes to heap
./main.go:16:13: c escapes to heap
./main.go:15:9: b + "!" escapes to heap
./main.go:13:10: main new(A) does not escape
./main.go:14:11: main a.s + " world" does not escape
./main.go:16:13: main []interface {} literal does not escape
<autogenerated>:1: os.(*File).close .this does not escape
```

- `./main.go:8:10: new(A) escapes to heap` 说明 `new(A)` 逃逸了,符合上述提到的常见情况中的第一种。
- `./main.go:14:11: main a.s + " world" does not escape` 说明 b 变量没有逃逸，因为它只在方法内存在，会在方法结束时被回收。
- `./main.go:15:9: b + "!" escapes to heap` 说明 c 变量逃逸，通过`fmt.Println(a ...interface{})`打印的变量，都会发生逃逸，感兴趣的朋友可以去查查为什么。

以上操作其实就叫逃逸分析。下篇文章，跟大家聊聊怎么用一个比较trick的方法使变量不逃逸。方便大家在面试官面前秀一波。


>原文 <https://mp.weixin.qq.com/s/4YYR1eYFIFsNOaTxL4Q-eQ>








# 字符串转成byte数组，会发生内存拷贝吗？

## 问题

字符串转成byte数组，会发生内存拷贝吗？

## 回答

字符串转成切片，会产生拷贝。严格来说，只要是发生类型强转都会发生内存拷贝。那么问题来了。

频繁的内存拷贝操作听起来对性能不大友好。有没有什么办法可以在字符串转成切片的时候不用发生拷贝呢？

## 解释

```go
package main

import (
 "fmt"
 "reflect"
 "unsafe"
)

func main() {
 a :="aaa"
 ssh := *(*reflect.StringHeader)(unsafe.Pointer(&a))
 b := *(*[]byte)(unsafe.Pointer(&ssh))  
 fmt.Printf("%v",b)
}
```

**`StringHeader` 是字符串在go的底层结构。**

```go
type StringHeader struct {
 Data uintptr
 Len  int
}
```

**`SliceHeader` 是切片在go的底层结构。**

```go
type SliceHeader struct {
 Data uintptr
 Len  int
 Cap  int
}
```

那么如果想要在底层转换二者，只需要把 StringHeader 的地址强转成 SliceHeader 就行。那么go有个很强的包叫 unsafe 。

1. `unsafe.Pointer(&a)`方法可以得到变量a的地址。
2. `(*reflect.StringHeader)(unsafe.Pointer(&a))` 可以把字符串a转成底层结构的形式。
3. `(*[]byte)(unsafe.Pointer(&ssh))` 可以把ssh底层结构体转成byte的切片的指针。
4. 再通过 `*`转为指针指向的实际内容。
















# http包的内存泄漏

## 问题

```go
package main

import (
	"fmt"
	"io/ioutil"
	"net/http"
	"runtime"
)

func main() {
	num := 6
	for index := 0; index < num; index++ {
		resp, _ := http.Get("https://www.baidu.com")
		_, _ = ioutil.ReadAll(resp.Body)
	}
	fmt.Printf("此时goroutine个数= %d\n", runtime.NumGoroutine())


}
```

上面这道题在不执行`resp.Body.Close()`的情况下，泄漏了吗？如果泄漏，泄漏了多少个goroutine?

## 怎么答

不进行resp.Body.Close()，泄漏是一定的。但是泄漏的goroutine个数就让我迷糊了。由于执行了6遍，每次泄漏一个读和写goroutine，就是12个goroutine，加上main函数本身也是一个goroutine，所以答案是13.
然而执行程序，发现答案是3，出入有点大，为什么呢？

## 解释

我们直接看源码。golang 的 http 包。

```go
http.Get()

-- DefaultClient.Get
----func (c *Client) do(req *Request)
------func send(ireq *Request, rt RoundTripper, deadline time.Time)
-------- resp, didTimeout, err = send(req, c.transport(), deadline) 
// 以上代码在 go/1.12.7/libexec/src/net/http/client:174 

func (c *Client) transport() RoundTripper {
	if c.Transport != nil {
		return c.Transport
	}
	return DefaultTransport
}
```

- 说明 `http.Get` 默认使用 `DefaultTransport` 管理连接。

DefaultTransport 是干嘛的呢？

```go
// It establishes network connections as needed
// and caches them for reuse by subsequent calls.
```

- `DefaultTransport` 的作用是根据需要建立网络连接并缓存它们以供后续调用重用。

那么 `DefaultTransport` 什么时候会建立连接呢？

接着上面的代码堆栈往下翻

```go
func send(ireq *Request, rt RoundTripper, deadline time.Time) 
--resp, err = rt.RoundTrip(req) // 以上代码在 go/1.12.7/libexec/src/net/http/client:250
func (t *Transport) RoundTrip(req *http.Request)
func (t *Transport) roundTrip(req *Request)
func (t *Transport) getConn(treq *transportRequest, cm connectMethod)
func (t *Transport) dialConn(ctx context.Context, cm connectMethod) (*persistConn, error) {
    ...
	go pconn.readLoop()  // 启动一个读goroutine
	go pconn.writeLoop() // 启动一个写goroutine
	return pconn, nil
}
```

- 一次建立连接，就会启动一个读goroutine和写goroutine。这就是为什么一次`http.Get()`会泄漏两个goroutine的来源。
- 泄漏的来源知道了，也知道是因为没有执行close

**那为什么不执行 close 会泄漏呢？**

回到刚刚启动的读goroutine 的 `readLoop()` 代码里

```go
func (pc *persistConn) readLoop() {
	alive := true
	for alive {
        ...
		// Before looping back to the top of this function and peeking on
		// the bufio.Reader, wait for the caller goroutine to finish
		// reading the response body. (or for cancelation or death)
		select {
		case bodyEOF := <-waitForBodyRead:
			pc.t.setReqCanceler(rc.req, nil) // before pc might return to idle pool
			alive = alive &&
				bodyEOF &&
				!pc.sawEOF &&
				pc.wroteRequest() &&
				tryPutIdleConn(trace)
			if bodyEOF {
				eofc <- struct{}{}
			}
		case <-rc.req.Cancel:
			alive = false
			pc.t.CancelRequest(rc.req)
		case <-rc.req.Context().Done():
			alive = false
			pc.t.cancelRequest(rc.req, rc.req.Context().Err())
		case <-pc.closech:
			alive = false
        }
        ...
	}
}
```

其中第一个 body 被读取完或关闭这个 case:

```go
alive = alive &&
    bodyEOF &&
    !pc.sawEOF &&
    pc.wroteRequest() &&
    tryPutIdleConn(trace)

```

bodyEOF 来源于到一个通道 waitForBodyRead，这个字段的 true 和 false 直接决定了 alive 变量的值（alive=true那读goroutine继续活着，循环，否则退出goroutine）。

**那么这个通道的值是从哪里过来的呢？**


```go
// go/1.12.7/libexec/src/net/http/transport.go: 1758
		body := &bodyEOFSignal{
			body: resp.Body,
			earlyCloseFn: func() error {
				waitForBodyRead <- false
				<-eofc // will be closed by deferred call at the end of the function
				return nil

			},
			fn: func(err error) error {
				isEOF := err == io.EOF
				waitForBodyRead <- isEOF
				if isEOF {
					<-eofc // see comment above eofc declaration
				} else if err != nil {
					if cerr := pc.canceled(); cerr != nil {
						return cerr
					}
				}
				return err
			},
		}
```

- 如果执行 earlyCloseFn ，waitForBodyRead 通道输入的是 false，alive 也会是 false，那 readLoop() 这个 goroutine 就会退出。
- 如果执行 fn ，其中包括正常情况下 body 读完数据抛出 io.EOF 时的 case，waitForBodyRead 通道输入的是 true，那 alive 会是 true，那么 readLoop() 这个 goroutine 就不会退出，同时还顺便执行了 tryPutIdleConn(trace) 。

```go
// tryPutIdleConn adds pconn to the list of idle persistent connections awaiting
// a new request.
// If pconn is no longer needed or not in a good state, tryPutIdleConn returns
// an error explaining why it wasn't registered.
// tryPutIdleConn does not close pconn. Use putOrCloseIdleConn instead for that.
func (t *Transport) tryPutIdleConn(pconn *persistConn) error
```

- tryPutIdleConn 将 pconn 添加到等待新请求的空闲持久连接列表中，也就是之前说的连接会复用。

那么问题又来了，什么时候会执行这个 `fn` 和 `earlyCloseFn` 呢？

```go
func (es *bodyEOFSignal) Close() error {
	es.mu.Lock()
	defer es.mu.Unlock()
	if es.closed {
		return nil
	}
	es.closed = true
	if es.earlyCloseFn != nil && es.rerr != io.EOF {
		return es.earlyCloseFn() // 关闭时执行 earlyCloseFn
	}
	err := es.body.Close()
	return es.condfn(err)
}
```

- 上面这个其实就是我们比较收悉的 resp.Body.Close() ,在里面会执行 earlyCloseFn，也就是此时 readLoop() 里的 waitForBodyRead 通道输入的是 false，alive 也会是 false，那 readLoop() 这个 goroutine 就会退出，goroutine 不会泄露。
  
```go
b, err = ioutil.ReadAll(resp.Body)
--func ReadAll(r io.Reader) 
----func readAll(r io.Reader, capacity int64) 
------func (b *Buffer) ReadFrom(r io.Reader)


// go/1.12.7/libexec/src/bytes/buffer.go:207
func (b *Buffer) ReadFrom(r io.Reader) (n int64, err error) {
	for {
		...
		m, e := r.Read(b.buf[i:cap(b.buf)])  // 看这里，是body在执行read方法
		...
	}
}
```

- 这个`read`，其实就是 `bodyEOFSignal` 里的

```go
func (es *bodyEOFSignal) Read(p []byte) (n int, err error) {
	...
	n, err = es.body.Read(p)
	if err != nil {
		... 
    // 这里会有一个io.EOF的报错，意思是读完了
		err = es.condfn(err)
	}
	return
}


func (es *bodyEOFSignal) condfn(err error) error {
	if es.fn == nil {
		return err
	}
	err = es.fn(err)  // 这了执行了 fn
	es.fn = nil
	return err
}
```

- 上面这个其实就是我们比较收悉的读取 body 里的内容。 ioutil.ReadAll() ,在读完 body 的内容时会执行 fn，也就是此时 readLoop() 里的 waitForBodyRead 通道输入的是 true，alive 也会是 true，那 readLoop() 这个 goroutine 就不会退出，goroutine 会泄露，然后执行 tryPutIdleConn(trace) 把连接放回池子里复用。
  
## 总结

- 所以结论呼之欲出了，虽然执行了 6 次循环，而且每次都没有执行 Body.Close() ,就是因为执行了ioutil.ReadAll()把内容都读出来了，连接得以复用，因此只泄漏了一个读goroutine和一个写goroutine，最后加上main goroutine，所以答案就是3个goroutine。
- 从另外一个角度说，正常情况下我们的代码都会执行 ioutil.ReadAll()，但如果此时忘了 resp.Body.Close()，确实会导致泄漏。但如果你调用的域名一直是同一个的话，那么只会泄漏一个 读goroutine 和一个写goroutine，这就是为什么代码明明不规范但却看不到明显内存泄漏的原因。
- 那么问题又来了，为什么上面要特意强调是同一个域名呢？改天，回头，以后有空再说吧。


>作者：9號同学
链接：https://juejin.cn/post/6896993332019822605
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。


作者：9號同学
链接：https://juejin.cn/post/6896993332019822605
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。


# sync.Map 的用法

## 问题

```go
package main

import (
	"fmt"
	"sync"
)

func main(){
	var m sync.Map
	m.Store("address",map[string]string{"province":"江苏","city":"南京"})
        v,_ := m.Load("address")
	fmt.Println(v["province"]) 
}
```

- A，江苏；
- B`，v["province"]`取值错误；
- C，`m.Store`存储错误；
- D，不知道

## 解析

`invalid operation: v["province"] (type interface {} does not support indexing)`
因为 `func (m *Map) Store(key interface{}, value interface{})`
所以 `v`类型是 `interface {}` ，这里需要一个类型断言

```go
fmt.Println(v.(map[string]string)["province"]) //江苏
```
<!-- TOC -->

- [MongoDB](#mongodb)
  - [1.MongoDB中对多条记录做更新操作命令是什么？](#1mongodb%e4%b8%ad%e5%af%b9%e5%a4%9a%e6%9d%a1%e8%ae%b0%e5%bd%95%e5%81%9a%e6%9b%b4%e6%96%b0%e6%93%8d%e4%bd%9c%e5%91%bd%e4%bb%a4%e6%98%af%e4%bb%80%e4%b9%88)
  - [2.MongoDB如何才会拓展到多个shard里？](#2mongodb%e5%a6%82%e4%bd%95%e6%89%8d%e4%bc%9a%e6%8b%93%e5%b1%95%e5%88%b0%e5%a4%9a%e4%b8%aashard%e9%87%8c)

<!-- /TOC -->

## MongoDB
### 1.MongoDB中对多条记录做更新操作命令是什么？
### 2.MongoDB如何才会拓展到多个shard里？
# 为什么MySQL使用B+树做索引？

索引这个词，相信大多数人已经相当熟悉了，很多人都知道MySQL的索引主要以B+树为主，但是要问到为什么用B+树，恐怕很少有人能把前因后果讲述的很完整。本文就来从头到尾介绍下数据库的索引。

索引是一种数据结构，用于帮助我们在大量数据中快速定位到我们想要查找的数据。 索引最形象的比喻就是图书的目录了。注意这里的大量，数据量大了索引才显得有意义，如果我想要在[1,2,3,4]中找到4这个数据，直接对全数据检索也很快，没有必要费力气建索引再去查找。索引在mysql数据库中分三类： 

B+树索引、Hash索引、全文索引

我们今天要介绍的是工作开发中最常接触到innodb存储引擎中的的B+树索引。

要介绍B+树索引，就不得不提二叉查找树，平衡二叉树和B树这三种数据结构。B+树就是从他们仨演化来的。

## 二叉查找树

首先，让我们先看一张图

![](../images/640.jpeg)

从图中可以看到，我们为user表（用户信息表）建立了一个二叉查找树的索引。图中的圆为二叉查找树的节点，节点中存储了键(key)和数据(data)。

键对应user表中的id，数据对应user表中的行数据。二叉查找树的特点就是任何节点的左子节点的键值都小于当前节点的键值，右子节点的键值都大于当前节点的键值。 顶端的节点我们称为根节点，没有子节点的节点我们称之为叶节点。 


如果我们需要查找id=12的用户信息，利用我们创建的二叉查找树索引，查找流程如下： 

1. 将根节点作为当前节点，把12与当前节点的键值10比较，12大于10，接下来我们把当前节点>的右子节点作为当前节点。 
2. 继续把12和当前节点的键值13比较，发现12小于13，把当前节点的左子节点作为当前节点。 
3. 把12和当前节点的键值12对比，12等于12，满足条件，我们从当前节点中取出data，即id=12,name=xm。

利用二叉查找树我们只需要3次即可找到匹配的数据。如果在表中一条条的查找的话，我们需要6次才能找到。


## 平衡二叉树

上面我们讲解了利用二叉查找树可以快速的找到数据。但是，如果上面的二叉查找树是这样的构造：

![](../images/Wohph0xohfa9.jpg)

这个时候可以看到我们的二叉查找树变成了一个链表。

如果我们需要查找id=17的用户信息，我们需要查找7次，也就相当于全表扫描了。 

导致这个现象的原因其实是二叉查找树变得不平衡了，也就是高度太高了，从而导致查找效率的不稳定。

为了解决这个问题，我们需要保证二叉查找树一直保持平衡，就需要用到平衡二叉树了。 


平衡二叉树又称AVL树，在满足二叉查找树特性的基础上，要求每个节点的左右子树的高度差不能超过1。 

下面是平衡二叉树和非平衡二叉树的对比：

![](../images/Shu2ohcohb4j.jpg)


由平衡二叉树的构造我们可以发现第一张图中的二叉树其实就是一棵平衡二叉树。

平衡二叉树保证了树的构造是平衡的，当我们插入或删除数据导致不满足平衡二叉树不平衡时，平衡二叉树会进行调整树上的节点来保持平衡。具体的调整方式这里就不介绍了。

平衡二叉树相比于二叉查找树来说，查找效率更稳定，总体的查找速度也更快。


## B树

因为内存的易失性。一般情况下，我们都会选择将user表中的数据和索引存储在磁盘这种外围设备中。

但是和内存相比，从磁盘中读取数据的速度会慢上百倍千倍甚至万倍，所以，我们应当尽量减少从磁盘中读取数据的次数。 另外，从磁盘中读取数据时，都是按照磁盘块来读取的，并不是一条一条的读。 

如果我们能把尽量多的数据放进磁盘块中，那一次磁盘读取操作就会读取更多数据，那我们查找数据的时间也会大幅度降低。 

如果我们用树这种数据结构作为索引的数据结构，那我们每查找一次数据就需要从磁盘中读取一个节点，也就是我们说的一个磁盘块，我们都知道平衡二叉树可是每个节点只存储一个键值和数据的。

那说明什么？

说明每个磁盘块仅仅存储一个键值和数据！

那如果我们要存储海量的数据呢？

可以想象到二叉树的节点将会非常多，高度也会及其高，我们查找数据时也会进行很多次磁盘IO，我们查找数据的效率将会极低！

![](../images/ahwu4aihuo1H.jpg)

为了解决平衡二叉树的这个弊端，我们应该寻找一种单个节点可以存储多个键值和数据的平衡树。也就是我们接下来要说的B树。 

B树（Balance Tree）即为平衡树的意思，下图即是一颗B树。

![](../images/iey3Weim7thu.jpg)

图中的p节点为指向子节点的指针，二叉查找树和平衡二叉树其实也有，因为图的美观性，被省略了。- 图中的每个节点称为页，页就是我们上面说的磁盘块，在mysql中数据读取的基本单位都是页，所以我们这里叫做页更符合mysql中索引的底层数据结构。

从上图可以看出，B树相对于平衡二叉树，每个节点存储了更多的键值(key)和数据(data)，并且每个节点拥有更多的子节点，子节点的个数一般称为阶，上述图中的B树为3阶B树，高度也会很低。 
基于这个特性，B树查找数据读取磁盘的次数将会很少，数据的查找效率也会比平衡二叉树高很多。 

假如我们要查找id=28的用户信息，那么我们在上图B树中查找的流程如下： 

1. 先找到根节点也就是页1，判断28在键值17和35之间，我们那么我们根据页1中的指针p2找到页3。 
2. 将28和页3中的键值相比较，28在26和30之间，我们根据页3中的指针p2找到页8。 
3. 将28和页8中的键值相比较，发现有匹配的键值28，键值28对应的用户信息为(28,bv)。

## B+树

B+树是对B树的进一步优化。让我们先来看下B+树的结构图：

![](../images/deiGoh6JiePh.webp)

根据上图我们来看下B+树和B树有什么不同。 

1. B+树非叶子节点上是不存储数据的，仅存储键值，而B树节点中不仅存储键值，也会存储数据。之所以这么做是因为在数据库中页的大小是固定的，innodb中页的默认大小是16KB。如果不存储数据，那么就会存储更多的键值，相应的树的阶数（节点的子节点树）就会更大，树就会更矮更胖，如此一来我们查找数据进行磁盘的IO次数有会再次减少，数据查询的效率也会更快。另外，B+树的阶数是等于键值的数量的，如果我们的B+树一个节点可以存储1000个键值，那么3层B+树可以存储1000×1000×1000=10亿个数据。一般根节点是常驻内存的，所以一般我们查找10亿数据，只需要2次磁盘IO。 
2. 因为B+树索引的所有数据均存储在叶子节点，而且数据是按照顺序排列的。那么B+树使得范围查找，排序查找，分组查找以及去重查找变得异常简单。而B树因为数据分散在各个节点，要实现这一点是很不容易的。  

有心的读者可能还发现上图B+树中各个页之间是通过双向链表连接的，叶子节点中的数据是通过单向链表连接的。
其实上面的B树我们也可以对各个节点加上链表。其实这些不是它们之前的区别，是因为在mysql的innodb存储引擎中，索引就是这样存储的。也就是说上图中的B+树索引就是innodb中B+树索引真正的实现方式，准确的说应该是聚集索引（聚集索引和非聚集索引下面会讲到）。
通过上图可以看到，在innodb中，我们通过数据页之间通过双向链表连接以及叶子节点中数据之间通过单向链表连接的方式可以找到表中所有的数据。
MyISAM中的B+树索引实现与innodb中的略有不同。在MyISAM中，B+树索引的叶子节点并不存储数据，而是存储数据的文件地址。

## 聚集索引 VS 非聚集索引

在上节介绍B+树索引的时候，我们提到了图中的索引其实是聚集索引的实现方式。那什么是聚集索引呢？
在MySQL中，B+树索引按照存储方式的不同分为聚集索引和非聚集索引。
这里我们着重介绍innodb中的聚集索引和非聚集索引。

1. 聚集索引（聚簇索引）：以innodb作为存储引擎的表，表中的数据都会有一个主键，即使你不创建主键，系统也会帮你创建一个隐式的主键。这是因为innodb是把数据存放在B+树中的，而B+树的键值就是主键，在B+树的叶子节点中，存储了表中所有的数据。这种以主键作为B+树索引的键值而构建的B+树索引，我们称之为聚集索引。 
2. 非聚集索引（非聚簇索引）：以主键以外的列值作为键值构建的B+树索引，我们称之为非聚集索引。非聚集索引与聚集索引的区别在于非聚集索引的叶子节点不存储表中的数据，而是存储该列对应的主键，想要查找数据我们还需要根据主键再去聚集索引中进行查找，这个再根据聚集索引查找数据的过程，我们称为回表。  

明白了聚集索引和非聚集索引的定义，我们应该明白这样一句话：数据即索引，索引即数据。

### 利用聚集索引和非聚集索引查找数据

前面我们讲解B+树索引的时候并没有去说怎么在B+树中进行数据的查找，主要就是因为还没有引出聚集索引和非聚集索引的概念。下面我们通过讲解如何通过聚集索引以及非聚集索引查找数据表中数据的方式介绍一下B+树索引查找数据方法。
利用聚集索引查找数据

![](../images/zaTh5uayeeT1.webp)

还是这张B+树索引图，现在我们应该知道这就是聚集索引，表中的数据存储在其中。现在假设我们要查找id>=18并且id<40的用户数据。对应的sql语句为select * from user where id>=18 and id <40，其中id为主键。具体的查找过程如下：

1. 一般根节点都是常驻内存的，也就是说页1已经在内存中了，此时不需要到磁盘中读取数据，直接从内存中读取即可。
    从内存中读取到页1，要查找这个id>=18 and id <40或者范围值，我们首先需要找到id=18的键值。
    从页1中我们可以找到键值18，此时我们需要根据指针p2，定位到页3。

2. 要从页3中查找数据，我们就需要拿着p2指针去磁盘中进行读取页3。
    从磁盘中读取页3后将页3放入内存中，然后进行查找，我们可以找到键值18，然后再拿到页3中的指针p1，定位到页8。

3. 同样的页8页不在内存中，我们需要再去磁盘中将页8读取到内存中。
    将页8读取到内存中后。因为页中的数据是链表进行连接的，而且键值是按照顺序存放的，此时可以根据二分查找法定位到键值18。
    此时因为已经到数据页了，此时我们已经找到一条满足条件的数据了，就是键值18对应的数据。
    因为是范围查找，而且此时所有的数据又都存在叶子节点，并且是有序排列的，那么我们就可以对页8中的键值依次进行遍历查找并匹配满足条件的数据。
    我们可以一直找到键值为22的数据，然后页8中就没有数据了，此时我们需要拿着页8中的p指针去读取页9中的数据。

4. 因为页9不在内存中，就又会加载页9到内存中，并通过和页8中一样的方式进行数据的查找，直到将页12加载到内存中，发现41大于40，此时不满足条件。那么查找到此终止。
    最终我们找到满足条件的所有数据为：`(18,kl),(19,kl),(22,hj),(24,io),(25,vg),(29,jk),(31,jk),(33,rt),(34,ty),(35,yu),(37,rt),(39,rt)` 总共12条记录。


下面看下具体的查找流程图：

![](../images/ieHie2ur2ooh.webp)

利用非聚集索引查找数据

![](../images/FiaChoSij8ej.webp)

读者看到这张图的时候可能会蒙，这是啥东西啊？怎么都是数字。
如果有这种感觉，请仔细看下图中红字的解释。什么？还看不懂？那我再来解释下吧。首先，这个非聚集索引表示的是用户幸运数字的索引（为什么是幸运数字？一时兴起想起来的:-)），此时表结构是这样的。

|id	|name|	luckyNum|
|----|----|----|
1|	zs|	23
2|	ls|	7

在叶子节点中，不在存储所有的数据了，存储的是键值和主键。

对于叶子节点中的x-y，比如1-1。左边的1表示的是索引的键值，右边的1表示的是主键值。如果我们要找到幸运数字为33的用户信息，对应的sql语句为select * from user where luckNum=33。

查找的流程跟聚集索引一样，这里就不详细介绍了。我们最终会找到主键值47，找到主键后我们需要再到聚集索引中查找具体对应的数据信息，此时又回到了聚集索引的查找流程。  

下面看下具体的查找流程图：

![](../images/giethaD1Eice.webp)

在MyISAM中，聚集索引和非聚集索引的叶子节点都会存储数据的文件地址。

## 总结

本篇文从二叉查找树，详细说明了为什么mysql用B+树作为数据的索引，以及在innodb中数据库如何通过B+树索引来存储数据以及查找数据。我们一定要记住这句话：数据即索引，索引即数据。

> 原文： [再有人问你为什么MySQL用B+树做索引，就把这篇文章发给她](https://mp.weixin.qq.com/s?__biz=MzIwOTE2MzU4NA==&mid=2247484085&idx=1&sn=92639430ac7ef3e412b550a09bde0115&chksm=9779469aa00ecf8c157e899fe0d5c5b060b282a4e5f2f2f63c187eb3c04d04ef6fad7a1e09e3&token=472896045&lang=zh_CN#rd)
















# MySQL数据库经典面试题解析

## 1. MySQL 索引使用有哪些注意事项呢？

可以从三个维度回答这个问题：索引哪些情况会失效，索引不适合哪些场景，索引规则

### 索引哪些情况会失效

- 查询条件包含or，可能导致索引失效
- 如何字段类型是字符串，where时一定用引号括起来，否则索引失效
- like通配符可能导致索引失效。
- 联合索引，查询时的条件列不是联合索引中的第一个列，索引失效。
- 在索引列上使用mysql的内置函数，索引失效。
- 对索引列运算（如，+、-、*、/），索引失效。
- 索引字段上使用（！= 或者 < >，not in）时，可能会导致索引失效。
- 索引字段上使用is null， is not null，可能导致索引失效。
- 左连接查询或者右连接查询查询关联的字段编码格式不一样，可能导致索引失效。
- mysql估计使用全表扫描要比使用索引快,则不使用索引。

### 索引不适合哪些场景

- 数据量少的不适合加索引
- 更新比较频繁的也不适合加索引
- 区分度低的字段不适合加索引（如性别）

### 索引的一些潜规则

- 覆盖索引
- 回表
- 索引数据结构（B+树）
- 最左前缀原则
- 索引下推

## 2. MySQL 遇到过死锁问题吗，你是如何解决的？

我排查死锁的一般步骤是酱紫的：

- 查看死锁日志show engine innodb status;
- 找出死锁Sql
- 分析sql加锁情况
- 模拟死锁案发
- 分析死锁日志
- 分析死锁结果

## 3. 日常工作中你是怎么优化SQL的？

可以从这几个维度回答这个问题：

- 加索引
- 避免返回不必要的数据
- 适当分批量进行
- 优化sql结构
- 分库分表
- 读写分离

## 4. 说说分库与分表的设计

分库分表方案，分库分表中间件，分库分表可能遇到的问题

### 分库分表方案:

- 水平分库：以字段为依据，按照一定策略（hash、range等），将一个库中的数据拆分到多个库中。
- 水平分表：以字段为依据，按照一定策略（hash、range等），将一个表中的数据拆分到多个表中。
- 垂直分库：以表为依据，按照业务归属不同，将不同的表拆分到不同的库中。
- 垂直分表：以字段为依据，按照字段的活跃性，将表中字段拆到不同的表（主表和扩展表）中。

### 常用的分库分表中间件：

- sharding-jdbc（当当）
- Mycat
- TDDL（淘宝）
- Oceanus(58同城数据库中间件)
- vitess（谷歌开发的数据库中间件）
- Atlas(Qihoo 360)

### 分库分表可能遇到的问题

- 事务问题：需要用分布式事务啦
- 跨节点Join的问题：解决这一问题可以分两次查询实现
- 跨节点的count,order by,group by以及聚合函数问题：分别在各个节点上得到结果后在应用程序端进行合并。
- 数据迁移，容量规划，扩容等问题
- ID问题：数据库被切分后，不能再依赖数据库自身的主键生成机制啦，最简单可以考虑UUID
- 跨分片的排序分页问题（后台加大pagesize处理？）

## 5. InnoDB与MyISAM的区别

- InnoDB支持事务，MyISAM不支持事务
- InnoDB支持外键，MyISAM不支持外键
- InnoDB 支持 MVCC(多版本并发控制)，MyISAM 不支持
- `select count(*) from table`时，MyISAM更快，因为它有一个变量保存了整个表的总行数，可以直接读取，InnoDB就需要全表扫描。
- Innodb不支持全文索引，而MyISAM支持全文索引（5.7以后的InnoDB也支持全文索引）
- InnoDB支持表、行级锁，而MyISAM支持表级锁。
- InnoDB表必须有主键，而MyISAM可以没有主键
- Innodb表需要更多的内存和存储，而MyISAM可被压缩，存储空间较小，。
- Innodb按主键大小有序插入，MyISAM记录插入顺序是，按记录插入顺序保存。
- InnoDB 存储引擎提供了具有提交、回滚、崩溃恢复能力的事务安全，与 MyISAM 比 InnoDB 写的效率差一些，并且会占用更多的磁盘空间以保留数据和索引
- InnoDB 属于索引组织表，使用共享表空间和多表空间储存数据。MyISAM用`.frm`、`.MYD`、`.MTI`来储存表定义，数据和索引。

## 6. 数据库索引的原理，为什么要用 B+树，为什么不用二叉树？

可以从几个维度去看这个问题，查询是否够快，效率是否稳定，存储数据多少，以及查找磁盘次数，为什么不是二叉树，为什么不是平衡二叉树，为什么不是B树，而偏偏是B+树呢？

### 为什么不是一般二叉树？

如果二叉树特殊化为一个链表，相当于全表扫描。平衡二叉树相比于二叉查找树来说，查找效率更稳定，总体的查找速度也更快。

### 为什么不是平衡二叉树呢？

我们知道，在内存比在磁盘的数据，查询效率快得多。如果树这种数据结构作为索引，那我们每查找一次数据就需要从磁盘中读取一个节点，也就是我们说的一个磁盘块，但是平衡二叉树可是每个节点只存储一个键值和数据的，如果是B树，可以存储更多的节点数据，树的高度也会降低，因此读取磁盘的次数就降下来啦，查询效率就快啦。

### 那为什么不是B树而是B+树呢？

- 1）B+树非叶子节点上是不存储数据的，仅存储键值，而B树节点中不仅存储键值，也会存储数据。innodb中页的默认大小是16KB，如果不存储数据，那么就会存储更多的键值，相应的树的阶数（节点的子节点树）就会更大，树就会更矮更胖，如此一来我们查找数据进行磁盘的IO次数有会再次减少，数据查询的效率也会更快。
- 2）B+树索引的所有数据均存储在叶子节点，而且数据是按照顺序排列的，链表连着的。那么B+树使得范围查找，排序查找，分组查找以及去重查找变得异常简单。


## 7. 聚集索引与非聚集索引的区别

- 一个表中只能拥有一个聚集索引，而非聚集索引一个表可以存在多个。
- 聚集索引，索引中键值的逻辑顺序决定了表中相应行的物理顺序；非聚集索引，索引中索引的逻辑顺序与磁盘上行的物理存储顺序不同。
- 索引是通过二叉树的数据结构来描述的，我们可以这么理解聚簇索引：索引的叶节点就是数据节点。而非聚簇索引的叶节点仍然是索引节点，只不过有一个指针指向对应的数据块。
- 聚集索引：物理存储按照索引排序；非聚集索引：物理存储不按照索引排序；

何时使用聚集索引或非聚集索引？

![](../images/172346f5e5f0ffab.jpg)


## 8. limit 1000000 加载很慢的话，你是怎么解决的呢？

方案一：如果id是连续的，可以这样，返回上次查询的最大记录(偏移量)，再往下limit

```sql
select id，name from employee where id>1000000 limit 10.
```

方案二：在业务允许的情况下限制页数：

建议跟业务讨论，有没有必要查这么后的分页啦。因为绝大多数用户都不会往后翻太多页。

方案三：order by + 索引（id为索引）

```sql
select id，name from employee order by id  limit 1000000，10
```

```sql
SELECT a.* FROM employee a, (select id from employee where 条件 LIMIT 1000000,10 ) b where a.id=b.id
```

方案四：利用延迟关联或者子查询优化超多分页场景。（先快速定位需要获取的id段，然后再关联）

## 9. 如何选择合适的分布式主键方案呢？

- 数据库自增长序列或字段。
- UUID。
- Redis生成ID
- Twitter的snowflake算法
- 利用zookeeper生成唯一ID
- MongoDB的ObjectId

## 10. 事务的隔离级别有哪些？MySQL的默认隔离级别是什么？

- 读未提交（Read Uncommitted）
- 读已提交（Read Committed）
- 可重复读（Repeatable Read）
- 串行化（Serializable）

Mysql默认的事务隔离级别是可重复读(Repeatable Read)

## 11. 什么是幻读，脏读，不可重复读呢？

- 事务A、B交替执行，事务A被事务B干扰到了，因为事务A读取到事务B未提交的数据,这就是脏读
- 在一个事务范围内，两个相同的查询，读取同一条记录，却返回了不同的数据，这就是不可重复读。
- 事务A查询一个范围的结果集，另一个并发事务B往这个范围中插入/删除了数据，并静悄悄地提交，然后事务A再次查询相同的范围，两次读取得到的结果集不一样了，这就是幻读。

## 12. 在高并发情况下，如何做到安全的修改同一行数据？

要安全的修改同一行数据，就要保证一个线程在修改时其它线程无法更新这行记录。一般有悲观锁和乐观锁两种方案~

### 使用悲观锁

悲观锁思想就是，当前线程要进来修改数据时，别的线程都得拒之门外~
比如，可以使用select…for update ~

```sql
select * from User where name=‘jay’ for update
```

以上这条sql语句会锁定了User表中所有符合检索条件（name=‘jay’）的记录。本次事务提交之前，别的线程都无法修改这些记录。

### 使用乐观锁

乐观锁思想就是，有线程过来，先放过去修改，如果看到别的线程没修改过，就可以修改成功，如果别的线程修改过，就修改失败或者重试。实现方式：乐观锁一般会使用版本号机制或CAS算法实现。

## 13. 数据库的乐观锁和悲观锁。

### 悲观锁：

悲观锁她专一且缺乏安全感了，她的心只属于当前事务，每时每刻都担心着它心爱的数据可能被别的事务修改，所以一个事务拥有（获得）悲观锁后，其他任何事务都不能对数据进行修改啦，只能等待锁被释放才可以执行。

![](../images/1723d313056fce6a.jpg)

### 乐观锁：

乐观锁的“乐观情绪”体现在，它认为数据的变动不会太频繁。因此，它允许多个事务同时对数据进行变动。实现方式：乐观锁一般会使用版本号机制或CAS算法实现。

![](../images/1723e7d3872c9406.jpg)

## 14. SQL优化的一般步骤是什么，怎么看执行计划（explain），如何理解其中各个字段的含义。

- `show status` 命令了解各种 sql 的执行频率
- 通过慢查询日志定位那些执行效率较低的 sql 语句
- `explain` 分析低效 sql 的执行计划（这点非常重要，日常开发中用它分析Sql，会大大降低Sql导致的线上事故）

## 15. select for update有什么含义，会锁表还是锁行还是其他。

### select for update 含义

select查询语句是不会加锁的，但是select for update除了有查询的作用外，还会加锁呢，而且它是悲观锁哦。至于加了是行锁还是表锁，这就要看是不是用了索引/主键啦。
没用索引/主键的话就是表锁，否则就是是行锁。

## 16. MySQL事务得四大特性以及实现原理

![](../images/17237f45a661cbb7.jpg)

- 原子性： 事务作为一个整体被执行，包含在其中的对数据库的操作要么全部被执行，要么都不执行。
- 一致性： 指在事务开始之前和事务结束以后，数据不会被破坏，假如A账户给B账户转10块钱，不管成功与否，A和B的总金额是不变的。
- 隔离性： 多个事务并发访问时，事务之间是相互隔离的，即一个事务不影响其它事务运行效果。简言之，就是事务之间是进水不犯河水的。
- 持久性： 表示事务完成以后，该事务对数据库所作的操作更改，将持久地保存在数据库之中。

### 事务ACID特性的实现思想

- 原子性：是使用 undo log来实现的，如果事务执行过程中出错或者用户执行了rollback，系统通过undo log日志返回事务开始的状态。
- 持久性：使用 redo log来实现，只要redo log日志持久化了，当系统崩溃，即可通过redo log把数据恢复。
- 隔离性：通过锁以及MVCC,使事务相互隔离开。
- 一致性：通过回滚、恢复，以及并发情况下的隔离性，从而实现一致性。

## 17. 如果某个表有近千万数据，CRUD比较慢，如何优化。

### 分库分表

某个表有近千万数据，可以考虑优化表结构，分表（水平分表，垂直分表），当然，你这样回答，需要准备好面试官问你的分库分表相关问题呀，如

- 分表方案（水平分表，垂直分表，切分规则hash等）
- 分库分表中间件（Mycat，sharding-jdbc等）
- 分库分表一些问题（事务问题？跨节点Join的问题）
- 解决方案（分布式事务等）

### 索引优化

除了分库分表，优化表结构，当然还有所以索引优化等方案~

## 18. 如何写sql能够有效的使用到复合索引。

复合索引，也叫组合索引，用户可以在多个列上建立索引,这种索引叫做复合索引。

当我们创建一个组合索引的时候，如(k1,k2,k3)，相当于创建了（k1）、(k1,k2)和(k1,k2,k3)三个索引，这就是最左匹配原则。

```sql
select * from table where k1=A AND k2=B AND k3=D 
```

有关于复合索引，我们需要关注查询Sql条件的顺序，确保最左匹配原则有效，同时可以删除不必要的冗余索引。


## 19. mysql中in 和exists的区别。

这个，跟一下demo来看更刺激吧，啊哈哈

假设表A表示某企业的员工表，表B表示部门表，查询所有部门的所有员工，很容易有以下SQL:

```sql
select * from A where deptId in (select deptId from B);
```

这样写等价于：

>先查询部门表B
>select deptId from B
>再由部门deptId，查询A的员工
> select * from A where A.deptId = B.deptId

可以抽象成这样的一个循环：

```sql
   List<> resultSet ;
    for(int i=0;i<B.length;i++) {
          for(int j=0;j<A.length;j++) {
          if(A[i].id==B[j].id) {
             resultSet.add(A[i]);
             break;
          }
       }
    }
```

显然，除了使用in，我们也可以用exists实现一样的查询功能，如下：

```sql
select * from A where exists (select 1 from B where A.deptId = B.deptId); 
```

因为exists查询的理解就是，先执行主查询，获得数据后，再放到子查询中做条件验证，根据验证结果（true或者false），来决定主查询的数据结果是否得意保留。

那么，这样写就等价于：

> select * from A,先从A表做循环
> select * from B where A.deptId = B.deptId,再从B表做循环.

同理，可以抽象成这样一个循环：

```java 
 List<> resultSet ;
    for(int i=0;i<A.length;i++) {
          for(int j=0;j<B.length;j++) {
          if(A[i].deptId==B[j].deptId) {
             resultSet.add(A[i]);
             break;
          }
       }
    }
```

数据库最费劲的就是跟程序链接释放。假设链接了两次，每次做上百万次的数据集查询，查完就走，这样就只做了两次；相反建立了上百万次链接，申请链接释放反复重复，这样系统就受不了了。即mysql优化原则，就是小表驱动大表，小的数据集驱动大的数据集，从而让性能更优。
因此，我们要选择最外层循环小的，也就是，如果B的数据量小于A，适合使用in，如果B的数据量大于A，即适合选择exists，这就是in和exists的区别。

## 20. 数据库自增主键可能遇到什么问题。

使用自增主键对数据库做分库分表，可能出现诸如主键重复等的问题。解决方案的话，简单点的话可以考虑使用UUID哈
自增主键会产生表锁，从而引发问题
自增主键可能用完问题。

## 21. MVCC熟悉吗，它的底层原理？

MVCC,多版本并发控制,它是通过读取历史版本的数据，来降低并发事务冲突，从而提高并发性能的一种机制。

### MVCC需要关注这几个知识点：

- 事务版本号
- 表的隐藏列
- undo log
- read view

## 22. 数据库中间件了解过吗，sharding jdbc，mycat？

sharding-jdbc目前是基于jdbc驱动，无需额外的proxy，因此也无需关注proxy本身的高可用。
Mycat 是基于 Proxy，它复写了 MySQL 协议，将 Mycat Server 伪装成一个 MySQL 数据库，而 Sharding-JDBC 是基于 JDBC 接口的扩展，是以 jar 包的形式提供轻量级服务的。

## 23. MYSQL的主从延迟，你怎么解决？

嘻嘻，先复习一下主从复制原理吧，如图：

![](../images/1723fa894b85ed73.jpg)

主从复制分了五个步骤进行：

- 步骤一：主库的更新事件(update、insert、delete)被写到binlog
- 步骤二：从库发起连接，连接到主库。
- 步骤三：此时主库创建一个binlog dump thread，把binlog的内容发送到从库。
- 步骤四：从库启动之后，创建一个I/O线程，读取主库传过来的binlog内容并写入到relay log
- 步骤五：还会创建一个SQL线程，从relay log里面读取内容，从Exec_Master_Log_Pos位置开始执行读取到的更新事件，将更新内容写入到slave的db

### 主从同步延迟的原因

一个服务器开放Ｎ个链接给客户端来连接的，这样有会有大并发的更新操作, 但是从服务器的里面读取binlog的线程仅有一个，当某个SQL在从服务器上执行的时间稍长 或者由于某个SQL要进行锁表就会导致，主服务器的SQL大量积压，未被同步到从服务器里。这就导致了主从不一致， 也就是主从延迟。

### 主从同步延迟的解决办法

- 主服务器要负责更新操作，对安全性的要求比从服务器要高，所以有些设置参数可以修改，比如sync_binlog=1，innodb_flush_log_at_trx_commit = 1 之类的设置等。
- 选择更好的硬件设备作为slave。
- 把一台从服务器当度作为备份使用， 而不提供查询， 那边他的负载下来了， 执行relay log 里面的SQL效率自然就高了。
- 增加从服务器喽，这个目的还是分散读的压力，从而降低服务器负载。

## 24. 说一下大表查询的优化方案

- 优化shema、sql语句+索引；
- 可以考虑加缓存，memcached, redis，或者JVM本地缓存；
- 主从复制，读写分离；
- 分库分表；

## 25. 什么是数据库连接池?为什么需要数据库连接池呢?

### 连接池基本原理：

数据库连接池原理：在内部对象池中，维护一定数量的数据库连接，并对外暴露数据库连接的获取和返回方法。

### 应用程序和数据库建立连接的过程：

- 通过TCP协议的三次握手和数据库服务器建立连接
- 发送数据库用户账号密码，等待数据库验证用户身份
- 完成身份验证后，系统可以提交SQL语句到数据库执行
- 把连接关闭，TCP四次挥手告别。

### 数据库连接池好处：

- 资源重用 (连接复用)
- 更快的系统响应速度
- 新的资源分配手段
 统一的连接管理，避免数据库连接泄漏

## 26. 一条SQL语句在MySQL中如何执行的？

先看一下Mysql的逻辑架构图吧~

![](../images/1724037179201fe9.jpg)

### 查询语句：

- 先检查该语句是否有权限
- 如果没有权限，直接返回错误信息
- 如果有权限，在 MySQL8.0 版本以前，会先查询缓存。
- 如果没有缓存，分析器进行词法分析，提取 sql 语句select等的关键元素。然后判断sql 语句是否有语法错误，比如关键词是否正确等等。
- 优化器进行确定执行方案
- 进行权限校验，如果没有权限就直接返回错误信息，如果有权限就会调用数据库引擎接口，返回执行结果。

## 27. InnoDB引擎中的索引策略，了解过吗？

- 覆盖索引
- 最左前缀原则
- 索引下推
- 索引下推优化是 MySQL 5.6 引入的， 可以在索引遍历过程中，对索引中包含的字段先做判断，直接过滤掉不满足条件的记录，减少回表次数。

## 28. 数据库存储日期格式时，如何考虑时区转换问题？

- datetime类型适合用来记录数据的原始的创建时间，修改记录中其他字段的值，datetime字段的值不会改变，除非手动修改它。
- timestamp类型适合用来记录数据的最后修改时间，只要修改了记录中其他字段的值，timestamp字段的值都会被自动更新。

## 29. 一条sql执行过长的时间，你如何优化，从哪些方面入手？

- 查看是否涉及多表和子查询，优化Sql结构，如去除冗余字段，是否可拆表等
- 优化索引结构，看是否可以适当添加索引
- 数量大的表，可以考虑进行分离/分表（如交易流水表）
- 数据库主从分离，读写分离
- explain分析sql语句，查看执行计划，优化sql
- 查看mysql执行日志，分析是否有其他方面的问题

## 30. MYSQL数据库服务器性能分析的方法命令有哪些?

- Show status, 一些值得监控的变量值：

> Bytes_received和Bytes_sent 和服务器之间来往的流量。
>  Com_*服务器正在执行的命令。
>  Created_*在查询执行期限间创建的临时表和文件。
>  Handler_*存储引擎操作。
>  Select_*不同类型的联接执行计划。
>  Sort_*几种排序信息。
  
- Show profiles 是MySql用来分析当前会话SQL语句执行的资源消耗情况

## 31. Blob和text有什么区别？

- Blob用于存储二进制数据，而Text用于存储大字符串。
- Blob值被视为二进制字符串（字节字符串）,它们没有字符集，并且排序和比较基于列值中的字节的数值。
- text值被视为非二进制字符串（字符字符串）。它们有一个字符集，并根据字符集的排序规则对值进行排序和比较。

## 32.  mysql里记录货币用什么字段类型比较好？

- 货币在数据库中MySQL常用Decimal和Numric类型表示，这两种类型被MySQL实现为同样的类型。他们被用于保存与金钱有关的数据。
- salary DECIMAL(9,2)，9(precision)代表将被用于存储值的总的小数位数，而2(scale)代表将被用于存储小数点后的位数。存储在salary列中的值的范围是从-9999999.99到9999999.99。
- DECIMAL和NUMERIC值作为字符串存储，而不是作为二进制浮点数，以便保存那些值的小数精度。

## 33.  Mysql中有哪几种锁，列举一下？

![](../images/17240a13fa147bea.jpg)

如果按锁粒度划分，有以下3种：

- 表锁： 开销小，加锁快；锁定力度大，发生锁冲突概率高，并发度最低;不会出现死锁。
- 行锁： 开销大，加锁慢；会出现死锁；锁定粒度小，发生锁冲突的概率低，并发度高。
- 页锁： 开销和加锁速度介于表锁和行锁之间；会出现死锁；锁定粒度介于表锁和行锁之间，并发度一般

## 34.  Hash索引和B+树区别是什么？你在设计索引是怎么抉择的？

- B+树可以进行范围查询，Hash索引不能。
- B+树支持联合索引的最左侧原则，Hash索引不支持。
- B+树支持order by排序，Hash索引不支持。
- Hash索引在等值查询上比B+树效率更高。
- B+树使用like 进行模糊查询的时候，like后面（比如%开头）的话可以起到优化的作用，Hash索引根本无法进行模糊查询。

## 35.  mysql 的内连接、左连接、右连接有什么区别？

- Inner join 内连接，在两张表进行连接查询时，只保留两张表中完全匹配的结果集
- left join 在两张表进行连接查询时，会返回左表所有的行，即使在右表中没有匹配的记录。
- right join 在两张表进行连接查询时，会返回右表所有的行，即使在左表中没有匹配的记录。

## 36.  说说MySQL 的基础架构图

![](../images/17240afafdc289e5.jpg)

Mysql逻辑架构图主要分三层：

- 第一层负责连接处理，授权认证，安全等等
- 第二层负责编译并优化SQL
- 第三层是存储引擎。

## 37.  什么是内连接、外连接、交叉连接、笛卡尔积呢？

- 内连接（inner join）：取得两张表中满足存在连接匹配关系的记录。
- 外连接（outer join）：取得两张表中满足存在连接匹配关系的记录，以及某张表（或两张表）中不满足匹配关系的记录。
- 交叉连接（cross join）：显示两张表所有记录一一对应，没有匹配关系进行筛选，也被称为：笛卡尔积。

## 38.  说一下数据库的三大范式

- 第一范式：数据表中的每一列（每个字段）都不可以再拆分。
- 第二范式：在第一范式的基础上，分主键列完全依赖于主键，而不能是依赖于主键的一部分。
- 第三范式：在满足第二范式的基础上，表中的非主键只依赖于主键，而不依赖于其他非主键。

## 39.  mysql有关权限的表有哪几个呢？

MySQL服务器通过权限表来控制用户对数据库的访问，权限表存放在mysql数据库里，由mysql_install_db脚本初始化。这些权限表分别user，db，table_priv，columns_priv和host。

- user权限表：记录允许连接到服务器的用户帐号信息，里面的权限是全局级的。
- db权限表：记录各个帐号在各个数据库上的操作权限。
- table_priv权限表：记录数据表级的操作权限。
- columns_priv权限表：记录数据列级的操作权限。
- host权限表：配合db权限表对给定主机上数据库级操作权限作更细致的控制。这个权限表不受GRANT和REVOKE语句的影响。

## 40.  Mysql的binlog有几种录入格式？分别有什么区别？

有三种格式哈，statement，row和mixed。

- statement，每一条会修改数据的sql都会记录在binlog中。不需要记录每一行的变化，减少了binlog日志量，节约了IO，提高性能。由于sql的执行是有上下文的，因此在保存的时候需要保存相关的信息，同时还有一些使用了函数之类的语句无法被记录复制。
- row，不记录sql语句上下文相关信息，仅保存哪条记录被修改。记录单元为每一行的改动，基本是可以全部记下来但是由于很多操作，会导致大量行的改动(比如alter table)，因此这种模式的文件保存的信息太多，日志量太大。
- mixed，一种折中的方案，普通操作使用statement记录，当无法使用statement的时候使用row。


## 41.  InnoDB引擎的4大特性，了解过吗

- 插入缓冲（insert buffer)
- 二次写(double write)
- 自适应哈希索引(ahi)
- 预读(read ahead)

## 42.  索引有哪些优缺点？

**优点：**

- 唯一索引可以保证数据库表中每一行的数据的唯一性
- 索引可以加快数据查询速度，减少查询时间

**缺点：**

- 创建索引和维护索引要耗费时间
- 索引需要占物理空间，除了数据表占用数据空间之外，每一个索引还要占用一定的物理空间
- 以表中的数据进行增、删、改的时候，索引也要动态的维护。

## 43.  索引有哪几种类型？

- 主键索引: 数据列不允许重复，不允许为NULL，一个表只能有一个主键。
- 唯一索引: 数据列不允许重复，允许为NULL值，一个表允许多个列创建唯一索引。
- 普通索引: 基本的索引类型，没有唯一性的限制，允许为NULL值。
- 全文索引：是目前搜索引擎使用的一种关键技术，对文本的内容进行分词、搜索。
- 覆盖索引：查询列要被所建的索引覆盖，不必读取数据行
- 组合索引：多列值组成一个索引，用于组合搜索，效率大于索引合并

## 44.  创建索引有什么原则呢？

- 最左前缀匹配原则
- 频繁作为查询条件的字段才去创建索引
- 频繁更新的字段不适合创建索引
- 索引列不能参与计算，不能有函数操作
- 优先考虑扩展索引，而不是新建索引，避免不必要的索引
- 在order by或者group by子句中，创建索引需要注意顺序
- 区分度低的数据列不适合做索引列(如性别）
- 定义有外键的数据列一定要建立索引。
- 对于定义为text、image数据类型的列不要建立索引。
- 删除不再使用或者很少使用的索引

## 45.  创建索引的三种方式

在执行CREATE TABLE时创建索引

```sql
CREATE TABLE `employee` (
  `id` int(11) NOT NULL,
  `name` varchar(255) DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  `date` datetime DEFAULT NULL,
  `sex` int(1) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `idx_name` (`name`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

使用ALTER TABLE命令添加索引

```sql
ALTER TABLE table_name ADD INDEX index_name (column);
```

使用CREATE INDEX命令创建

```sql
CREATE INDEX index_name ON table_name (column);
```

## 46. 百万级别或以上的数据，你是如何删除的？

- 我们想要删除百万数据的时候可以先删除索引
- 然后批量删除其中无用数据
- 删除完成后重新创建索引。

## 47. 什么是最左前缀原则？什么是最左匹配原则？

- 最左前缀原则，就是最左优先，在创建多列索引时，要根据业务需求，where子句中使用最频繁的一列放在最左边。
- 当我们创建一个组合索引的时候，如(k1,k2,k3)，相当于创建了（k1）、(k1,k2)和(k1,k2,k3)三个索引，这就是最左匹配原则。。

## 48.  B树和B+树的区别，数据库为什么使用B+树而不是B树？

- 在B树中，键和值即存放在内部节点又存放在叶子节点；在B+树中，内部节点只存键，叶子节点则同时存放键和值。
- B+树的叶子节点有一条链相连，而B树的叶子节点各自独立的。

- B+树索引的所有数据均存储在叶子节点，而且数据是按照顺序排列的，链表连着的。那么B+树使得范围查找，排序查找，分组查找以及去重查找变得异常简单。.
- B+树非叶子节点上是不存储数据的，仅存储键值，而B树节点中不仅存储键值，也会存储数据。innodb中页的默认大小是16KB，如果不存储数据，那么就会存储更多的键值，相应的树的阶数（节点的子节点树）就会更大，树就会更矮更胖，如此一来我们查找数据进行磁盘的IO次数有会再次减少，数据查询的效率也会更快.


## 49.  覆盖索引、回表等这些，了解过吗？

- 覆盖索引： 查询列要被所建的索引覆盖，不必从数据表中读取，换句话说查询列要被所使用的索引覆盖。
- 回表：二级索引无法直接查询所有列的数据，所以通过二级索引查询到聚簇索引后，再查询到想要的数据，这种通过二级索引查询出来的过程，就叫做回表。

## 50.  B+树在满足聚簇索引和覆盖索引的时候不需要回表查询数据？

- 在B+树的索引中，叶子节点可能存储了当前的key值，也可能存储了当前的key值以及整行的数据，这就是聚簇索引和非聚簇索引。 在InnoDB中，只有主键索引是聚簇索引，如果没有主键，则挑选一个唯一键建立聚簇索引。如果没有唯一键，则隐式的生成一个键来建立聚簇索引。
- 当查询使用聚簇索引时，在对应的叶子节点，可以获取到整行数据，因此不用再次进行回表查询。

## 51. 何时使用聚簇索引与非聚簇索引

| 动作描述 | 使用聚集索引 | 使用非聚集索引 |
| ------ | ------ |------ |
|列经常被分组排序| 应 | 应|
|返回某范围内的数据| 应 |不应|
|一个或极少不同值 | 不应 | 不应 | 
|小数目的不同值|应|不应
|大数目的不同值|不应|应
|频繁更新的列|不应|应
|外键列|应|应|
|主键列|应|应|
|频繁修改索引列|不应|应|

## 52.  非聚簇索引一定会回表查询吗？

不一定，如果查询语句的字段全部命中了索引，那么就不必再进行回表查询（哈哈，覆盖索引就是这么回事）。

举个简单的例子，假设我们在学生表的上建立了索引，那么当进行`select age from student where age < 20`的查询时，在索引的叶子节点上，已经包含了age信息，不会再次进行回表查询。

## 53. 组合索引是什么？为什么需要注意组合索引中的顺序？

组合索引，用户可以在多个列上建立索引,这种索引叫做组合索引。
因为InnoDB引擎中的索引策略的最左原则，所以需要注意组合索引中的顺序。

## 54.  什么是数据库事务？

数据库事务（简称：事务），是数据库管理系统执行过程中的一个逻辑单位，由一个有限的数据库操作序列构成，这些操作要么全部执行,要么全部不执行，是一个不可分割的工作单位。

## 55.  隔离级别与锁的关系

回答这个问题，可以先阐述四种隔离级别，再阐述它们的实现原理。隔离级别就是依赖锁和MVCC实现的。

## 56. 按照锁的粒度分，数据库锁有哪些呢？锁机制与InnoDB锁算法

![](../images/1724129062f57007.jpg)

- 按锁粒度分有：表锁，页锁，行锁
- 按锁机制分有：乐观锁，悲观锁

## 57.  从锁的类别角度讲，MySQL都有哪些锁呢？

从锁的类别上来讲，有共享锁和排他锁。

- 共享锁: 又叫做读锁。当用户要进行数据的读取时，对数据加上共享锁。共享锁可以同时加上多个。
- 排他锁: 又叫做写锁。当用户要进行数据的写入时，对数据加上排他锁。排他锁只可以加一个，他和其他的排他锁，共享锁都相斥。

锁兼容性如下：

![](../images/172412db1d202759.jpg)

## 58. MySQL中InnoDB引擎的行锁是怎么实现的？

基于索引来完成行锁的。

```sql
select * from t where id = 666 for update;
```

for update 可以根据条件来完成行锁锁定，并且 id 是有索引键的列，如果 id 不是索引键那么InnoDB将实行表锁。

## 59.  什么是死锁？怎么解决？

死锁是指两个或多个事务在同一资源上相互占用，并请求锁定对方的资源，从而导致恶性循环的现象。看图形象一点，如下：

![](../images/17241317342078b9.jpg)

死锁有四个必要条件：互斥条件，请求和保持条件，环路等待条件，不剥夺条件。
解决死锁思路，一般就是切断环路，尽量避免并发形成环路。

- 如果不同程序会并发存取多个表，尽量约定以相同的顺序访问表，可以大大降低死锁机会。
- 在同一个事务中，尽可能做到一次锁定所需要的所有资源，减少死锁产生概率；
- 对于非常容易产生死锁的业务部分，可以尝试使用升级锁定颗粒度，通过表级锁定来减少死锁产生的概率；
- 如果业务处理不好可以用分布式事务锁或者使用乐观锁
- 死锁与索引密不可分，解决索引问题，需要合理优化你的索引，

## 60.  为什么要使用视图？什么是视图？

### 为什么要使用视图？

为了提高复杂SQL语句的复用性和表操作的安全性，MySQL数据库管理系统提供了视图特性。

### 什么是视图？

视图是一个虚拟的表，是一个表中的数据经过某种筛选后的显示方式，视图由一个预定义的查询select语句组成。

## 61.  视图有哪些特点？哪些使用场景？

### 视图特点：

- 视图的列可以来自不同的表，是表的抽象和在逻辑意义上建立的新关系。
- 视图是由基本表(实表)产生的表(虚表)。
- 视图的建立和删除不影响基本表。
- 对视图内容的更新(添加，删除和修改)直接影响基本表。
- 当视图来自多个基本表时，不允许添加和删除数据。

**视图用途：** 简化sql查询，提高开发效率，兼容老的表结构。

### 视图的常见使用场景：

- 重用SQL语句；
- 简化复杂的SQL操作。
- 使用表的组成部分而不是整个表；
- 保护数据
- 更改数据格式和表示。视图可返回与底层表的表示和格式不同的数据。

## 62.  视图的优点，缺点，讲一下？

- 查询简单化。视图能简化用户的操作
- 数据安全性。视图使用户能以多种角度看待同一数据，能够对机密数据提供安全保护
- 逻辑数据独立性。视图对重构数据库提供了一定程度的逻辑独立性

## 63.  count(1)、count(*) 与 count(列名) 的区别？

- count(*)包括了所有的列，相当于行数，在统计结果的时候，不会忽略列值为NULL
- count(1)包括了忽略所有列，用1代表代码行，在统计结果的时候，不会忽略列值为NULL
- count(列名)只包括列名那一列，在统计结果的时候，会忽略列值为空（这里的空不是只空字符串或者0，而是表示null）的计数，即某个字段值为NULL时，不统计。

## 64.  什么是游标？

游标提供了一种对从表中检索出的数据进行操作的灵活手段，就本质而言，游标实际上是一种能从包括多条数据记录的结果集中每次提取一条记录的机制。

## 65.  什么是存储过程？有哪些优缺点？

存储过程，就是一些编译好了的SQL语句，这些SQL语句代码像一个方法一样实现一些功能（对单表或多表的增删改查），然后给这些代码块取一个名字，在用到这个功能的时候调用即可。

**优点：**

- 存储过程是一个预编译的代码块，执行效率比较高
- 存储过程在服务器端运行，减少客户端的压力
- 允许模块化程序设计，只需要创建一次过程，以后在程序中就可以调用该过程任意次，类似方法的复用
-一个存储过程替代大量T_SQL语句 ，可以降低网络通信量，提高通信速率
- 可以一定程度上确保数据安全

**缺点：**

- 调试麻烦
- 可移植性不灵活
- 重新编译问题

## 66.  什么是触发器？触发器的使用场景有哪些？

触发器，指一段代码，当触发某个事件时，自动执行这些代码。

**使用场景：**

- 可以通过数据库中的相关表实现级联更改。
- 实时监控某张表中的某个字段的更改而需要做出相应的处理。
- 例如可以生成某些业务的编号。
- 注意不要滥用，否则会造成数据库及应用程序的维护困难。

## 67.   MySQL中都有哪些触发器？

MySQL 数据库中有六种触发器：

- Before Insert
- After Insert
- Before Update
- After Update
- Before Delete
- After Delete

## 68.   超键、候选键、主键、外键分别是什么？

- 超键：在关系模式中，能唯一知标识元组的属性集称为超键。
- 候选键：是最小超键，即没有冗余元素的超键。
- 主键：数据库表中对储存数据对象予以唯一和完整标识的数据列或属性的组合。一个数据列只能有一个主键，且主键的取值不能缺失，即不能为空值（Null）。
- 外键：在一个表中存在的另一个表的主键称此表的外键。。

## 69.   SQL 约束有哪几种呢？

- NOT NULL: 约束字段的内容一定不能为NULL。
- UNIQUE: 约束字段唯一性，一个表允许有多个 Unique 约束。
- PRIMARY KEY: 约束字段唯一，不可重复，一个表只允许存在一个。
- FOREIGN KEY: 用于预防破坏表之间连接的动作，也能防止非法数据插入外键。
- CHECK: 用于控制字段的值范围。

## 70.   谈谈六种关联查询，使用场景。

- 交叉连接
- 内连接
- 外连接
- 联合查询
- 全连接
- 交叉连接

## 71.  varchar(50)中50的涵义

字段最多存放 50 个字符
如 varchar(50) 和 varchar(200) 存储 "jay" 字符串所占空间是一样的，后者在排序时会消耗更多内存

## 72.   mysql中int(20)和char(20)以及varchar(20)的区别

- int(20) 表示字段是int类型，显示长度是 20
- char(20)表示字段是固定长度字符串，长度为 20
- varchar(20) 表示字段是可变长度字符串，长度为 20

## 73.   drop、delete与truncate的区别

| | 	delete |	truncate	| drop |
| ----- | ----- | ----- | ----- |
|类型	| DML |	DDL	| DDL|
|回滚	|可回滚	|不可回滚|	不可回滚|
|删除内容	|表结构还在，删除表的全部或者一部分数据行	|表结构还在，删除表中的所有数据|	从数据库中删除表，所有的数据行，索引和权限也会被删除|
|删除速度	|删除速度慢，逐行删除	|删除速度快|	删除速度最快|

## 74.   UNION与UNION ALL的区别？

- Union：对两个结果集进行并集操作，不包括重复行，同时进行默认规则的排序；
- Union All：对两个结果集进行并集操作，包括重复行，不进行排序；
- UNION的效率高于 UNION ALL

## 75.  SQL的生命周期？

- 服务器与数据库建立连接
- 数据库进程拿到请求sql
- 解析并生成执行计划，执行
- 读取数据到内存，并进行逻辑处理
- 通过步骤一的连接，发送结果到客户端
- 关掉连接，释放资源

## 76.  一条Sql的执行顺序？

![](../images/17243f19b75d3514.jpg)

## 77.  列值为NULL时，查询是否会用到索引？

列值为NULL也是可以走索引的
计划对列进行索引，应尽量避免把它设置为可空，因为这会让 MySQL 难以优化引用了可空列的查询，同时增加了引擎的复杂度

## 78.   关心过业务系统里面的sql耗时吗？统计过慢查询吗？对慢查询都怎么优化过？

- 我们平时写Sql时，都要养成用explain分析的习惯。
- 慢查询的统计，运维会定期统计给我们

**优化慢查询：**

- 分析语句，是否加载了不必要的字段/数据。
- 分析SQl执行句话，是否命中索引等。
- 如果SQL很复杂，优化SQL结构
- 如果表数据量太大，考虑分表

## 79.  主键使用自增ID还是UUID，为什么？

如果是单机的话，选择自增ID；如果是分布式系统，优先考虑UUID吧，但还是最好自己公司有一套分布式唯一ID生产方案吧。

- 自增ID：数据存储空间小，查询效率高。但是如果数据量过大,会超出自增长的值范围，多库合并，也有可能有问题。
- uuid：适合大量数据的插入和更新操作，但是它无序的，插入数据效率慢，占用空间大。

## 80. mysql自增主键用完了怎么办？

自增主键一般用int类型，一般达不到最大值，可以考虑提前分库分表的。

自增ID用完后 一直都是最大值 如果标识了主键 则主键冲突

## 81. 字段为什么要求定义为not null？

null值会占用更多的字节，并且null有很多坑的。

## 82.  如果要存储用户的密码散列，应该使用什么字段进行存储？

密码散列，盐，用户身份证号等固定长度的字符串，应该使用char而不是varchar来存储，这样可以节省空间且提高检索效率。

## 83. Mysql驱动程序是什么？

这个jar包： mysql-connector-java-5.1.18.jar
Mysql驱动程序主要帮助编程语言与 MySQL服务端进行通信，如连接、传输数据、关闭等。

## 84.   如何优化长难的查询语句？有实战过吗？

- 将一个大的查询分为多个小的相同的查询
- 减少冗余记录的查询。
- 一个复杂查询可以考虑拆成多个简单查询
- 分解关联查询，让缓存的效率更高。

## 85.  优化特定类型的查询语句

平时积累吧：

- 比如使用select 具体字段代替 select *
- 使用count(*) 而不是count(列名)
- 在不影响业务的情况，使用缓存
- explain 分析你的SQL


## 86. MySQL数据库cpu飙升的话，要怎么处理呢？

**排查过程：**

- 使用top 命令观察，确定是mysqld导致还是其他原因。
- 如果是mysqld导致的，show processlist，查看session情况，确定是不是有消耗资源的sql在运行。
- 找出消耗高的 sql，看看执行计划是否准确， 索引是否缺失，数据量是否太大。

**处理：**

- kill 掉这些线程(同时观察 cpu 使用率是否下降)，
- 进行相应的调整(比如说加索引、改 sql、改内存参数)
- 重新跑这些 SQL。

**其他情况：**

也有可能是每个 sql 消耗资源并不多，但是突然之间，有大量的 session 连进来导致 cpu 飙升，这种情况就需要跟应用一起来分析为何连接数会激增，再做出相应的调整，比如说限制连接数等

## 87.   读写分离常见方案？

- 应用程序根据业务逻辑来判断，增删改等写操作命令发给主库，查询命令发给备库。
- 利用中间件来做代理，负责对数据库的请求识别出读还是写，并分发到不同的数据库中。（如：amoeba，mysql-proxy）

## 88. MySQL的复制原理以及流程

主从复制原理，简言之，就三步曲，如下：

- 主数据库有个bin-log二进制文件，纪录了所有增删改Sql语句。（binlog线程）
- 从数据库把主数据库的bin-log文件的sql语句复制过来。（io线程）
- 从数据库的relay-log重做日志文件中再执行一次这些sql语句。（Sql执行线程）

如下图所示：

![](../images/16c4d9dd1b8235c3.jpg)

上图主从复制分了五个步骤进行：

- 步骤一：主库的更新事件(update、insert、delete)被写到binlog
- 步骤二：从库发起连接，连接到主库。
- 步骤三：此时主库创建一个binlog dump thread，把binlog的内容发送到从库。
- 步骤四：从库启动之后，创建一个I/O线程，读取主库传过来的binlog内容并写入到relay log
- 步骤五：还会创建一个SQL线程，从relay log里面读取内容，从Exec_Master_Log_Pos位置开始执行读取到的更新事件，将更新内容写入到slave的db

## 89.  MySQL中DATETIME和TIMESTAMP的区别

存储精度都为秒

**区别：**

- DATETIME 的日期范围是 1001——9999 年；TIMESTAMP 的时间范围是 1970——2038 年
- DATETIME 存储时间与时区无关；TIMESTAMP 存储时间与时区有关，显示的值也依赖于时区
- DATETIME 的存储空间为 8 字节；TIMESTAMP 的存储空间为 4 字节
- DATETIME 的默认值为 null；TIMESTAMP 的字段默认不为空(not null)，默认值为当前时间(CURRENT_TIMESTAMP)

## 90.   Innodb的事务实现原理？

- 原子性：是使用 undo log来实现的，如果事务执行过程中出错或者用户执行了rollback，系统通过undo log日志返回事务开始的状态。
- 持久性：使用 redo log来实现，只要redo log日志持久化了，当系统崩溃，即可通过redo log把数据恢复。
- 隔离性：通过锁以及MVCC,使事务相互隔离开。
- 一致性：通过回滚、恢复，以及并发情况下的隔离性，从而实现一致性。

## 91. 谈谈MySQL的Explain

Explain 执行计划包含字段信息如下：分别是 id、select_type、table、partitions、type、possible_keys、key、key_len、ref、rows、filtered、Extra 等12个字段。
我们重点关注的是type，它的属性排序如下：

```sql
system  > const > eq_ref > ref  > ref_or_null >
index_merge > unique_subquery > index_subquery > 
range > index > ALL
```

## 92. Innodb的事务与日志的实现方式


### 有多少种日志

- innodb两种日志redo和undo。

### 日志的存放形式

- redo：在页修改的时候，先写到 redo log buffer 里面， 然后写到 redo log 的文件系统缓存里面(fwrite)，然后再同步到磁盘文件（ fsync）。
- Undo：在 MySQL5.5 之前， undo 只能存放在 ibdata文件里面， 5.6 之后，可以通过设置 innodb_undo_tablespaces 参数把 undo log 存放在 ibdata之外。

### 事务是如何通过日志来实现的

- 因为事务在修改页时，要先记 undo，在记 undo 之前要记 undo 的 redo， 然后修改数据页，再记数据页修改的 redo。 Redo（里面包括 undo 的修改） 一定要比数据页先持久化到磁盘。
- 当事务需要回滚时，因为有 undo，可以把数据页回滚到前镜像的 状态，崩溃恢复时，如果 redo log 中事务没有对应的 commit 记录，那么需要用 undo把该事务的修改回滚到事务开始之前。
- 如果有 commit 记录，就用 redo 前滚到该事务完成时并提交掉。

## 93.  MySQL中TEXT数据类型的最大长度

- TINYTEXT：256 bytes
- TEXT：65,535 bytes(64kb)
- MEDIUMTEXT：16,777,215 bytes(16MB)
- LONGTEXT：4,294,967,295 bytes(4GB)

## 94.  500台db，在最快时间之内重启。

- 可以使用批量 ssh 工具 pssh 来对需要重启的机器执行重启命令。
- 也可以使用 salt（前提是客户端有安装 salt）或者 ansible（ ansible 只需要 ssh 免登通了就行）等多线程工具同时操作多台服务

## 95. 你是如何监控你们的数据库的？你们的慢日志都是怎么查询的？

监控的工具有很多，例如zabbix，lepus，我这里用的是lepus

## 96. 你是否做过主从一致性校验，如果有，怎么做的，如果没有，你打算怎么做？

主从一致性校验有多种工具 例如checksum、mysqldiff、pt-table-checksum等

## 97. 你们数据库是否支持emoji表情存储，如果不支持，如何操作？

更换字符集utf8-->utf8mb4

## 98.  MySQL如何获取当前日期？

SELECT CURRENT_DATE();

## 99. 一个6亿的表a，一个3亿的表b，通过外间tid关联，你如何最快的查询出满足条件的第50000到第50200中的这200条数据记录。

1、如果A表TID是自增长,并且是连续的,B表的ID为索引

```sql
select * from a,b where a.tid = b.id and a.tid>500000 limit 200;
```

2、如果A表的TID不是连续的,那么就需要使用覆盖索引.TID要么是主键,要么是辅助索引,B表ID也需要有索引。

```sql
select * from b , (select tid from a limit 50000,200) a where b.id = a .tid;
```

## 100. Mysql一条SQL加锁分析

一条SQL加锁，可以分9种情况进行：

- 组合一：id列是主键，RC隔离级别
- 组合二：id列是二级唯一索引，RC隔离级别
- 组合三：id列是二级非唯一索引，RC隔离级别
- 组合四：id列上没有索引，RC隔离级别
- 组合五：id列是主键，RR隔离级别
- 组合六：id列是二级唯一索引，RR隔离级别
- 组合七：id列是二级非唯一索引，RR隔离级别
- 组合八：id列上没有索引，RR隔离级别
- 组合九：Serializable隔离级别

>作者：Jay_huaxiao
>链接：https://juejin.im/post/5ec15ab9f265da7bc60e1910
>来源：掘金
>著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。


# MySQL InnoDB MVCC 机制的原理及实现


## MVCC 是什么？

### 数据库并发控制——锁

Multiversion (version) concurrency control (MCC or MVCC) 多版本并发控制 ，它是数据库管理系统一种常见的并发控制。
我们知道并发控制常用的是锁，当线程要对一个共享资源进行操作的时候，加锁是一种非常简单粗暴的方法(事务开始时给 DQL 加读锁，给 DML 加写锁)，这种锁是一种 悲观 的实现方式，也就是说这会给其他事务造成堵塞，从而影响数据库性能。
我来解释一下 乐观锁 和 悲观锁 的概念。我觉得它俩主要是概念的理解。

- 悲观锁： 当一个线程需要对共享资源进行操作的时候，首先对共享资源进行加锁，当该线程持有该资源的锁的时候，其他线程对该资源进行操作的时候会被 阻塞。比如 Java 中的 Synchronized 关键字。
- 乐观锁：当一个线程需要对一个共享资源进行操作的时候，不对它进行加锁，而是在操作完成之后进行判断。(比如乐观锁会通过一个版本号控制，如果操作完成后通过版本号进行判断在该线程操作过程中是否有其他线程已经对该共享资源进行操作了，如果有则通知操作失败，如果没有则操作成功)，当然除了 版本号 还有 CAS，如果不了解的可以去学习一下，这里不做过多涉及。

### 数据库并发控制——MVCC

很多人认为 MVCC 就是一种 乐观锁 的实现形式，而我认为 MVCC 只是一种 乐观 的实现形式，它是通过 一种 可见性算法 来实现数据库并发控制。

### MVCC 的两种读形式

在讲 MVCC 的实现原理之前，我觉很有必要先去了解一下 MVCC 的两种读形式。

- 快照读：读取的只是当前事务的可见版本，不用加锁。而你只要记住 简单的 select 操作就是快照读(select * from table where id = xxx)。
- 当前读：读取的是当前版本，比如 特殊的读操作，更新/插入/删除操作

比如：

```sql
 select * from table where xxx lock in share mode，
 select * from table where xxx for update，
 update table set....
 insert into table (xxx,xxx) values (xxx,xxx)
 delete from table where id = xxx
```

## MVCC 的实现原理

MVCC 使用了“三个隐藏字段”来实现版本并发控制，我查了很多资料，看到有很多博客上写的是通过 一个创建事务id字段和一个删除事务id字段 来控制实现的。但后来发现并不是很正确，我们先来看一看 MySQL 在建表的时候 innoDB 创建的真正的三个隐藏列吧。

|RowID	|DB_TRX_ID	|DB_ROLL_PTR|	id|	name	|password|
| ---- | ---- | ---- | ---- | ---- | ----|
|自动创建的id	|事务id	|回滚指针|	id|	name|	password|

- RowID：隐藏的自增ID，当建表没有指定主键，InnoDB会使用该RowID创建一个聚簇索引。
- DB_TRX_ID：最近修改（更新/删除/插入）该记录的事务ID。
- DB_ROLL_PTR：回滚指针，指向这条记录的上一个版本。

> 其实还有一个删除的flag字段，用来判断该行记录是否已经被删除。

而 MVCC 使用的是其中的 事务字段，回滚指针字段，是否删除字段。我们来看一下现在的表格(isDelete是我自己取的，按照官方说法是在一行开头的content里面，这里其实位置无所谓，你只要知道有就行了)。


|isDelete	|DB_TRX_ID	|DB_ROLL_PTR|	id	|name	|password|
| ---- | ---- | ---- | ---- | ---- |---- |
|true/false|	事务id	|回滚指针	|id|	name	|password|

那么如何通过这三个字段来实现 MVCC 的 可见性算法 呢？
还差点东西！ undoLog(回滚日志) 和 read-view(读视图)。


- undoLog: 事务的回滚日志，是 可见性算法 的非常重要的部分，分为两类。
    - insert undo log：事务在插入新记录产生的undo log，当事务提交之后可以直接丢弃
    - update undo log：事务在进行 update 或者 delete 的时候产生的 undo log，在快照读的时候还是需要的，所以不能直接删除，只有当系统没有比这个log更早的read-view了的时候才能删除。ps：所以长事务会产生很多老的视图导致undo log无法删除 大量占用存储空间。
- read-view: 读视图，是MySQL秒级创建视图的必要条件，比如一个事务在进行 select 操作(快照读)的时候会创建一个 read-view ，这个read-view 其实只是三个字段。
    - alive_trx_list(我自己取的)：read-view生成时刻系统中正在活跃的事务id。
    - up_limit_id：记录上面的 alive_trx_list 中的最小事务id。
    - low_limit_id：read-view生成时刻，目前已出现的事务ID的最大值 + 1。



这时候，万事俱备，只欠东风了。下面我来介绍一下，最重要的 可见性算法。
其实主要思路就是：当生成read-view的时候如何去拿获取的 DB_TRX_ID 去和 read-view 中的三个属性(上面讲了)去作比较。我来说一下三个步骤，如果不是很理解可以参考着我后面的实践结合着去理解。

- 首先比较这条记录的 DB_TRX_ID 是否是 小于 up_limit_id 或者 等于当前事务id。如果满足，那么说明当前事务能看到这条记录。如果大于则进入下一轮判断
- 然后判断这条记录的 DB_TRX_ID 是否 大于等于 low-limit-id。如果大于等于则说明此事务无法看见该条记录，不然就进入下一轮判断。
- 判断该条记录的 DB_TRX_ID 是否在活跃事务的数组中，如果在则说明这条记录还未提交对于当前操作的事务是不可见的，如果不在则说明已经提交，那么就是可见的。

> 如果此条记录对于该事务不可见且 ROLL_PTR 不为空那么就会指向回滚指针的地址，通过undolog来查找可见的记录版本。

下面我画了一个可见性的算法的流程图

![](../images/16dddb3da13ee747.jpg)

## 实践

### 准备数据

首先我创建了一个非常简单的表，只有id和name的学生表。

|id	|name|
|----|----|
|学生id|	学生姓名|

这个时候我们将我们需要的隐藏列也标识出来，就变成了这样

|isDelete	|id|	|name	|DB_TRX_ID	|DB_ROLL_PTR|
|----|----|----|----|----|----|
|是否被删除	|学生id	|学生姓名|	创建删除更新该记录的事务id	|回滚指针|

这个时候插入三行数据，将表的数据变成下面这个样子。

|isDelete	|id|	name|	DB_TRX_ID|	DB_ROLL_PTR|
|----|----|----|----|----|
|false	|1	|小明	|1	|null
false	|2	|小方	|1|	null
false	|3|	小张	|1|	null

### 示例一

![](../images/16dddce6e7f823b4.jpg)

使用过 MySQL 的都知道，因为隔离性，事务 B 此时获取到的数据肯定是这样的。

|id|	name|
|----|----|
1	|小明
2	|小方
3	|小张

为什么事务A未提交的修改对于事务B是不可见的，MVCC 是如何做到的？我们用刚刚的可见性算法来实验一下。
首先事务A开启了事务(当然这不算开启，在RR模式下 真正获取read-view的是在进行第一次进行快照读的时候)。我们假设事务A的事务id为2，事务B的id为3。
然后事务A进行了更新操作，如图所示，更新操作创建了一个新的版本并且新版本的回滚指针指向了旧的版本(注意 undo log其实存放的是逻辑日志，这里为了方便我直接写成物理日志)。

![](../images/16ddddda50800f63.jpg)


最后 事务B 进行了快照读，注意，这是我们分析的重点。

首先，在进行快照读的时候我们会创建一个 read-view (忘记回去看一下那三个字段) 这个时候我们的 read-view 是:
```bash
  up-limit-id = 2
  alive-trx-list = [2,3]
  low-limit-id = 4
```

然后我们获取那两个没有被修改的记录(没有顺序，这里为了一起解释方便), 我们获取到(2,小方)和(3,小张)这两条记录，发现他们两的 `DB_TRX_ID = 1`,  我们先判断 DB_TRX_ID 是否小于 `up-limit-id` 或者等于当前事务id ,  发现 1<2 小于 up-limit-id ，则可见 直接返回视图。
  
然后我们获取更改了的数据行:

![](../images/16ddddda50800f63.jpg)

其实你也发现了这是一个链表，此时链表头的 DB_TRX_ID 为 2,我们进行判断 2 < 2 不符合，进入下一步判断, 判断 DB_TRX_ID >= low_limit_id 发现此时是 2 >= 4 不符合 故再进入下一步,此时判断 Db_TRX_ID 是否在 alive_trx_list 活跃事务列表中，发现这个 DB_TRX_ID 在活跃列表中，所以只能说明该行记录还未提交，不可见。最终判断不可见之后通过回滚指针查看旧版本，发现此时 DB_TRX_ID 为1,故再次进行判断 DB_TRX_ID < up-limit-id ,此时 1 < 2 符合 ，所以可见并返回, 所以最终返回的是

|id	|name|
|----|----|
1	|小明
2	|小方
3	|小张

我们再来验证一下，这个时候我们将事务A提交，重新创建一个事务C并select。

我们预期的结果应该是这样的

|id	|name|
|----|----|
1	|小强
2	|小方
3	|小张

这个操作的流程图如下

![](../images/16dde266675798b5.jpg)

这个时候我们再来分析一下 事务c产生的 read-view。

这个时候事务A已经提交，所以事务A不在活跃事务数组中，此时 read-view 的三个属性应该是

```bash
 up-limit-id = 3
 alive-trx-list = [3,4]
 low-limit-id = 5
```

- 跟上面一样，我们首先获取(2,小方)和(3,小张)这两条记录，发现他们两的 DB_TRX_ID = 1，此时 1 < up-limit-id = 3，故符合可见性，则返回。
- 然后我们获取刚刚被修改的id为1的记录行，发现链表头部的 DB_TRX_ID 为 2, 此时 2 < up-limit-id = 3 故也符合可见性，则返回。

所以最终返回的就是

|id	|name|
|----|----|
1	|小强
2	|小方
3	|小张

### 示例二

为了加深理解，我们再使用一个相对来说比较复杂的示例来验证 可见性算法 。

![](../images/16dde33e7e6444f3.jpg)

首先我们在事务A中删除一条记录，这个时候就变成了下面的样子。

![](../images/16dde36171697ce0.jpg)

然后事务B进行了插入，这样就变成了下面这样。

![](../images/16dde38e3d6c258f.jpg)

然后事务B进行了 select 操作，我们可以发现 这个时候整张表其实会变成这样让这个 select 操作进行选取。

![](../images/16dde41e63808dc7.jpg)

此时的 read-view 为

```bash
 up-limit-id = 2
 alive-trx-list = [2,3,4]
 low-limit-id = 5
```

这个时候我们进行 快照读，首先对于前面两条小明和小方的记录是一样的，此时 DB_TX_ID 为 1，我们可以判断此时 DB_TX_ID = 1 < up-limit-id = 2 成立故返回。然后判断小张这条记录，首先也是 DB_TX_ID = 2 < up-limit-id = 2 不成立故进入下一轮，DB_TX_ID = 2 >= low-limit-id 不成立再进入最后一轮判断是否在活跃事务列表中，发现 DB_TX_ID = 2 在 alive-trx-list = [2,3,4] 中故不可见(如果可见则会知道前面的删除标志是已经删除，则返回的是空)，则根据回滚指针找到上一个版本记录，此时 DB_TX_ID = 1 和上面一样可见则返回该行。
最后一个判断小亮这条记录，因为 DB_TX_ID = current_tx_id(当前事务id) 所以可见并返回。
这个时候返回的表则是这样的


|id	|name|
|----|----|
1	|小明
2	|小方
3	|小张
4	|小亮

然后是事务A进行了select的操作，我们可以得知现在的 read-view 为

```bash
 up-limit-id = 2
 alive-trx-list = [2,3,4]
 low-limit-id = 5
```

然后此时所见和上面也是一样的

![](../images/16dde41e63808dc7.jpg)

这个时候我们进行 快照读，首先对于前面两条小明和小方的记录是一样的，此时 DB_TX_ID 为 1，我们可以判断此时 DB_TX_ID = 1 < up-limit-id = 2 成立故返回。然后判断小张这条记录，首先 DB_TX_ID = 2 = current_tx_id = 2 成立故返回发现前面的 isDelete 标志为true 则说明已被删除则返回空，对于第四条小亮的也是一样判断 DB_TX_ID = 4 < up-limit-id = 2 不成立进入下一步判断 DB_TX_ID = 4 >= low-limit-id = 5 不成立进入最后一步发现在活跃事务数组中故不可见且此条记录回滚指针为null所以返回空。
那么此时返回的列表应该就是这样了

|id|	name|
|----|----|
1	|小明
2	|小方

> 虽然要分析很多，但多多益善嘛，多熟悉熟悉就能更深刻理解这个算法了。

之后是事务C进行 快照读 操作。首先此时视图还是这个样子

![](../images/16dde41e63808dc7.jpg)

然后对于事务C的 read-view 为

```bash
 up-limit-id = 2
 alive-trx-list = [2,3,4]
 low-limit-id = 5
```

小明和小方的两条记录和上面一样是可见的这里我就不重复分析了，然后对于小张这条记录 DB_TX_ID = 2 < up-limit-id = 2 || DB_TX_ID == curent_tx_id = 4 不成立故进入下一轮发现 DB_TX_ID >= low-limit-id = 5 更不成立故进入最后一轮发现 DB_TX_ID = 2 在活跃事务数组中故不可见，然后通过回滚指针判断 DB_TX_ID = 1 的小张记录发现可见并返回。最后的小亮也是如此 最后会发现 DB_TX_ID = 3 也在活跃事务数组中故不可见。
所以事务C select 的结果为

|id|	name|
|----|----|
1	|小明
2	|小方
3	|小张

后面事务A和事务B都进行了提交的动作，并且有一个事务D进行了快照读，此时视图还是如此

![](../images/16dde41e63808dc7.jpg)

但此时的 read-view发生了变化

```bash
 up-limit-id = 4
 alive-trx-list = [4,5]
 low-limit-id = 6
```

我们首先判断小明和小方的记录——可见(不解释了)，小张的记录 DB_TX_ID = 2 < up-limit-id = 4 成立故可见，因为前面 isDelete 为 true 则说明删除了返回空，然后小亮的记录 DB_TX_ID = 3 < up-limit-id = 4 成立故可见则返回。所以这次的 select 结果应该是这样的

|id	|name|
|----|----|
1	|小明
2	|小方
4	|小亮

最后(真的最后了，不容易吧！)，事务C有一次进行了 select 操作。因为在 RR 模式下 read-view 是在第一次快照读的时候确定的，所以此时 read-view是不会更改的，然后前面视图也没有进行更改，所以此时即使前面事务A 事务B已经进行了提交，对于这个时候的事务C的select结果是没有影响的。故结果应该为



|id|name|
|----|----|
1|小明
2|小方
3|小张

总结
我们来总结一下吧。

其实 MVCC 是通过 "三个" 隐藏字段 (事务id,回滚指针,删除标志) 加上undo log和可见性算法来实现的版本并发控制。

为了你再次深入理解这个算法，我再把这张图挂上来

![](../images/16dddb3da13ee747.jpg)


>作者：FrancisQ
>链接：https://juejin.im/post/5da8493ae51d4524b25add55
>来源：掘金
>著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。## MySQL
### 1.事务

数据库事务(Database Transaction) ，是指作为单个逻辑工作单元执行的一系列操作，要么完全地执行，要么完全地不执行。
彻底理解数据库事务: http://www.hollischuang.com/archives/898

### 2.数据库索引

[MySQL索引背后的数据结构及算法原理](http://blog.codinglabs.org/articles/theory-of-mysql-index.html)

[面试中常被提到的最左前缀匹配原则](https://www.cnblogs.com/ljl150/p/12934071.html)

聚集索引,非聚集索引,B-Tree,B+Tree,最左前缀原理

### 3.乐观锁和悲观锁

悲观锁：假定会发生并发冲突，屏蔽一切可能违反数据完整性的操作

乐观锁：假设不会发生并发冲突，只在提交操作时检查是否违反数据完整性。

乐观锁与悲观锁的具体区别: https://juejin.cn/post/6844903639207641096#heading-1

### 4.MVCC

> ​	全称是Multi-Version Concurrent Control，即多版本并发控制，在MVCC协议下，每个读操作会看到一个一致性的snapshot，并且可以实现非阻塞的读。MVCC允许数据具有多个版本，这个版本可以是时间戳或者是全局递增的事务ID，在同一个时间点，不同的事务看到的数据是不同的。

### [MySQL](http://lib.csdn.net/base/mysql)的innodb引擎是如何实现MVCC的

innodb会为每一行添加两个字段，分别表示该行**创建的版本**和**删除的版本**，填入的是事务的版本号，这个版本号随着事务的创建不断递增。在repeated read的隔离级别（[事务的隔离级别请看这篇文章](http://blog.csdn.net/chosen0ne/article/details/10036775)）下，具体各种数据库操作的实现：

- select：满足以下两个条件innodb会返回该行数据：
  - 该行的创建版本号小于等于当前版本号，用于保证在select操作之前所有的操作已经执行落地。
  - 该行的删除版本号大于当前版本或者为空。删除版本号大于当前版本意味着有一个并发事务将该行删除了。
- insert：将新插入的行的创建版本号设置为当前系统的版本号。
- delete：将要删除的行的删除版本号设置为当前系统的版本号。
- update：不执行原地update，而是转换成insert + delete。将旧行的删除版本号设置为当前版本号，并将新行insert同时设置创建版本号为当前版本号。

其中，写操作（insert、delete和update）执行时，需要将系统版本号递增。

​由于旧数据并不真正的删除，所以必须对这些数据进行清理，innodb会开启一个后台线程执行清理工作，具体的规则是将删除版本号小于当前系统版本的行删除，这个过程叫做purge。

通过MVCC很好的实现了事务的隔离性，可以达到repeated read级别，要实现serializable还必须加锁。

参考：[MVCC浅析](http://blog.csdn.net/chosen0ne/article/details/18093187)

### 5.MyISAM和InnoDB

MyISAM 适合于一些需要大量查询的应用，但其对于有大量写操作并不是很好。甚至你只是需要update一个字段，整个表都会被锁起来，而别的进程，就算是读进程都无法操作直到读操作完成。另外，MyISAM 对于 SELECT COUNT(*) 这类的计算是超快无比的。

InnoDB 的趋势会是一个非常复杂的存储引擎，对于一些小的应用，它会比 MyISAM 还慢。他是它支持“行锁” ，于是在写操作比较多的时候，会更优秀。并且，他还支持更多的高级应用，比如：事务。

mysql 数据库引擎: http://www.cnblogs.com/0201zcr/p/5296843.html
MySQL存储引擎－－MyISAM与InnoDB区别: https://segmentfault.com/a/1190000008227211

### 6.主键 超键 候选键 外键

主键：数据库表中对存储数据对象予以唯一和完整标识的数据列或属性的组合。一个数据列只能有一个主键，且主键的取值不能缺失，即不能为空值(Null).

超键：在关系中能唯一标识元组的属性集称为关系模式的超键。一个属性可以作为一个超键，多个属性组合在一起也可以作为一个超键。超键包含候选键和主键。

候选键：是最小超键，即没有冗余元素的超键。

外键：在一个表中存在的另一个表的主键称此表的外键。

### 7.视图的作用，视图可以更改么？

视图是虚拟的表，与包含数据的表不一样，视图只包含使用时动态检索数据的查询;不包含任何列或数据。使用视图可以简化复杂的sql操作，隐藏具体的细节，保护数据;视图创建后，可以使用与表相同的方式利用它们。

视图不能被索引，也不能有关联的触发器或默认值，如果视图本身内有order by则对视图再次order by将被覆盖。

创建视图： create view xxx as xxxxxx

对于某些视图比如未使用联结子查询分组聚集函数Distinct Union等，是可以对其更新的，对视图的更新将对基表进行更新;但是视图主要用于简化检索，保护数据，并不用于更新，而且大部分视图都不可以更新。

### 8.drop,delete与truncate的区别

drop直接删掉表，truncate删除表中数据，再插入时自增长id又从1开始，delete删除表中数据，可以加where字句。

1.delete 语句执行删除的过程是每次从表中删除一行，并且同时将该行的删除操作作为事务记录在日志中保存以便进行回滚操作。truncate table则一次性地从表中删除所有的数据并不把单独的删除操作记录记入日志保存，删除行是不能恢复的。并且在删除的过程中不会激活与表有关的删除触发器，执行速度快。

2.表和索引所占空间。当表被truncate后，这个表和索引所占用的空间会恢复到初始大小，而delete操作不会减少表或索引所占用的空间。drop语句将表所占用的空间全释放掉。

3.一般而言，drop>truncate>delete

4.应用范围。truncate只能对table，delete可以是table和view

5.truncate和delete只删除数据，而drop则删除整个表（结构和数据)

6.truncate与不带where的delete:只删除数据，而不删除表的结构（定义）drop语句将删除表的结构被依赖的约束(constrain),触发器（trigger)索引(index);依赖于该表的存储过程/函数将被保留，但其状态会变为:invalid.

### 9.索引的工作原理及其种类

数据库索引，是数据库管理系统中一个排序的数据结构，以协助快速查询，更新数据库表中数据。索引的实现通常使用B树以其变种B+树。

在数据之外，数据库系统还维护着满足特定查找算法的数据结构，这些数据结构以某种方式引用（指向）数据，这样就可以在这些数据结构上实现高级查找算法。这种数据结构，就是索引。

为表设置索引要付出代价的：一是增加了数据库的存储空间，二是在插入和修改数据时要花费较多的时间（因为索引也要随之变动）


### 常用索引数据结构，以及MySQL B 树 B+ 树区别。

https://mp.weixin.qq.com/s/d7Zfat2fP6IX5DMKKtEIjQ


### 如何对 MySQL 进行服务器扩容。

https://mp.weixin.qq.com/s/r6usm2DkSC4Qx3FRSoMCNA


### 分库分表相关

https://mp.weixin.qq.com/s/dGECnPailOkMX476KoTQfg

### 隔离级别、MVCC

https://mp.weixin.qq.com/s/XOBhxc_AiuUxvwBsB_JprQ

### MySQL 的锁机制

https://mp.weixin.qq.com/s/ezBZhZaUqQtASPeT5TKslw
https://mp.weixin.qq.com/s/ZNdBk22Un-7wIYz2fM7-xA
## 交替打印数字和字母

**问题描述**

使用两个 `goroutine` 交替打印序列，一个 `goroutine` 打印数字， 另外一个 `goroutine` 打印字母， 最终效果如下：

```bash
12AB34CD56EF78GH910IJ1112KL1314MN1516OP1718QR1920ST2122UV2324WX2526YZ2728
```

**解题思路**

问题很简单，使用 channel 来控制打印的进度。使用两个 channel ，来分别控制数字和字母的打印序列， 数字打印完成后通过 channel 通知字母打印, 字母打印完成后通知数字打印，然后周而复始的工作。

**源码参考**

```
	letter,number := make(chan bool),make(chan bool)
	wait := sync.WaitGroup{}

	go func() {
		i := 1
		for {
			select {
			case <-number:
				fmt.Print(i)
				i++
				fmt.Print(i)
				i++
				letter <- true
			}
		}
	}()
	wait.Add(1)
	go func(wait *sync.WaitGroup) {
		i := 'A'
		for{
			select {
			case <-letter:
				if i >= 'Z' {
					wait.Done()
					return
				}

				fmt.Print(string(i))
				i++
				fmt.Print(string(i))
				i++
				number <- true
			}

		}
	}(&wait)
	number<-true
	wait.Wait()
```

**源码解析**

这里用到了两个`channel`负责通知，letter负责通知打印字母的goroutine来打印字母，number用来通知打印数字的goroutine打印数字。

wait用来等待字母打印完成后退出循环。
<!-- TOC -->

- [Python精选](#Python精选)
    - [必备知识](必备知识)
        - [1.Python的函数参数传递](#1python%e7%9a%84%e5%87%bd%e6%95%b0%e5%8f%82%e6%95%b0%e4%bc%a0%e9%80%92)
        - [2.Python中的元类(metaclass)](#2python%e4%b8%ad%e7%9a%84%e5%85%83%e7%b1%bbmetaclass)
        - [3.@staticmethod和@classmethod](#3@staticmethod和@classmethod)
        - [4.类变量和实例变量](#4类变量和实例变量)
        - [5.Python自省](#5python%e8%87%aa%e7%9c%81)
        - [6.字典推导式](#6字典推导式)
        - [7.Python中单下划线和双下划线](#7python%e4%b8%ad%e5%8d%95%e4%b8%8b%e5%88%92%e7%ba%bf%e5%92%8c%e5%8f%8c%e4%b8%8b%e5%88%92%e7%ba%bf)
        - [8.字符串格式化:%和.format](#8%e5%ad%97%e7%ac%a6%e4%b8%b2%e6%a0%bc%e5%bc%8f%e5%8c%96%e5%92%8cformat)
        - [9.迭代器和生成器](#9%e8%bf%ad%e4%bb%a3%e5%99%a8%e5%92%8c%e7%94%9f%e6%88%90%e5%99%a8)
        - [10.`*args` 和 `**kwargs`](#10args-%e5%92%8c-kwargs)
        - [11.面向切面编程AOP和装饰器](#11%e9%9d%a2%e5%90%91%e5%88%87%e9%9d%a2%e7%bc%96%e7%a8%8baop%e5%92%8c%e8%a3%85%e9%a5%b0%e5%99%a8)
        - [12.鸭子类型](#12%e9%b8%ad%e5%ad%90%e7%b1%bb%e5%9e%8b)
        - [13.Python中重载](#13python%e4%b8%ad%e9%87%8d%e8%bd%bd)
        - [14.新式类和旧式类](#14%e6%96%b0%e5%bc%8f%e7%b1%bb%e5%92%8c%e6%97%a7%e5%bc%8f%e7%b1%bb)
        - [15.`__new__`和`__init__`的区别](#15new%e5%92%8cinit%e7%9a%84%e5%8c%ba%e5%88%ab)
        - [16.单例模式](#16%e5%8d%95%e4%be%8b%e6%a8%a1%e5%bc%8f)
        - [17.Python中的作用域](#17python%e4%b8%ad%e7%9a%84%e4%bd%9c%e7%94%a8%e5%9f%9f)
        - [18.GIL线程全局锁](#18gil%e7%ba%bf%e7%a8%8b%e5%85%a8%e5%b1%80%e9%94%81)
        - [19.协程](#19%e5%8d%8f%e7%a8%8b)
        - [20.闭包](#20%e9%97%ad%e5%8c%85)
        - [21.lambda函数](#21lambda%e5%87%bd%e6%95%b0)
        - [22.Python函数式编程](#22python%e5%87%bd%e6%95%b0%e5%bc%8f%e7%bc%96%e7%a8%8b)
        - [23.Python里的拷贝](#23python%e9%87%8c%e7%9a%84%e6%8b%b7%e8%b4%9d)
        - [24.Python垃圾回收机制](#24python%e5%9e%83%e5%9c%be%e5%9b%9e%e6%94%b6%e6%9c%ba%e5%88%b6)
        - [25.Python的List](#25python%e7%9a%84list)
        - [26.Python的is](#26python%e7%9a%84is)
        - [27.read,readline和readlines](#27readreadline%e5%92%8creadlines)
        - [28.Python2和3的区别](#28python2%e5%92%8c3%e7%9a%84%e5%8c%ba%e5%88%ab)
        - [29.super init](#29super-init)
        - [30.range and xrange](#30range-and-xrange)
- [Python基础](#python基础)
    - [文件操作](#文件操作)
        - [31.Python 处理文件](#31python-%e5%a4%84%e7%90%86%e6%96%87%e4%bb%b6)
        - [32.遍历文件夹](#32%e9%81%8d%e5%8e%86%e6%96%87%e4%bb%b6%e5%a4%b9)
    - [模块与包](#模块与包)
        - [33.输入日期，判断这一天是这一年的第几天？](#33%e8%be%93%e5%85%a5%e6%97%a5%e6%9c%9f%e5%88%a4%e6%96%ad%e8%bf%99%e4%b8%80%e5%a4%a9%e6%98%af%e8%bf%99%e4%b8%80%e5%b9%b4%e7%9a%84%e7%ac%ac%e5%87%a0%e5%a4%a9)
        - [34.打乱一个排好序的list对象](#34%e6%89%93%e4%b9%b1%e4%b8%80%e4%b8%aa%e6%8e%92%e5%a5%bd%e5%ba%8f%e7%9a%84list%e5%af%b9%e8%b1%a1)
    - [数据类型](#数据类型)
        - [35.将字典按value值进行排序](#35%e5%b0%86%e5%ad%97%e5%85%b8%e6%8c%89value%e5%80%bc%e8%bf%9b%e8%a1%8c%e6%8e%92%e5%ba%8f)
        - [36.请反转字符串 "aStr"](#36%e8%af%b7%e5%8f%8d%e8%bd%ac%e5%ad%97%e7%ac%a6%e4%b8%b2-%22astr%22)
        - [37.将字符串 "k:1|k1:2|k2:3|k3:4" 处理成字典 {k:1,k1:2,...}](#37%e5%b0%86%e5%ad%97%e7%ac%a6%e4%b8%b2-%22k1k12k23k34%22-%e5%a4%84%e7%90%86%e6%88%90%e5%ad%97%e5%85%b8-k1k12)
        - [38.列表切片](#38%e5%88%97%e8%a1%a8%e5%88%87%e7%89%87)
        - [39.写一个列表生成式，产生一个公差为11的等差数列](#39%e5%86%99%e4%b8%80%e4%b8%aa%e5%88%97%e8%a1%a8%e7%94%9f%e6%88%90%e5%bc%8f%e4%ba%a7%e7%94%9f%e4%b8%80%e4%b8%aa%e5%85%ac%e5%b7%ae%e4%b8%ba11%e7%9a%84%e7%ad%89%e5%b7%ae%e6%95%b0%e5%88%97)
        - [40.给定两个列表，找出他们相同的元素和不同的元素](#40%e7%bb%99%e5%ae%9a%e4%b8%a4%e4%b8%aa%e5%88%97%e8%a1%a8%e6%89%be%e5%87%ba%e4%bb%96%e4%bb%ac%e7%9b%b8%e5%90%8c%e7%9a%84%e5%85%83%e7%b4%a0%e5%92%8c%e4%b8%8d%e5%90%8c%e7%9a%84%e5%85%83%e7%b4%a0)
        
    - [企业面试题](#企业面试题)
        - [41.python中内置的数据结构有几种](#41python%e4%b8%ad%e5%86%85%e7%bd%ae%e7%9a%84%e6%95%b0%e6%8d%ae%e7%bb%93%e6%9e%84%e6%9c%89%e5%87%a0%e7%a7%8d)
        - [42.反转一个整数，例如-123 --> -321](#42%e5%8f%8d%e8%bd%ac%e4%b8%80%e4%b8%aa%e6%95%b4%e6%95%b0%e4%be%8b%e5%a6%82-123-----321)
        - [43.实现遍历目录与子目录，抓取.pyc文件](#43%e5%ae%9e%e7%8e%b0%e9%81%8d%e5%8e%86%e7%9b%ae%e5%bd%95%e4%b8%8e%e5%ad%90%e7%9b%ae%e5%bd%95%e6%8a%93%e5%8f%96pyc%e6%96%87%e4%bb%b6)
        - [44.Python遍历列表时删除元素的正确做法](#44python%e9%81%8d%e5%8e%86%e5%88%97%e8%a1%a8%e6%97%b6%e5%88%a0%e9%99%a4%e5%85%83%e7%b4%a0%e7%9a%84%e6%ad%a3%e7%a1%ae%e5%81%9a%e6%b3%95)
        - [45.字符串 `"123"` 转换成 `123`，不使用内置api，例如 `int()`](#45%e5%ad%97%e7%ac%a6%e4%b8%b2-%22123%22-%e8%bd%ac%e6%8d%a2%e6%88%90-123%e4%b8%8d%e4%bd%bf%e7%94%a8%e5%86%85%e7%bd%aeapi%e4%be%8b%e5%a6%82-int)
        - [46.统计一个文本中单词频次最高的10个单词](#46%e7%bb%9f%e8%ae%a1%e4%b8%80%e4%b8%aa%e6%96%87%e6%9c%ac%e4%b8%ad%e5%8d%95%e8%af%8d%e9%a2%91%e6%ac%a1%e6%9c%80%e9%ab%98%e7%9a%8410%e4%b8%aa%e5%8d%95%e8%af%8d)
        - [47.阅读一下代码他们的输出结果是什么？](#47%e9%98%85%e8%af%bb%e4%b8%80%e4%b8%8b%e4%bb%a3%e7%a0%81%e4%bb%96%e4%bb%ac%e7%9a%84%e8%be%93%e5%87%ba%e7%bb%93%e6%9e%9c%e6%98%af%e4%bb%80%e4%b9%88)

- [Python高级](#python高级)
    - [元类](#元类)
        - [48.介绍Cython，Pypy Cpython Numba各有什么缺点](#48%e4%bb%8b%e7%bb%8dcythonpypy-cpython-numba%e5%90%84%e6%9c%89%e4%bb%80%e4%b9%88%e7%bc%ba%e7%82%b9)
        - [49.请描述抽象类和接口类的区别和联系](#49%e8%af%b7%e6%8f%8f%e8%bf%b0%e6%8a%bd%e8%b1%a1%e7%b1%bb%e5%92%8c%e6%8e%a5%e5%8f%a3%e7%b1%bb%e7%9a%84%e5%8c%ba%e5%88%ab%e5%92%8c%e8%81%94%e7%b3%bb)
        - [50.Python中如何动态获取和设置对象的属性](#50python%e4%b8%ad%e5%a6%82%e4%bd%95%e5%8a%a8%e6%80%81%e8%8e%b7%e5%8f%96%e5%92%8c%e8%ae%be%e7%bd%ae%e5%af%b9%e8%b1%a1%e7%9a%84%e5%b1%9e%e6%80%a7)
    
    - [内存管理与垃圾回收机制](#内存管理与垃圾回收机制)
        - [51.哪些操作会导致Python内存溢出，怎么处理](#51%e5%93%aa%e4%ba%9b%e6%93%8d%e4%bd%9c%e4%bc%9a%e5%af%bc%e8%87%b4python%e5%86%85%e5%ad%98%e6%ba%a2%e5%87%ba%e6%80%8e%e4%b9%88%e5%a4%84%e7%90%86)
        - [52.内存泄露是什么？如何避免？](#52内存泄露是什么如何避免)
    
    - [函数](#函数)
        - [53.手写一个判断时间的装饰器](#53%e6%89%8b%e5%86%99%e4%b8%80%e4%b8%aa%e5%88%a4%e6%96%ad%e6%97%b6%e9%97%b4%e7%9a%84%e8%a3%85%e9%a5%b0%e5%99%a8)
        - [54.带参数的装饰器](#54%e5%b8%a6%e5%8f%82%e6%95%b0%e7%9a%84%e8%a3%85%e9%a5%b0%e5%99%a8)
        - [55.线程安全的装饰器](#55%e7%ba%bf%e7%a8%8b%e5%ae%89%e5%85%a8%e7%9a%84%e8%a3%85%e9%a5%b0%e5%99%a8)
        - [56.交换两个变量的值](#56%e4%ba%a4%e6%8d%a2%e4%b8%a4%e4%b8%aa%e5%8f%98%e9%87%8f%e7%9a%84%e5%80%bc)
        - [57.hasattr() getattr() setattr() 函数使用详解](#57hasattr-getattr-setattr-%e5%87%bd%e6%95%b0%e4%bd%bf%e7%94%a8%e8%af%a6%e8%a7%a3)
        
    - [设计模式](#设计模式)
        - [58.对装饰器的理解，并写出一个计时器记录方法执行性能的装饰器](#58%e5%af%b9%e8%a3%85%e9%a5%b0%e5%99%a8%e7%9a%84%e7%90%86%e8%a7%a3%e5%b9%b6%e5%86%99%e5%87%ba%e4%b8%80%e4%b8%aa%e8%ae%a1%e6%97%b6%e5%99%a8%e8%ae%b0%e5%bd%95%e6%96%b9%e6%b3%95%e6%89%a7%e8%a1%8c%e6%80%a7%e8%83%bd%e7%9a%84%e8%a3%85%e9%a5%b0%e5%99%a8)
        - [59.Python中yield的用法](#59python中yield的用法)
    
    - [面向对象](#面向对象)
        - [60.Python的魔法方法](#60python%e7%9a%84%e9%ad%94%e6%b3%95%e6%96%b9%e6%b3%95)
        - [61.面向对象中怎么实现只读属性](#61%e9%9d%a2%e5%90%91%e5%af%b9%e8%b1%a1%e4%b8%ad%e6%80%8e%e4%b9%88%e5%ae%9e%e7%8e%b0%e5%8f%aa%e8%af%bb%e5%b1%9e%e6%80%a7)
        - [62.谈谈你对面向对象的理解](#62%e8%b0%88%e8%b0%88%e4%bd%a0%e5%af%b9%e9%9d%a2%e5%90%91%e5%af%b9%e8%b1%a1%e7%9a%84%e7%90%86%e8%a7%a3)

    - [正则表达式](#正则表达式)
        - [63.请写出一段代码用正则匹配出ip](#63%e8%af%b7%e5%86%99%e5%87%ba%e4%b8%80%e6%ae%b5%e4%bb%a3%e7%a0%81%e7%94%a8%e6%ad%a3%e5%88%99%e5%8c%b9%e9%85%8d%e5%87%baip)
        - [64.Python字符串查找和替换？](#64python字符串查找和替换)
        - [65.正则表达式贪婪与非贪婪模式的区别？](#65正则表达式贪婪与非贪婪模式的区别)
        - [66.写出开头匹配字母和下划线，末尾是数字的正则表达式？](#66写出开头匹配字母和下划线末尾是数字的正则表达式)
        - [67.怎么过滤评论中的表情？](#67怎么过滤评论中的表情)
        - [68.Python里match与search的区别？](#68python里match与search的区别)

- [Web](#web)
    - [Flask](#flask)
        - [140.对Flask蓝图(Blueprint)的理解？](#140对flask蓝图blueprint的理解)
        - [141.Flask 和 Django 路由映射的区别？](#141flask-和-django-路由映射的区别)
    - [Django](#django)
        - [142.什么是wsgi,uwsgi,uWSGI?](#142什么是wsgiuwsgiuwsgi)
        - [143.Django、Flask、Tornado的对比？](#143djangoflasktornado的对比)
        - [144.CORS 和 CSRF的区别？](#144cors-和-csrf的区别)
        - [145.Session,Cookie,JWT的理解](#145sessioncookiejwt的理解)
        - [146.简述Django请求生命周期](#146简述django请求生命周期)
        - [147.用的restframework完成api发送时间时区](#147用的restframework完成api发送时间时区)
        - [148.nginx,tomcat,apach到都是什么？](#148nginxtomcatapach到都是什么)
        - [149.请给出你熟悉关系数据库范式有哪些，有什么作用？](#149请给出你熟悉关系数据库范式有哪些有什么作用)
        - [150.简述QQ登陆过程](#150简述qq登陆过程)
        - [151.post 和 get的区别?](#151post-和-get的区别)
        - [152.项目中日志的作用](#152项目中日志的作用)
        - [153.django中间件的使用？](#153django中间件的使用)
        - [154.谈一下你对uWSGI和nginx的理解？](#154谈一下你对uwsgi和nginx的理解)
        - [155.Django中哪里用到了线程？哪里用到了协程？哪里用到了进程？](#155django中哪里用到了线程哪里用到了协程哪里用到了进程)
        - [156.有用过Django REST framework吗？](#156有用过django-rest-framework吗)
    - [爬虫](#爬虫)
        - [159.试列出至少三种目前流行的大型数据库](#159试列出至少三种目前流行的大型数据库)
        - [160.列举您使用过的Python网络爬虫所用到的网络数据包?](#160列举您使用过的python网络爬虫所用到的网络数据包)
        - [161.爬取数据后使用哪个数据库存储数据的，为什么？](#161爬取数据后使用哪个数据库存储数据的为什么)
        - [162.你用过的爬虫框架或者模块有哪些？优缺点？](#162你用过的爬虫框架或者模块有哪些优缺点)
        - [163.写爬虫是用多进程好？还是多线程好？](#163写爬虫是用多进程好还是多线程好)
        - [164.常见的反爬虫和应对方法？](#164常见的反爬虫和应对方法)
        - [165.解析网页的解析器使用最多的是哪几个?](#165解析网页的解析器使用最多的是哪几个)
        - [166.需要登录的网页，如何解决同时限制ip，cookie,session](#166需要登录的网页如何解决同时限制ipcookiesession)
        - [167.验证码的解决?](#167验证码的解决)
        - [168.使用最多的数据库，对他们的理解？](#168使用最多的数据库对他们的理解)
        - [169.编写过哪些爬虫中间件？](#169编写过哪些爬虫中间件)
        - [170.“极验”滑动验证码如何破解？](#170极验滑动验证码如何破解)
        - [171.爬虫多久爬一次，爬下来的数据是怎么存储？](#171爬虫多久爬一次爬下来的数据是怎么存储)
        - [172.cookie过期的处理问题？](#172cookie过期的处理问题)
        - [173.动态加载又对及时性要求很高怎么处理？](#173动态加载又对及时性要求很高怎么处理)
        - [174.HTTPS有什么优点和缺点？](#174https有什么优点和缺点)
        - [175.HTTPS是如何实现安全传输数据的？](#175https是如何实现安全传输数据的)
        - [176.TTL，MSL，RTT各是什么？](#176ttlmslrtt各是什么)
        - [177.谈一谈你对Selenium和PhantomJS了解](#177谈一谈你对selenium和phantomjs了解)
        - [178.平常怎么使用代理的 ？](#178平常怎么使用代理的-)
        - [179.存放在数据库(redis、mysql等)。](#179存放在数据库redismysql等)
        - [180.怎么监控爬虫的状态?](#180怎么监控爬虫的状态)
        - [181.描述下scrapy框架运行的机制？](#181描述下scrapy框架运行的机制)
        - [182.谈谈你对Scrapy的理解？](#182谈谈你对scrapy的理解)
        - [183.怎么样让 scrapy 框架发送一个 post 请求（具体写出来）](#183怎么样让-scrapy-框架发送一个-post-请求具体写出来)
        - [184.怎么监控爬虫的状态 ？](#184怎么监控爬虫的状态-)
        - [185.怎么判断网站是否更新？](#185怎么判断网站是否更新)
        - [186.图片、视频爬取怎么绕过防盗连接](#186图片视频爬取怎么绕过防盗连接)
        - [187.你爬出来的数据量大概有多大？大概多长时间爬一次？](#187你爬出来的数据量大概有多大大概多长时间爬一次)
        - [188.用什么数据库存爬下来的数据？部署是你做的吗？怎么部署？](#188用什么数据库存爬下来的数据部署是你做的吗怎么部署)
        - [189.增量爬取](#189增量爬取)
        - [190.爬取下来的数据如何去重，说一下scrapy的具体的算法依据。](#190爬取下来的数据如何去重说一下scrapy的具体的算法依据)
        - [191.Scrapy的优缺点?](#191scrapy的优缺点)
        - [192.怎么设置爬取深度？](#192怎么设置爬取深度)
        - [193.scrapy和scrapy-redis有什么区别？为什么选择redis数据库？](#193scrapy和scrapy-redis有什么区别为什么选择redis数据库)
        - [194.分布式爬虫主要解决什么问题？](#194分布式爬虫主要解决什么问题)
        - [195.什么是分布式存储？](#195什么是分布式存储)
        - [196.你所知道的分布式爬虫方案有哪些？](#196你所知道的分布式爬虫方案有哪些)
        - [197.scrapy-redis，有做过其他的分布式爬虫吗？](#197scrapy-redis有做过其他的分布式爬虫吗)
    
    - [测试](#测试)
        - [213.编写测试计划的目的是](#213编写测试计划的目的是)
        - [214.对关键词触发模块进行测试](#214对关键词触发模块进行测试)
        - [215.其他常用笔试题目网址汇总](#215其他常用笔试题目网址汇总)
        - [216.测试人员在软件开发过程中的任务是什么](#216测试人员在软件开发过程中的任务是什么)
        - [217.一条软件Bug记录都包含了哪些内容？](#217一条软件bug记录都包含了哪些内容)
        - [218.简述黑盒测试和白盒测试的优缺点](#218简述黑盒测试和白盒测试的优缺点)
        - [219.请列出你所知道的软件测试种类，至少5项](#219请列出你所知道的软件测试种类至少5项)
        - [220.Alpha测试与Beta测试的区别是什么？](#220alpha测试与beta测试的区别是什么)
        - [221.举例说明什么是Bug？一个bug report应包含什么关键字？](#221举例说明什么是bug一个bug-report应包含什么关键字)
    
    - [大数据](#大数据)
        - [242.找出1G的文件中高频词](#242找出1g的文件中高频词)
        - [243.一个大约有一万行的文本文件统计高频词](#243一个大约有一万行的文本文件统计高频词)
        - [244.怎么在海量数据中找出重复次数最多的一个？](#244怎么在海量数据中找出重复次数最多的一个)
        - [245.判断数据是否在大量数据中](#245判断数据是否在大量数据中)

<!-- /TOC -->
# Python精选
## 必备知识
### 1.Python的函数参数传递

看两个例子:

```python
a = 1
def fun(a):
    a = 2
fun(a)
print a  # 1
```

```python
a = []
def fun(a):
    a.append(1)
fun(a)
print a  # [1]
```

所有的变量都可以理解是内存中一个对象的“引用”，或者，也可以看似 C 中 `void*` 的感觉。

通过`id`来看引用`a`的内存地址可以比较理解：

```python
a = 1
def fun(a):
    print "func_in",id(a)   # func_in 41322472
    a = 2
    print "re-point",id(a), id(2)   # re-point 41322448 41322448
print "func_out",id(a), id(1)  # func_out 41322472 41322472
fun(a)
print a  # 1
```

注：具体的值在不同电脑上运行时可能不同。

可以看到，在执行完`a = 2`之后，`a`引用中保存的值，即内存地址发生变化，由原来`1`对象的所在地址变成了`2`这个实体对象的内存地址。

而第2个例子`a`引用保存的内存值就不会发生变化：

```python
a = []
def fun(a):
    print "func_in",id(a)  # func_in 53629256
    a.append(1)
print "func_out",id(a)     # func_out 53629256
fun(a)
print a  # [1]
```

这里记住的是类型是属于对象的，而不是变量。而对象有两种,“可更改”（mutable）与“不可更改”（immutable）对象。在python中，strings, tuples, 和numbers是不可更改的对象，而 list, dict, set 等则是可以修改的对象。(这就是这个问题的重点)

当一个引用传递给函数的时候,函数自动复制一份引用,这个函数里的引用和外边的引用没有半毛关系了.所以第一个例子里函数把引用指向了一个不可变对象,当函数返回的时候,外面的引用没半毛感觉.而第二个例子就不一样了,函数内的引用指向的是可变对象,对它的操作就和定位了指针地址一样,在内存里进行修改.

如果还不明白的话,这里有更好的解释: http://stackoverflow.com/questions/986006/how-do-i-pass-a-variable-by-reference

### 2.Python中的元类(metaclass)

这个非常的不常用,但是像ORM这种复杂的结构还是会需要的。

扩展阅读：

1. https://www.liaoxuefeng.com/wiki/1016959663602400/1017592449371072
2. [深入理解Python中的元类(metaclass)](https://www.cnblogs.com/JetpropelledSnake/p/9094103.html)

### 3.@staticmethod和@classmethod

Python其实有3个方法,即静态方法(staticmethod),类方法(classmethod)和实例方法,如下:

```python
def foo(x):
    print "executing foo(%s)"%(x)

class A(object):
    def foo(self,x):
        print "executing foo(%s,%s)"%(self,x)

    @classmethod
    def class_foo(cls,x):
        print "executing class_foo(%s,%s)"%(cls,x)

    @staticmethod
    def static_foo(x):
        print "executing static_foo(%s)"%x

a=A()
```

这里先理解下函数参数里面的self和cls。这个self和cls是对类或者实例的绑定，对于一般的函数来说我们可以这么调用`foo(x)`，这个函数就是最常用的，它的工作跟任何东西(类,实例)无关。对于实例方法，我们知道在类里每次定义方法的时候都需要绑定这个实例，就是`foo(self, x)`，为什么要这么做呢？因为实例方法的调用离不开实例，我们需要把实例自己传给函数，调用的时候是这样的`a.foo(x)`(其实是`foo(a, x)`)。类方法一样,只不过它传递的是类而不是实例，`A.class_foo(x)`。注意这里的self和cls可以替换别的参数，但是python的约定是这俩，还是不要改的好。

对于静态方法其实和普通的方法一样，不需要对谁进行绑定，唯一的区别是调用的时候需要使用 `a.static_foo(x)` 或者 `A.static_foo(x)` 来调用。

| \\      | 实例方法 | 类方法         | 静态方法        |
| :------ | :------- | :------------- | :-------------- |
| a = A() | a.foo(x) | a.class_foo(x) | a.static_foo(x) |
| A       | 不可用   | A.class_foo(x) | A.static_foo(x) |

更多关于这个问题:

1. http://stackoverflow.com/questions/136097/what-is-the-difference-between-staticmethod-and-classmethod-in-python
2. https://realpython.com/blog/python/instance-class-and-static-methods-demystified/

### 4.类变量和实例变量

**类变量：**

是可在类的所有实例之间共享的值（也就是说，它们不是单独分配给每个实例的）。例如下例中，num_of_instance 就是类变量，用于跟踪存在着多少个 Test 的实例。

**实例变量：**

实例化之后，每个实例单独拥有的变量。

```python
class Test(object):  
    num_of_instance = 0  
    def __init__(self, name):  
        self.name = name  
        Test.num_of_instance += 1  
  
if __name__ == '__main__':  
    print Test.num_of_instance   # 0
    t1 = Test('jack')  
    print Test.num_of_instance   # 1
    t2 = Test('lucy')  
    print t1.name , t1.num_of_instance  # jack 2
    print t2.name , t2.num_of_instance  # lucy 2
```

补充的例子：

```python
class Person:
    name="aaa"

p1=Person()
p2=Person()
p1.name="bbb"
print p1.name  # bbb
print p2.name  # aaa
print Person.name  # aaa
```

这里`p1.name="bbb"`是实例调用了类变量，这其实和上面第一个问题一样，就是函数传参的问题，`p1.name` 一开始是指向的类变量`name="aaa"`，但是在实例的作用域里把类变量的引用改变了，就变成了一个实例变量，`self.name` 不再引用Person的类变量name了。

可以看看下面的例子:

```python
class Person:
    name=[]

p1=Person()
p2=Person()
p1.name.append(1)
print p1.name  # [1]
print p2.name  # [1]
print Person.name  # [1]
```

参考:http://stackoverflow.com/questions/6470428/catch-multiple-exceptions-in-one-line-except-block

### 5.Python自省

这个也是python彪悍的特性。

自省就是面向对象的语言所写的程序在运行时，所能知道对象的类型，简单一句就是运行时能够获得对象的类型。比如type()，dir()，getattr()，hasattr()，isinstance()。

```python
a = [1,2,3]
b = {'a':1,'b':2,'c':3}
c = True
print type(a),type(b),type(c) # <type 'list'> <type 'dict'> <type 'bool'>
print isinstance(a,list)  # True
```

### 6.字典推导式

可能你见过列表推导式，却没有见过字典推导式，在2.7中才加入的：

```python
d = {key: value for (key, value) in iterable}
```

### 7.Python中单下划线和双下划线

```python
>>> class MyClass():
...     def __init__(self):
...             self.__superprivate = "Hello"
...             self._semiprivate = ", world!"
...
>>> mc = MyClass()
>>> print mc.__superprivate
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: myClass instance has no attribute '__superprivate'
>>> print mc._semiprivate
, world!
>>> print mc.__dict__
{'_MyClass__superprivate': 'Hello', '_semiprivate': ', world!'}
```

`__foo__`：一种约定，Python内部的名字，用来区别其他用户自定义的命名，以防冲突，就是例如：`__init__()`，`__del__()`，`__call__()` 这些特殊方法；

`_foo`：一种约定，用来指定变量私有，程序员用来指定私有变量的一种方式。不能用 `from module import *` 导入，其他方面和公有一样访问；

`__foo`：这个有真正的意义：解析器用 `_classname__foo` 来代替这个名字，以区别和其他类相同的命名，它无法直接像公有成员一样随便访问，通过 `对象名._类名__xxx` 这样的方式可以访问。

详情见:http://stackoverflow.com/questions/1301346/the-meaning-of-a-single-and-a-double-underscore-before-an-object-name-in-python

或者: http://www.zhihu.com/question/19754941

### 8.字符串格式化:%和.format

`.format` 在许多方面看起来更便利。对于 `%` 最烦人的是它无法同时传递一个变量和元组。你可能会想下面的代码不会有什么问题:

```python
"hi there %s" % name
```

但是，如果name恰好是(1,2,3)，它将会抛出一个 TypeError 异常。为了保证它总是正确的，你必须这样做：

```python
"hi there %s" % (name,)   # 提供一个单元素的数组而不是一个参数
```

`.format` 就没有这些问题。

为了和Python2.5兼容(譬如logging库建议使用`%`([issue #4](https://github.com/taizilongxu/interview_python/issues/4)))

http://stackoverflow.com/questions/5082452/python-string-formatting-vs-format

### 9.迭代器和生成器

这里有个关于生成器的创建问题面试官有考：
问：将列表生成式中 `[]` 改成 `()` 之后数据结构是否改变？ 
答案：是，从列表变为生成器。

```python
>>> L = [x*x for x in range(10)]
>>> L
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
>>> g = (x*x for x in range(10))
>>> g
<generator object <genexpr> at 0x0000028F8B774200>
```

通过列表生成式，可以直接创建一个列表。但是，受到内存限制，列表容量肯定是有限的。而且，创建一个包含百万元素的列表，不仅是占用很大的内存空间，如：我们只需要访问前面的几个元素，后面大部分元素所占的空间都是浪费的。因此，没有必要创建完整的列表（节省大量内存空间）。在Python中，我们可以采用生成器：边循环，边计算的机制—>generator

迭代器是遵循迭代协议的对象。用户可以使用 iter() 以从任何序列得到迭代器（如 list, tuple, dictionary, set 等）。另一个方法则是创建一个另一种形式的迭代器 —— generator 。要获取下一个元素，则使用成员函数 next()（Python 2）或函数 next() function （Python 3） 。当没有元素时，则引发 StopIteration 此例外。若要实现自己的迭代器，则只要实现 next()（Python 2）或 `__next__`()（ Python 3）

生成器（Generator），只是在需要返回数据的时候使用yield语句。每次next()被调用时，生成器会返回它脱离的位置（它记忆语句最后一次执行的位置和所有的数据值）

区别： 生成器能做到迭代器能做的所有事，而且因为自动创建iter()和next()方法，生成器显得特别简洁，而且生成器也是高效的，使用生成器表达式取代列表解析可以同时节省内存。除了创建和保存程序状态的自动方法，当发生器终结时，还会自动抛出StopIteration异常。

官方介绍：https://docs.python.org/3/tutorial/classes.html#iterators

### 10.`*args` 和 `**kwargs`

用`*args`和`**kwargs`只是为了方便并没有强制使用它们.

当你不确定你的函数里将要传递多少参数时你可以用`*args`.例如,它可以传递任意数量的参数:

```python
>>> def print_everything(*args):
        for count, thing in enumerate(args):
...         print '{0}. {1}'.format(count, thing)
...
>>> print_everything('apple', 'banana', 'cabbage')
1. apple
2. banana
3. cabbage
```

相似的,`**kwargs`允许你使用没有事先定义的参数名:

```python
>>> def table_things(**kwargs):
...     for name, value in kwargs.items():
...         print '{0} = {1}'.format(name, value)
...
>>> table_things(apple = 'fruit', cabbage = 'vegetable')
cabbage = vegetable
apple = fruit
```

你也可以混着用.命名参数首先获得参数值然后所有的其他参数都传递给`*args`和`**kwargs`.命名参数在列表的最前端.例如:

```python
def table_things(titlestring, **kwargs)
```

`*args`和`**kwargs`可以同时在函数的定义中,但是`*args`必须在`**kwargs`前面.

当调用函数时你也可以用`*`和`**`语法.例如:

```python
>>> def print_three_things(a, b, c):
...     print 'a = {0}, b = {1}, c = {2}'.format(a,b,c)
...
>>> mylist = ['aardvark', 'baboon', 'cat']
>>> print_three_things(*mylist)

a = aardvark, b = baboon, c = cat
```

就像你看到的一样,它可以传递列表(或者元组)的每一项并把它们解包.注意必须与它们在函数里的参数相吻合.当然,你也可以在函数定义或者函数调用时用 `*`.

http://stackoverflow.com/questions/3394835/args-and-kwargs

### 11.面向切面编程AOP和装饰器

这个AOP一听起来有点懵,同学面阿里的时候就被问懵了...

装饰器是一个很著名的设计模式，经常被用于有切面需求的场景，较为经典的有插入日志、性能测试、事务处理等。装饰器是解决这类问题的绝佳设计，有了装饰器，我们就可以抽离出大量函数中与函数功能本身无关的雷同代码并继续重用。

概括的讲，**装饰器的作用就是为已经存在的对象添加额外的功能。**

推荐：[Python 面向切面编程 AOP 和装饰器](https://mp.weixin.qq.com/s?src=11&timestamp=1619481363&ver=3033&signature=qVv9*2qGxuNtx6Ux5I7veLnQ5NS*CFBHXVzQGduiKVX8hodBvoasdAknUqzWoYBOuYQxnXKAWBOEwLEtq6SYtRPFbxh*QBQmtv6TvjpXwAbdC4X3xHN0FVydhEoQkbv8&new=1)

### 12.鸭子类型

“当看到一只鸟走起来像鸭子、游泳起来像鸭子、叫起来也像鸭子，那么这只鸟就可以被称为鸭子。”

我们并不关心对象是什么类型，到底是不是鸭子，只关心行为。

比如在python中，有很多file-like的东西，比如StringIO,GzipFile,socket。它们有很多相同的方法，我们把它们当作文件使用。

又比如list.extend()方法中,我们并不关心它的参数是不是list,只要它是可迭代的,所以它的参数可以是list/tuple/dict/字符串/生成器等.

鸭子类型在动态语言中经常使用，非常灵活，使得python不想java那样专门去弄一大堆的设计模式。

推荐：https://cloud.tencent.com/developer/article/1484390

### 13.Python中重载

引自知乎:http://www.zhihu.com/question/20053359

函数重载主要是为了解决两个问题。

1. 可变参数类型。
2. 可变参数个数。

另外，一个基本的设计原则是，仅仅当两个函数除了参数类型和参数个数不同以外，其功能是完全相同的，此时才使用函数重载，如果两个函数的功能其实不同，那么不应当使用重载，而应当使用一个名字不同的函数。

好吧，那么对于情况 1，函数功能相同，但是参数类型不同，python 如何处理？答案是根本不需要处理，因为 python 可以接受任何类型的参数，如果函数的功能相同，那么不同的参数类型在 python 中很可能是相同的代码，没有必要做成两个不同函数。

那么对于情况 2，函数功能相同，但参数个数不同，python 如何处理？大家知道，答案就是缺省参数。对那些缺少的参数设定为缺省参数即可解决问题。因为你假设函数功能相同，那么那些缺少的参数终归是需要用的。

好了，鉴于情况 1 跟情况 2 都有了解决方案，python 自然就不需要函数重载了。

### 14.新式类和旧式类

这个面试官问了，我说了老半天，不知道他问的真正意图是什么。

[stackoverflow](http://stackoverflow.com/questions/54867/what-is-the-difference-between-old-style-and-new-style-classes-in-python)

这篇文章很好的介绍了新式类的特性: http://www.cnblogs.com/btchenguang/archive/2012/09/17/2689146.html

新式类很早在2.2就出现了，所以旧式类完全是兼容的问题，Python3 里的类全部都是新式类。

这里有一个 MRO 问题可以了解下(新式类继承是根据C3算法，旧式类是深度优先)，<Python核心编程>里讲的也很多。

> 一个旧式类的深度优先的例子

```python
class A():
    def foo1(self):
        print "A"
class B(A):
    def foo2(self):
        pass
class C(A):
    def foo1(self):
        print "C"
class D(B, C):
    pass

d = D()
d.foo1()

# A
```

**按照经典类的查找顺序`从左到右深度优先`的规则，在访问`d.foo1()`的时候,D这个类是没有的..那么往上查找,先找到B,里面没有,深度优先,访问A,找到了foo1(),所以这时候调用的是A的foo1()，从而导致C重写的foo1()被绕过**

### 15.`__new__`和`__init__`的区别

这个`__new__`确实很少见到,先做了解吧.

1. `__new__`是一个静态方法,而`__init__`是一个实例方法.
2. `__new__`方法会返回一个创建的实例,而`__init__`什么都不返回.
3. 只有在`__new__`返回一个cls的实例时后面的`__init__`才能被调用.
4. 当创建一个新实例时调用`__new__`,初始化一个实例时用`__init__`.

[stackoverflow](http://stackoverflow.com/questions/674304/pythons-use-of-new-and-init)

ps: `__metaclass__`是创建类时起作用.所以我们可以分别使用`__metaclass__`,`__new__`和`__init__`来分别在类创建,实例创建和实例初始化的时候做一些小手脚.

### 16.单例模式

> ​	单例模式是一种常用的软件设计模式。在它的核心结构中只包含一个被称为单例类的特殊类。通过单例模式可以保证系统中一个类只有一个实例而且该实例易于外界访问，从而方便对实例个数的控制并节约系统资源。如果希望在系统中某个类的对象只能存在一个，单例模式是最好的解决方案。
>
> `__new__()`在`__init__()`之前被调用，用于生成实例对象。利用这个方法和类的属性的特点可以实现设计模式的单例模式。单例模式是指创建唯一对象，单例模式设计的类只能实例
> **这个绝对常考啊.绝对要记住1~2个方法,当时面试官是让手写的.**

#### 1 使用`__new__`方法

```python
class Singleton(object):
    def __new__(cls, *args, **kw):
        if not hasattr(cls, '_instance'):
            orig = super(Singleton, cls)
            cls._instance = orig.__new__(cls, *args, **kw)
        return cls._instance

class MyClass(Singleton):
    a = 1
```

#### 2 共享属性

创建实例时把所有实例的`__dict__`指向同一个字典,这样它们具有相同的属性和方法.

```python
class Borg(object):
    _state = {}
    def __new__(cls, *args, **kw):
        ob = super(Borg, cls).__new__(cls, *args, **kw)
        ob.__dict__ = cls._state
        return ob

class MyClass2(Borg):
    a = 1
```

#### 3 装饰器版本

```python
def singleton(cls):
    instances = {}
    def getinstance(*args, **kw):
        if cls not in instances:
            instances[cls] = cls(*args, **kw)
        return instances[cls]
    return getinstance

@singleton
class MyClass:
  ...
```

#### 4 import方法

作为python的模块是天然的单例模式

```python
class My_Singleton(object):
    def foo(self):
        pass

my_singleton = My_Singleton()

# to use
from mysingleton import my_singleton

my_singleton.foo()
```

推荐：[单例模式详细解释](https://www.cnblogs.com/huchong/p/8244279.html)

### 17.Python中的作用域

函数作用域的 LEGB 顺序：

L：local 函数内部作用域

E：enclosing 函数内部与内嵌函数之间

G：global 全局作用域

B：build-in 内置作用域

Python 中，一个变量的作用域总是由在代码中被赋值的地方所决定的。

当 Python 遇到一个变量的话他会按照这样的顺序进行搜索：

本地作用域（Local）→当前作用域被嵌入的本地作用域（Enclosing locals）→全局/模块作用域（Global）→内置作用域（Built-in）

### 18.GIL线程全局锁

线程全局锁(Global Interpreter Lock),即Python为了保证线程安全而采取的独立线程运行的限制,说白了就是一个核只能在同一时间运行一个线程.**对于io密集型任务，python的多线程起到作用，但对于cpu密集型任务，python的多线程几乎占不到任何优势，还有可能因为争夺资源而变慢。**

见[Python 最难的问题](http://www.oschina.net/translate/pythons-hardest-problem)
推荐：https://zhuanlan.zhihu.com/p/75780308

解决办法就是多进程和下面的协程(协程也只是单CPU,但是能减小切换代价提升性能).

### 19.协程

知乎被问到了,呵呵哒,跪了

简单点说协程是进程和线程的升级版,进程和线程都面临着内核态和用户态的切换问题而耗费许多切换时间,而协程就是用户自己控制切换的时机,不再需要陷入系统的内核态.

Python里最常见的yield就是协程的思想!可以查看第九个问题.

推荐：https://www.liaoxuefeng.com/wiki/897692888725344/923057403198272

### 20.闭包

闭包(closure)是函数式编程的重要的语法结构。闭包也是一种组织代码的结构，它同样提高了代码的可重复使用性。

当一个内嵌函数引用其外部作用域的变量,我们就会得到一个闭包. 总结一下,创建一个闭包必须满足以下几点:

1. 必须有一个内嵌函数
2. 内嵌函数必须引用外部函数中的变量
3. 外部函数的返回值必须是内嵌函数

感觉闭包还是有难度的,几句话是说不明白的,还是查查相关资料.

重点是函数运行后并不会被撤销,就像16题的instance字典一样,当函数运行完后,instance并不被销毁,而是继续留在内存空间里.这个功能类似类里的类变量,只不过迁移到了函数上.

闭包就像个空心球一样,你知道外面和里面,但你不知道中间是什么样.

### 21.lambda函数

其实就是一个匿名函数,为什么叫lambda?因为和后面的函数式编程有关.

推荐: [知乎](http://www.zhihu.com/question/20125256)

### 22.Python函数式编程

这个需要适当的了解一下吧,毕竟函数式编程在Python中也做了引用.

推荐: [酷壳](http://coolshell.cn/articles/10822.html)

python中函数式编程支持:

filter 函数的功能相当于过滤器。调用一个布尔函数`bool_func`来迭代遍历每个seq中的元素；返回一个使`bool_seq`返回值为true的元素的序列。

```python
>>>a = [1,2,3,4,5,6,7]
>>>b = filter(lambda x: x > 5, a)
>>>print b
>>>[6,7]
```

map函数是对一个序列的每个项依次执行函数，下面是对一个序列每个项都乘以2：

```python
>>> a = map(lambda x:x*2,[1,2,3])
>>> list(a)
[2, 4, 6]
```

reduce函数是对一个序列的每个项迭代调用函数，下面是求3的阶乘：

```python
>>> reduce(lambda x,y:x*y,range(1,4))
6
```

### 23.Python里的拷贝

引用和copy(),deepcopy()的区别

```python
import copy
a = [1, 2, 3, 4, ['a', 'b']]  #原始对象

b = a  #赋值，传对象的引用
c = copy.copy(a)  #对象拷贝，浅拷贝
d = copy.deepcopy(a)  #对象拷贝，深拷贝

a.append(5)  #修改对象a
a[4].append('c')  #修改对象a中的['a', 'b']数组对象

print 'a = ', a
print 'b = ', b
print 'c = ', c
print 'd = ', d

输出结果：
a =  [1, 2, 3, 4, ['a', 'b', 'c'], 5]
b =  [1, 2, 3, 4, ['a', 'b', 'c'], 5]
c =  [1, 2, 3, 4, ['a', 'b', 'c']]
d =  [1, 2, 3, 4, ['a', 'b']]
```

### 24.Python垃圾回收机制

Python GC 主要使用引用计数（reference counting）来跟踪和回收垃圾。在引用计数的基础上，通过“标记-清除”（mark and sweep）解决容器对象可能产生的循环引用问题，通过“分代回收”（generation collection）以空间换时间的方法提高垃圾回收效率。

#### 1 引用计数

PyObject是每个对象必有的内容，其中`ob_refcnt`就是做为引用计数。当一个对象有新的引用时，它的`ob_refcnt`就会增加，当引用它的对象被删除，它的`ob_refcnt`就会减少.引用计数为0时，该对象生命就结束了。

优点:

1. 简单
2. 实时性

缺点:

1. 维护引用计数消耗资源
2. 循环引用

#### 2 标记-清除机制

基本思路是先按需分配，等到没有空闲内存的时候从寄存器和程序栈上的引用出发，遍历以对象为节点、以引用为边构成的图，把所有可以访问到的对象打上标记，然后清扫一遍内存空间，把所有没标记的对象释放。

#### 3 分代技术

分代回收的整体思想是：将系统中的所有内存块根据其存活时间划分为不同的集合，每个集合就成为一个“代”，垃圾收集频率随着“代”的存活时间的增大而减小，存活时间通常利用经过几次垃圾回收来度量。

Python默认定义了三代对象集合，索引数越大，对象存活时间越长。

举例：
当某些内存块M经过了3次垃圾收集的清洗之后还存活时，我们就将内存块M划到一个集合A中去，而新分配的内存都划分到集合B中去。当垃圾收集开始工作时，大多数情况都只对集合B进行垃圾回收，而对集合A进行垃圾回收要隔相当长一段时间后才进行，这就使得垃圾收集机制需要处理的内存少了，效率自然就提高了。在这个过程中，集合B中的某些内存块由于存活时间长而会被转移到集合A中，当然，集合A中实际上也存在一些垃圾，这些垃圾的回收会因为这种分代的机制而被延迟。

推荐：https://zhuanlan.zhihu.com/p/83251959

### 25.Python的List

推荐: http://www.jianshu.com/p/J4U6rR

### 26.Python的is

is是对比地址,==是对比值

### 27.read,readline和readlines

- read        读取整个文件
- readline    读取下一行,使用生成器方法
- readlines   读取整个文件到一个迭代器以供我们遍历

### 28.Python2和3的区别

推荐：[Python 2.7.x 与 Python 3.x 的主要差异](http://chenqx.github.io/2014/11/10/Key-differences-between-Python-2-7-x-and-Python-3-x/)

### 29.super init

super() lets you avoid referring to the base class explicitly, which can be nice. But the main advantage comes with multiple inheritance, where all sorts of fun stuff can happen. See the standard docs on super if you haven't already.

Note that the syntax changed in Python 3.0: you can just say super().`__init__`() instead of super(ChildB, self).`__init__`() which IMO is quite a bit nicer.

http://stackoverflow.com/questions/576169/understanding-python-super-with-init-methods

[Python2.7中的super方法浅见](http://blog.csdn.net/mrlevo520/article/details/51712440)

https://python3-cookbook.readthedocs.io/zh_CN/latest/c08/p07_calling_method_on_parent_class.html

### 30.range and xrange

都在循环时使用，xrange内存性能更好。
for i in range(0, 20):
for i in xrange(0, 20):
What is the difference between range and xrange functions in Python 2.X?
 range creates a list, so if you do range(1, 10000000) it creates a list in memory with 9999999 elements.
 xrange is a sequence object that evaluates lazily.

http://stackoverflow.com/questions/94935/what-is-the-difference-between-range-and-xrange-functions-in-python-2-x


### 31 python 字典的内部实现原理介绍

https://cloud.tencent.com/developer/article/1664241

# Python基础

## 文件操作

### 31.Python 处理文件

有一个 jsonline 格式的文件 file.txt 大小约为 10K，很简单，直接读取处理就可以了。

```python
def get_lines():
    with open('file.txt','rb') as f:
        return f.readlines()

if __name__ == '__main__':
    for e in get_lines():
        process(e) # 处理每一行数据
```

现在要处理一个大小为10G的文件，但是内存只有4G，如果在只修改 `get_lines` 函数而其他代码保持不变的情况下，应该如何实现？需要考虑的问题都有那些？

生成器方式：

```python
def get_lines():
    with open('file.txt','rb') as f:
        for i in f:
            yield i
```

分块读取方式：

```python
from mmap import mmap

def get_lines(fp):
    with open(fp,"r+") as f:
        m = mmap(f.fileno(), 0)
        tmp = 0
        for i, char in enumerate(m):
            if char==b"\n":
                yield m[tmp:i+1].decode()
                tmp = i+1

if __name__=="__main__":
    for i in get_lines("fp_some_huge_file"):
        print(i)
```

要考虑的问题有：内存只有4G无法一次性读入10G文件，需要分批读入，分批读入数据要记录每次读入数据的位置。分批每次读取数据的大小，太小会在读取操作花费过多时间。
https://stackoverflow.com/questions/30294146/python-fastest-way-to-process-large-file

### 32.遍历文件夹

```python
import os

def print_directory_contents(sPath):
    """
    这个函数接收文件夹的名称作为输入参数
    返回该文件夹中文件的路径
    以及其包含文件夹中文件的路径
    """
    for s_child in os.listdir(s_path):
        s_child_path = os.path.join(s_path, s_child)
        if os.path.isdir(s_child_path):
            print_directory_contents(s_child_path)
        else:
            print(s_child_path)
```

## 模块与包

### 33.输入日期，判断这一天是这一年的第几天？

```python
import datetime

def dayofyear():
    year = input("请输入年份: ")
    month = input("请输入月份: ")
    day = input("请输入天: ")
    date1 = datetime.date(year=int(year),month=int(month),day=int(day))
    date2 = datetime.date(year=int(year),month=1,day=1)
    return (date1-date2).days+1
```

### 34.打乱一个排好序的list对象

```python
import random

alist = [1,2,3,4,5]
random.shuffle(alist)
print(alist)
```

## 数据类型

### 35.将字典按value值进行排序

```python
sorted(d.items(), key=lambda x:x[1])
```

### 36.请反转字符串 "aStr"

```python
print("aStr"[::-1])
```

### 37.将字符串 "k:1|k1:2|k2:3|k3:4" 处理成字典 {k:1,k1:2,...}

```python
str1 = "k:1|k1:2|k2:3|k3:4"

def str2dict(str1):
    dict1 = {}
    for iterms in str1.split('|'):
        key, value = iterms.split(':')
        dict1[key] = value
    return dict1
```

或者直接用字典推导式：

```python
d = {k:int(v) for t in str1.split("|") for k, v in (t.split(":"), )}
```

### 38.列表切片

```python
list = ['a','b','c','d','e']
print(list[10:])
```

代码将输出 `[]`，不会产生IndexError错误，就像所期望的那样，尝试用超出成员的个数的index来获取某个列表的成员。例如，尝试获取list[10]和之后的成员，会导致IndexError。

然而，尝试获取列表的切片，开始的index超过了成员个数不会产生IndexError，而是仅仅返回一个空列表。这成为特别让人恶心的疑难杂症，因为运行的时候没有错误产生，导致Bug很难被追踪到。

### 39.写一个列表生成式，产生一个公差为11的等差数列

```python
print([x*11 for x in range(10)])
```

### 40.给定两个列表，找出他们相同的元素和不同的元素

```python
list1 = [1,2,3]
list2 = [3,4,5]

set1 = set(list1)
set2 = set(list2)

print(set1 & set2)
print(set1 ^ set2)
```

## 企业面试题

### 41.python中内置的数据结构有几种
a. 整型 int、 长整型 long、浮点型 float、 复数 complex

b. 字符串 str、 列表 list、 元组 tuple

c. 字典 dict 、 集合 set

d. Python3 中没有 long，只有无限精度的 int

### 42.反转一个整数，例如-123 --> -321

```python
class Solution(object):
    def reverse(self,x):
        if -10<x<10:
            return x

        str_x = str(x)
        if str_x[0] !="-":
            str_x = str_x[::-1]
            x = int(str_x)
        else:
            str_x = str_x[1:][::-1]
            x = int(str_x)
            x = -x

        return x if -2147483648<x<2147483647 else 0

if __name__ == '__main__':
    s = Solution()
    reverse_int = s.reverse(-120)
    print(reverse_int)
```

### 43.实现遍历目录与子目录，抓取.pyc文件

第一种方法：

```python
import os

def get_files(dir,suffix):
    res = []
    for root,dirs,files in os.walk(dir):
        for filename in files:
            name,suf = os.path.splitext(filename)
            if suf == suffix:
                res.append(os.path.join(root,filename))
    print(res)

get_files("./",'.pyc')
```

第二种方法：

```python
import os

def pick(obj):
    if ob.endswith(".pyc"):
        print(obj)
    
def scan_path(ph):
    file_list = os.listdir(ph)
    for obj in file_list:
        if os.path.isfile(obj):
            pick(obj)
        elif os.path.isdir(obj):
            scan_path(obj)
    
if __name__=='__main__':
    path = input('输入目录')
    scan_path(path)
```

第三种方法：

```python
from glob import iglob

def func(fp, postfix):
    for i in iglob(f"{fp}/**/*{postfix}", recursive=True):
        print(i)

if __name__ == "__main__":
    postfix = ".pyc"
    func("K:\Python_script", postfix)
```

### 44.Python遍历列表时删除元素的正确做法

a.遍历时在新列表操作，删除时在原来的列表操作：

```python
a = [1,2,3,4,5,6,7,8]
print(id(a))
print(id(a[:]))
for i in a[:]:
    if i>5:
        pass
    else:
        a.remove(i)
    print(a)
print(id(a))
```

b.使用filter：

```python
a=[1,2,3,4,5,6,7,8]
b = filter(lambda x: x>5,a)
print(list(b))
```

c.列表生成式：

```python
a=[1,2,3,4,5,6,7,8]
b = [i for i in a if i>5]
print(b)
```

d.倒序删除：

因为列表总是‘向前移’，所以可以倒序遍历，即使后面的元素被修改了，还没有被遍历的元素和其坐标还是保持不变的。

```python
a=[1,2,3,4,5,6,7,8]
print(id(a))
for i in range(len(a)-1,-1,-1):
    if a[i]>5:
        pass
    else:
        a.remove(a[i])
print(id(a))
print(a)
```

### 45.字符串 `"123"` 转换成 `123`，不使用内置api，例如 `int()`

方法一： 利用 `str` 函数

```python
def atoi(s):
    num = 0
    for v in s:
        for j in range(10):
            if v == str(j):
                num = num * 10 + j
    return num
```

方法二： 利用 `ord` 函数

```python
def atoi(s):
    num = 0
    for v in s:
        num = num * 10 + ord(v) - ord('0')
    return num
```

方法三: 利用 `eval` 函数

```python
def atoi(s):
    num = 0
    for v in s:
        t = "%s * 1" % v
        n = eval(t)
        num = num * 10 + n
    return num
```

方法四: 结合方法二，使用 `reduce`，一行解决

```python
from functools import reduce

def atoi(s):
    return reduce(lambda num, v: num * 10 + ord(v) - ord('0'), s, 0)
```
  
### 46.统计一个文本中单词频次最高的10个单词

```python
# 方法一
import re

def test(filepath):
    distone = {}

    with open(filepath) as f:
        for line in f:
            line = re.sub("\W+", " ", line)
            lineone = line.split()
            for keyone in lineone:
                if not distone.get(keyone):
                    distone[keyone] = 1
                else:
                    distone[keyone] += 1

    num_ten = sorted(distone.items(), key=lambda x:x[1], reverse=True)[:10]
    num_ten =[x[0] for x in num_ten]
    return num_ten
 
# 方法二 
# 使用 built-in 的 Counter 里面的 most_common
import re
from collections import Counter

def test2(filepath):
    with open(filepath) as f:
        return list(map(lambda c: c[0], Counter(re.sub("\W+", " ", f.read()).split()).most_common(10)))
```

### 47.阅读一下代码他们的输出结果是什么？

```python
def multi():
    return [lambda x : i*x for i in range(4)]
print([m(3) for m in multi()])
```

正确答案是[9,9,9,9]，而不是[0,3,6,9]产生的原因是Python的闭包的后期绑定导致的，这意味着在闭包中的变量是在内部函数被调用的时候被查找的，因为，最后函数被调用的时候，for循环已经完成, i 的值最后是3,因此每一个返回值的i都是3,所以最后的结果是[9,9,9,9]

你如何修改上面的multipliers的定义产生想要的结果？

上述问题产生的原因是python闭包的延迟绑定。这意味着内部函数被调用时，参数的值在闭包内进行查找。因此，当任何由multipliers()返回的函数被调用时,i的值将在附近的范围进行查找。那时，不管返回的函数是否被调用，for循环已经完成，i被赋予了最终的值3.

```python
def multipliers():
    for i in range(4):
        yield lambda x: i *x
```

```python
def multipliers():
    return [lambda x,i = i: i*x for i in range(4)]
```

# Python高级

## 元类

### 48.介绍Cython，Pypy Cpython Numba各有什么缺点
### 49.请描述抽象类和接口类的区别和联系
### 50.Python中如何动态获取和设置对象的属性

## 内存管理与垃圾回收机制

### 51.哪些操作会导致Python内存溢出，怎么处理

### 52.内存泄露是什么？如何避免？

**内存泄漏**指由于疏忽或错误造成程序未能释放已经不再使用的内存。内存泄漏并非指内存在物理上的消失，而是应用程序分配某段内存后，由于设计错误，导致在释放该段内存之前就失去了对该段内存的控制，从而造成了内存的浪费。

有`__del__()`函数的对象间的循环引用是导致内存泄露的主凶。不使用一个对象时使用: del object 来删除一个对象的引用计数就可以有效防止内存泄露问题。

通过 Python 扩展模块 gc 来查看不能回收的对象的详细信息。

可以通过 sys.getrefcount(obj) 来获取对象的引用计数，并根据返回值是否为0来判断是否内存泄露。

## 函数

### 53.手写一个判断时间的装饰器

```python
import datetime


class TimeException(Exception):
    def __init__(self, exception_info):
        super().__init__()
        self.info = exception_info

    def __str__(self):
        return self.info


def timecheck(func):
    def wrapper(*args, **kwargs):
        if datetime.datetime.now().year == 2019:
            func(*args, **kwargs)
        else:
            raise TimeException("函数已过时")

    return wrapper


@timecheck
def test(name):
    print("Hello {}, 2019 Happy".format(name))


if __name__ == "__main__":
    test("backbp")
```

### 54.带参数的装饰器

a.带定长参数的装饰器：

```python
def new_func(func):
    def wrappedfun(username, passwd):
        if username == 'root' and passwd == '123456789':
            print('通过认证')
            print('开始执行附加功能')
            return func()
       	else:
            print('用户名或密码错误')
            return
    return wrappedfun

@new_func
def origin():
    print('开始执行函数')
origin('root','123456789')
```

b.带不定长参数的装饰器：

```python
def new_func(func):
    def wrappedfun(*parts):
        if parts:
            counts = len(parts)
            print('本系统包含 ', end='')
            for part in parts:
                print(part, ' ',end='')
            print('等', counts, '部分')
            return func()
        else:
            print('用户名或密码错误')
            return func()
   return wrappedfun
```

### 55.线程安全的装饰器

### 56.交换两个变量的值

```python
a, b = b, a
```

### 57.hasattr() getattr() setattr() 函数使用详解

hasattr(object,name)函数:

判断一个对象里面是否有name属性或者name方法，返回bool值，有name属性（方法）返回True，否则返回False。

```python
class function_demo(object):
    name = 'demo'
    def run(self):
        return "hello function"
functiondemo = function_demo()
res = hasattr(functiondemo, "name") # 判断对象是否有name属性，True
res = hasattr(functiondemo, "run") # 判断对象是否有run方法，True
res = hasattr(functiondemo, "age") # 判断对象是否有age属性，False
print(res)
```

getattr(object, name[,default])函数：

获取对象object的属性或者方法，如果存在则打印出来，如果不存在，打印默认值，默认值可选。注意：如果返回的是对象的方法，则打印结果是：方法的内存地址，如果需要运行这个方法，可以在后面添加括号().

```python
functiondemo = function_demo()
getattr(functiondemo, "name")# 获取name属性，存在就打印出来 --- demo
getattr(functiondemo, "run") # 获取run 方法，存在打印出方法的内存地址
getattr(functiondemo, "age") # 获取不存在的属性，报错
getattr(functiondemo, "age", 18)# 获取不存在的属性，返回一个默认值
```

setattr(object, name, values)函数：

给对象的属性赋值，若属性不存在，先创建再赋值

```python
class function_demo(object):
    name = "demo"
    def run(self):
        return "hello function"
functiondemo = function_demo()
res = hasattr(functiondemo, "age") # 判断age属性是否存在，False
print(res)
setattr(functiondemo, "age", 18) # 对age属性进行赋值，无返回值
res1 = hasattr(functiondemo, "age") # 再次判断属性是否存在，True
```

综合使用

```python
class function_demo(object):
    name = "demo"
    def run(self):
        return "hello function"

functiondemo = function_demo()
res = hasattr(functiondemo, "addr") # 先判断是否存在
if res:
    addr = getattr(functiondemo, "addr")
    print(addr)
else:
    addr = getattr(functiondemo, "addr", setattr(functiondemo, "addr", "北京首都"))
    print(addr)
```

##  设计模式

### 58.对装饰器的理解，并写出一个计时器记录方法执行性能的装饰器
装饰器本质上是一个callable object，它可以让其他函数在不需要做任何代码变动的前提下增加额外功能，装饰器的返回值也是一个函数对象。

```python
import time
from functools import wraps

def timeit(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.clock()
        ret = func(*args, **kwargs)
        end = time.clock()
        print('used:',end-start)
        return ret
    
    return wrapper

@timeit
def foo():
    print('in foo()'foo())
```

### 59.Python中yield的用法

yield就是保存当前程序执行状态。你用for循环的时候，每次取一个元素的时候就会计算一次。用yield的函数叫generator,和iterator一样，它的好处是不用一次计算所有元素，而是用一次算一次，可以节省很多空间，generator每次计算需要上一次计算结果，所以用yield,否则一return，上次计算结果就没了

## 面向对象

### 60.Python的魔法方法

魔法方法就是可以给你的类增加魔力的特殊方法，如果你的对象实现（重载）了这些方法中的某一个，那么这个方法就会在特殊的情况下被Python所调用，你可以定义自己想要的行为，而这一切都是自动发生的，它们经常是两个下划线包围来命名的（比如`__init___`,`__len__`),Python的魔法方法是非常强大的所以了解其使用方法也变得尤为重要!

`__init__`构造器，当一个实例被创建的时候初始化的方法，但是它并不是实例化调用的第一个方法。

`__new__`才是实例化对象调用的第一个方法，它只取下cls参数，并把其他参数传给`__init___`.

`___new__`很少使用，但是也有它适合的场景，尤其是当类继承自一个像元祖或者字符串这样不经常改变的类型的时候。

`__call__`让一个类的实例像函数一样被调用

`__getitem__`定义获取容器中指定元素的行为，相当于self[key]

`__getattr__`定义当用户试图访问一个不存在属性的时候的行为。

`__setattr__`定义当一个属性被设置的时候的行为

`__getattribute___`定义当一个属性被访问的时候的行为

### 61.面向对象中怎么实现只读属性

将对象私有化，通过共有方法提供一个读取数据的接口

```python
class person:
    def __init__(self, x):
        self.__age = 10
    def age(self):
        return self.__age
t = person(22)
# t.__age =100
print(t.age())
```

最好的方法

```python
class MyCls(object):
    __weight = 50
    
    @property
    def weight(self):
        return self.__weight
```

### 62.谈谈你对面向对象的理解

面向对象是相当于面向过程而言的，面向过程语言是一种基于功能分析的，以算法为中心的程序设计方法，而面向对象是一种基于结构分析的，以数据为中心的程序设计思想。在面向对象语言中有一个很重要的东西，叫做类。面向对象有三大特性：封装、继承、多态。

## 正则表达式

### 63.请写出一段代码用正则匹配出ip

```python
def ip_match(ip_str):
    partterns = re.compile(r"(2(5[0-5]{1}|[0-4]\d{1})|[0-1]?\d{1,2})(\.(2(5[0-5]{1}|[0-4]\d{1})|[0-1]?\d{1,2})){3}")
    print(partterns.search(ip_str).group(0))
```

### 64.Python字符串查找和替换？
### 65.正则表达式贪婪与非贪婪模式的区别？
### 66.写出开头匹配字母和下划线，末尾是数字的正则表达式？
### 67.怎么过滤评论中的表情？
### 68.Python里match与search的区别？

match 方法用于查找字符串的头部（也可以指定起始位置），它是一次匹配，只要找到了一个匹配的结果就返回，而不是查找所有匹配的结果。它的一般使用形式如下：

match(string[, pos[, endpos]])
其中，string 是待匹配的字符串，pos 和 endpos 是可选参数，指定字符串的起始和终点位置，默认值分别是 0 和 len (字符串长度)。因此，**当你不指定 pos 和 endpos 时，match 方法默认匹配字符串的头部**。

当匹配成功时，返回一个 Match 对象，如果没有匹配上，则返回 None。

```python
>>> import re
>>> pattern = re.compile(r'\d+')                    # 用于匹配至少一个数字
>>> m = pattern.match('one12twothree34four')        # 查找头部，没有匹配
>>> print m
None
>>> m = pattern.match('one12twothree34four', 2, 10) # 从'e'的位置开始匹配，没有匹配
>>> print m
None
>>> m = pattern.match('one12twothree34four', 3, 10) # 从'1'的位置开始匹配，正好匹配
>>> print m                                         # 返回一个 Match 对象
<_sre.SRE_Match object at 0x10a42aac0>
>>> m.group(0)   # 可省略 0
'12'
>>> m.start(0)   # 可省略 0
3
>>> m.end(0)     # 可省略 0
5
>>> m.span(0)    # 可省略 0
(3, 5)
```

search 方法用于查找字符串的任何位置，它也是一次匹配，只要找到了一个匹配的结果就返回，而不是查找所有匹配的结果，它的一般使用形式如下：

search(string[, pos[, endpos]])
其中，string 是待匹配的字符串，pos 和 endpos 是可选参数，指定字符串的起始和终点位置，默认值分别是 0 和 len (字符串长度)。

当匹配成功时，返回一个 Match 对象，如果没有匹配上，则返回 None。

```python
>>> import re
>>> pattern = re.compile('\d+')
>>> m = pattern.search('one12twothree34four')  # 这里如果使用 match 方法则不匹配
>>> m
<_sre.SRE_Match object at 0x10cc03ac0>
>>> m.group()
'12'
>>> m = pattern.search('one12twothree34four', 10, 30)  # 指定字符串区间
>>> m
<_sre.SRE_Match object at 0x10cc03b28>
>>> m.group()
'34'
>>> m.span()
(13, 15)
```

# Web
## Flask
### 140.对Flask蓝图(Blueprint)的理解？

蓝图的定义：

蓝图 Blueprint 是Flask应用程序组件化的方法，可以在一个应用内或跨越多个项目共用蓝图。使用蓝图可以极大简化大型应用的开发难度，也为Flask扩展提供了一种在应用中注册服务的集中式机制。

蓝图的应用场景：

把一个应用分解为一个蓝图的集合。这对大型应用是理想的。一个项目可以实例化一个应用对象，初始化几个扩展，并注册一集合的蓝图。

以URL前缀和/或子域名，在应用上注册一个蓝图。URL前缀/子域名中的参数即成为这个蓝图下的所有视图函数的共同的视图参数（默认情况下）
在一个应用中用不同的URL规则多次注册一个蓝图。

通过蓝图提供模板过滤器、静态文件、模板和其他功能。一个蓝图不一定要实现应用或视图函数。

初始化一个Flask扩展时，在这些情况中注册一个蓝图。

蓝图的缺点：

不能在应用创建后撤销注册一个蓝图而不销毁整个应用对象。

使用蓝图的三个步骤：

1.创建一个蓝图对象

```python
blue = Blueprint("blue",__name__)
```

2.在这个蓝图对象上进行操作，例如注册路由、指定静态文件夹、注册模板过滤器...

```python
@blue.route('/')
def blue_index():
    return "Welcome to my blueprint"
```

3.在应用对象上注册这个蓝图对象

```python
app.register_blueprint(blue,url_prefix="/blue")
```

### 141.Flask 和 Django 路由映射的区别？

在django中，路由是浏览器访问服务器时，先访问的项目中的url，再由项目中的url找到应用中url，这些url是放在一个列表里，遵从从前往后匹配的规则。在flask中，路由是通过装饰器给每个视图函数提供的，而且根据请求方式的不同可以一个url用于不同的作用。

## Django
### 142.什么是wsgi,uwsgi,uWSGI?

WSGI:

web服务器网关接口，是一套协议。用于接收用户请求并将请求进行初次封装，然后将请求交给web框架。

实现wsgi协议的模块：wsgiref，本质上就是编写一socket服务端，用于接收用户请求（django)

werkzeug,本质上就是编写一个socket服务端，用于接收用户请求(flask)

uwsgi：与WSGI一样是一种通信协议，它是uWSGI服务器的独占协议，用于定义传输信息的类型。

uWSGI：是一个web服务器，实现了WSGI的协议，uWSGI协议，http协议。

### 143.Django、Flask、Tornado的对比？

1、Django走的大而全的方向，开发效率高。它的MTV框架，自带的ORM，admin后台管理，自带的sqlite数据库和开发测试用的服务器，给开发者提高了超高的开发效率。

重量级web框架，功能齐全，提供一站式解决的思路，能让开发者不用在选择上花费大量时间。

自带ORM和模板引擎，支持jinja等非官方模板引擎。

自带ORM使Django和关系型数据库耦合度高，如果要使用非关系型数据库，需要使用第三方库。

自带数据库管理app

成熟，稳定，开发效率高，相对于Flask，Django的整体封闭性比较好，适合做企业级网站的开发。python web框架的先驱，第三方库丰富。

2、Flask 是轻量级的框架，自由，灵活，可扩展性强，核心基于Werkzeug WSGI工具 和jinja2 模板引擎

适用于做小网站以及web服务的API,开发大型网站无压力，但架构需要自己设计

与关系型数据库的结合不弱于Django，而与非关系型数据库的结合远远优于Django

3、Tornado走的是少而精的方向，性能优越，它最出名的异步非阻塞的设计方式

Tornado的两大核心模块：

iostraem：对非阻塞的socket进行简单的封装

ioloop：对I/O 多路复用的封装,它实现一个单例

### 144.CORS 和 CSRF的区别？

什么是CORS？

CORS是一个W3C标准，全称是“跨域资源共享"(Cross-origin resoure sharing)。
它允许浏览器向跨源服务器，发出XMLHttpRequest请求，从而客服了AJAX只能同源使用的限制。

什么是CSRF？

CSRF主流防御方式是在后端生成表单的时候生成一串随机token，内置到表单里成为一个字段，同时，将此串token置入session中。每次表单提交到后端时都会检查这两个值是否一致，以此来判断此次表单提交是否是可信的，提交过一次之后，如果这个页面没有生成CSRF token,那么token将会被清空,如果有新的需求，那么token会被更新。

攻击者可以伪造POST表单提交，但是他没有后端生成的内置于表单的token，session中没有token都无济于事。

### 145.Session,Cookie,JWT的理解

为什么要使用会话管理

众所周知，HTTP协议是一个无状态的协议，也就是说每个请求都是一个独立的请求，请求与请求之间并无关系。但在实际的应用场景，这种方式并不能满足我们的需求。举个大家都喜欢用的例子，把商品加入购物车，单独考虑这个请求，服务端并不知道这个商品是谁的，应该加入谁的购物车？因此这个请求的上下文环境实际上应该包含用户的相关信息，在每次用户发出请求时把这一小部分额外信息，也做为请求的一部分，这样服务端就可以根据上下文中的信息，针对具体的用户进行操作。所以这几种技术的出现都是对HTTP协议的一个补充，使得我们可以用HTTP协议+状态管理构建一个的面向用户的WEB应用。

Session 和 Cookie 的区别：

这里我想先谈谈session与cookies,因为这两个技术是做为开发最为常见的。那么session与cookies的区别是什么？个人认为session与cookies最核心区别在于额外信息由谁来维护。利用cookies来实现会话管理时，用户的相关信息或者其他我们想要保持在每个请求中的信息，都是放在cookies中,而cookies是由客户端来保存，每当客户端发出新请求时，就会稍带上cookies,服务端会根据其中的信息进行操作。

当利用session来进行会话管理时，客户端实际上只存了一个由服务端发送的session_id,而由这个session_id,可以在服务端还原出所需要的所有状态信息，从这里可以看出这部分信息是由服务端来维护的。

除此以外，session与cookies都有一些自己的缺点：
    
cookies的安全性不好，攻击者可以通过获取本地cookies进行欺骗或者利用cookies进行CSRF攻击。使用cookies时,在多个域名下，会存在跨域问题。

session 在一定的时间里，需要存放在服务端，因此当拥有大量用户时，也会大幅度降低服务端的性能，当有多台机器时，如何共享session也会是一个问题.(redis集群)也就是说，用户第一个访问的时候是服务器A，而第二个请求被转发给了服务器B，那服务器B如何得知其状态。实际上，session与cookies是有联系的，比如我们可以把session_id存放在cookies中的。

JWT是如何工作的

首先用户发出登录请求，服务端根据用户的登录请求进行匹配，如果匹配成功，将相关的信息放入payload中，利用算法，加上服务端的密钥生成token，这里需要注意的是secret_key很重要，如果这个泄露的话，客户端就可以随机篡改发送的额外信息，它是信息完整性的保证。生成token后服务端将其返回给客户端，客户端可以在下次请求时，将token一起交给服务端，一般是说我们可以将其放在Authorization首部中，这样也就可以避免跨域问题。

### 146.简述Django请求生命周期

一般是用户通过浏览器向我们的服务器发起一个请求(request),这个请求会去访问视图函数，如果不涉及到数据调用，那么这个时候视图函数返回一个模板也就是一个网页给用户）

视图函数调用模型去数据库查找数据，然后逐级返回，视图函数把返回的数据填充到模板中空格中，最后返回网页给用户。

1.wsgi ,请求封装后交给web框架（Flask，Django)

2.中间件，对请求进行校验或在请求对象中添加其他相关数据，例如：csrf,request.session

3.路由匹配 根据浏览器发送的不同url去匹配不同的视图函数

4.视图函数，在视图函数中进行业务逻辑的处理，可能涉及到：orm，templates 

5.中间件，对响应的数据进行处理

6.wsgi，将响应的内容发送给浏览器

### 147.用的restframework完成api发送时间时区

当前的问题是用django的rest framework模块做一个get请求的发送时间以及时区信息的api

```python
class getCurrenttime(APIView):
    def get(self,request):
        local_time = time.localtime()
        time_zone =settings.TIME_ZONE
        temp = {'localtime':local_time,'timezone':time_zone}
        return Response(temp)
```

### 148.nginx,tomcat,apache 都是什么？

Nginx（engine x)是一个高性能的HTTP和反向代理服务器，也是 一个IMAP/POP3/SMTP服务器，工作在OSI七层，负载的实现方式：轮询，IP_HASH,fair,session_sticky。

Apache HTTP Server是一个模块化的服务器，源于NCSAhttpd服务器。

Tomcat 服务器是一个免费的开放源代码的Web应用服务器，属于轻量级应用服务器，是开发和调试JSP程序的首选。

### 149.请给出你熟悉关系数据库范式有哪些，有什么作用？

在进行数据库的设计时，所遵循的一些规范，只要按照设计规范进行设计，就能设计出没有数据冗余和数据维护异常的数据库结构。

数据库的设计的规范有很多，通常来说我们在设计数据库时只要达到其中一些规范就可以了，这些规范又称之为数据库的三范式，一共有三条，也存在着其他范式，我们只要做到满足前三个范式的要求，就能设计出符合我们的数据库了，我们也不能全部来按照范式的要求来做，还要考虑实际的业务使用情况，所以有时候也需要做一些违反范式的要求。

1.数据库设计的第一范式(最基本)，基本上所有数据库的范式都是符合第一范式的，符合第一范式的表具有以下几个特点：

数据库表中的所有字段都只具有单一属性，单一属性的列是由基本的数据类型（整型，浮点型，字符型等）所构成的设计出来的表都是简单的二比表

2.数据库设计的第二范式(是在第一范式的基础上设计的)，要求一个表中只具有一个业务主键，也就是说符合第二范式的表中不能存在非主键列对只对部分主键的依赖关系

3.数据库设计的第三范式，指每一个非主属性既不部分依赖与也不传递依赖于业务主键，也就是第二范式的基础上消除了非主属性对主键的传递依赖

### 150.简述QQ登陆过程

qq登录，在我们的项目中分为了三个接口，

第一个接口是请求qq服务器返回一个qq登录的界面;

第二个接口是通过扫码或账号登陆进行验证，qq服务器返回给浏览器一个code和state,利用这个code通过本地服务器去向qq服务器获取access_token覆返回给本地服务器，凭借access_token再向qq服务器获取用户的openid(openid用户的唯一标识)

第三个接口是判断用户是否是第一次qq登录，如果不是的话直接登录返回的jwt-token给用户，对没有绑定过本网站的用户，对openid进行加密生成token进行绑定

### 151.post 和 get的区别?

1.GET是从服务器上获取数据，POST是向服务器传送数据

2.在客户端，GET方式在通过URL提交数据，数据在URL中可以看到，POST方式，数据放置在HTML——HEADER内提交

3.对于GET方式，服务器端用Request.QueryString获取变量的值，对于POST方式，服务器端用Request.Form获取提交的数据

### 152.项目中日志的作用

一、日志相关概念

1.日志是一种可以追踪某些软件运行时所发生事件的方法

2.软件开发人员可以向他们的代码中调用日志记录相关的方法来表明发生了某些事情

3.一个事件可以用一个包含可选变量数据的消息来描述

4.此外，事件也有重要性的概念，这个重要性也可以被成为严重性级别(level)

二、日志的作用

1.通过log的分析，可以方便用户了解系统或软件、应用的运行情况;

2.如果你的应用log足够丰富，可以分析以往用户的操作行为、类型喜好，地域分布或其他更多信息;

3.如果一个应用的log同时也分了多个级别，那么可以很轻易地分析得到该应用的健康状况，及时发现问题并快速定位、解决问题，补救损失。

4.简单来讲就是我们通过记录和分析日志可以了解一个系统或软件程序运行情况是否正常，也可以在应用程序出现故障时快速定位问题。不仅在开发中，在运维中日志也很重要，日志的作用也可以简单。总结为以下几点：

1.程序调试

2.了解软件程序运行情况，是否正常

3,软件程序运行故障分析与问题定位

4,如果应用的日志信息足够详细和丰富，还可以用来做用户行为分析

### 153.django中间件的使用？

Django在中间件中预置了六个方法，这六个方法的区别在于不同的阶段执行，对输入或输出进行干预，方法如下：

1.初始化：无需任何参数，服务器响应第一个请求的时候调用一次，用于确定是否启用当前中间件

```python
def __init__():
    pass
```

2.处理请求前：在每个请求上调用，返回None或HttpResponse对象。

```python
def process_request(request):
    pass
```

3.处理视图前:在每个请求上调用，返回None或HttpResponse对象。

```python
def process_view(request,view_func,view_args,view_kwargs):
    pass
```

4.处理模板响应前：在每个请求上调用，返回实现了render方法的响应对象。

```python
def process_template_response(request,response):
    pass
```

5.处理响应后：所有响应返回浏览器之前被调用，在每个请求上调用，返回HttpResponse对象。

```python
def process_response(request,response):
    pass
```

6.异常处理：当视图抛出异常时调用，在每个请求上调用，返回一个HttpResponse对象。

```python
def process_exception(request,exception):
    pass
```

### 154.谈一下你对uWSGI和nginx的理解？

1.uWSGI是一个Web服务器，它实现了WSGI协议、uwsgi、http等协议。Nginx中HttpUwsgiModule的作用是与uWSGI服务器进行交换。WSGI是一种Web服务器网关接口。它是一个Web服务器（如nginx，uWSGI等服务器）与web应用（如用Flask框架写的程序）通信的一种规范。

要注意WSGI/uwsgi/uWSGI这三个概念的区分。

WSGI是一种通信协议。

uwsgi是一种线路协议而不是通信协议，在此常用于在uWSGI服务器与其他网络服务器的数据通信。

uWSGI是实现了uwsgi和WSGI两种协议的Web服务器。

nginx 是一个开源的高性能的HTTP服务器和反向代理：

1.作为web服务器，它处理静态文件和索引文件效果非常高

2.它的设计非常注重效率，最大支持5万个并发连接，但只占用很少的内存空间

3.稳定性高，配置简洁。

4.强大的反向代理和负载均衡功能，平衡集群中各个服务器的负载压力应用

### 155.Django中哪里用到了线程？哪里用到了协程？哪里用到了进程？

1.Django中耗时的任务用一个进程或者线程来执行，比如发邮件，使用celery.

2.部署django项目是时候，配置文件中设置了进程和协程的相关配置。

### 156.有用过Django REST framework吗？

Django REST framework是一个强大而灵活的Web API工具。使用RESTframework的理由有：

Web browsable API对开发者有极大的好处

包括OAuth1a和OAuth2的认证策略

支持ORM和非ORM数据资源的序列化

全程自定义开发--如果不想使用更加强大的功能，可仅仅使用常规的function-based views额外的文档和强大的社区支持


## 爬虫
### 159.试列出至少三种目前流行的大型数据库
### 160.列举您使用过的Python网络爬虫所用到的网络数据包?
### 161.爬取数据后使用哪个数据库存储数据的，为什么？
### 162.你用过的爬虫框架或者模块有哪些？优缺点？
### 163.写爬虫是用多进程好？还是多线程好？
### 164.常见的反爬虫和应对方法？
### 165.解析网页的解析器使用最多的是哪几个?
### 166.需要登录的网页，如何解决同时限制ip，cookie,session
### 167.验证码的解决?
### 168.使用最多的数据库，对他们的理解？
### 169.编写过哪些爬虫中间件？
### 170.“极验”滑动验证码如何破解？
### 171.爬虫多久爬一次，爬下来的数据是怎么存储？
### 172.cookie过期的处理问题？
### 173.动态加载又对及时性要求很高怎么处理？
### 174.HTTPS有什么优点和缺点？
### 175.HTTPS是如何实现安全传输数据的？
### 176.TTL，MSL，RTT各是什么？
### 177.谈一谈你对Selenium和PhantomJS了解
### 178.平常怎么使用代理的 ？
### 179.存放在数据库(redis、mysql等)。
### 180.怎么监控爬虫的状态?
### 181.描述下scrapy框架运行的机制？
### 182.谈谈你对Scrapy的理解？
### 183.怎么样让 scrapy 框架发送一个 post 请求（具体写出来）
### 184.怎么监控爬虫的状态 ？
### 185.怎么判断网站是否更新？
### 186.图片、视频爬取怎么绕过防盗连接
### 187.你爬出来的数据量大概有多大？大概多长时间爬一次？
### 188.用什么数据库存爬下来的数据？部署是你做的吗？怎么部署？
### 189.增量爬取
### 190.爬取下来的数据如何去重，说一下scrapy的具体的算法依据。
### 191.Scrapy的优缺点?
### 192.怎么设置爬取深度？
### 193.scrapy和scrapy-redis有什么区别？为什么选择redis数据库？
### 194.分布式爬虫主要解决什么问题？
### 195.什么是分布式存储？
### 196.你所知道的分布式爬虫方案有哪些？
### 197.scrapy-redis，有做过其他的分布式爬虫吗？

## 测试
### 213.编写测试计划的目的是
### 214.对关键词触发模块进行测试
### 215.其他常用笔试题目网址汇总
### 216.测试人员在软件开发过程中的任务是什么
### 217.一条软件Bug记录都包含了哪些内容？
### 218.简述黑盒测试和白盒测试的优缺点
### 219.请列出你所知道的软件测试种类，至少5项
### 220.Alpha测试与Beta测试的区别是什么？
### 221.举例说明什么是Bug？一个bug report应包含什么关键字？

## 大数据
### 242.找出1G的文件中高频词
### 243.一个大约有一万行的文本文件统计高频词
### 244.怎么在海量数据中找出重复次数最多的一个？
### 245.判断数据是否在大量数据中

## 架构

### [Python后端架构演进](<https://zhu327.github.io/2018/07/19/python%E5%90%8E%E7%AB%AF%E6%9E%B6%E6%9E%84%E6%BC%94%E8%BF%9B/>)

这篇文章几乎涵盖了python会用的架构，在面试可以手画架构图，根据自己的项目谈下技术选型和优劣，遇到的坑等。绝对加分广而告之，欢迎关注我的微信公众号 AlwaysBeta，里面有各种后端开发干货，面试真题，助你轻轻松松进大厂。

![](http://ww1.sinaimg.cn/large/0061a0TTgy1gaqr087j9xj3076076wex.jpg)

想学习 Go 的同学可以到这里看看：https://github.com/yongxinz/gopher

Go 学习路线图，包括基础专栏，进阶专栏，源码阅读，实战开发，面试刷题，必读书单等一系列资源。

### 

# 后端面试题汇总

包括 Python、Go、Redis、MySQL、PostgreSQL、Kafka、数据结构、算法、编程、网络等相关内容，欢迎关注。

## Python

- [Python 常考题](https://github.com/yongxinz/backend-interview/tree/master/Python)

## Go
- [交替打印数字和字母](Go/q001.md)
- [判断字符串中字符是否全都不同](Go/q002.md)
- [翻转字符串](Go/q003.md)
- [判断两个给定的字符串排序后是否一致](Go/q004.md)
- [字符串替换问题](Go/q005.md)
- [机器人坐标计算](Go/q006.md)
- [语法题目一](Go/q007.md)
- [语法题目二](Go/q008.md)
- [goroutine和channel使用一](Go/q009.md)
- [实现阻塞读的并发安全Map](Go/q010.md)
- [高并发下的锁与map读写问题](Go/q011.md)
- [定时与 panic 恢复](Go/q012.md)
- [为 sync.WaitGroup 中Wait函数支持 WaitTimeout 功能.](Go/q013.md)
- [七道语法找错题目](Go/q014.md)
- [golang 并发题目测试](Go/q015.md)
- [记一道字节跳动的算法面试题](Go/q016.md)
- [多协程查询切片问题](Go/q017.md)
- [对已经关闭的的chan进行读写，会怎么样？为什么？](Go/q018.md)
- [简单聊聊内存逃逸？](Go/q019.md)
- [字符串转成byte数组，会发生内存拷贝吗？](Go/q020.md)
- [http包的内存泄漏](Go/q021.md)
- [sync.Map 的用法](Go/q022.md)
- [Go语言的GPM调度器是什么？](Go/go-gpm.md)
- [Goroutine调度策略](Go/go-scheduler.md)
- [goroutine调度器概述](Go/go-scheduler-base.md)

## 数据库
### MySQL

- [MySQL 常考题](https://github.com/yongxinz/backend-interview/tree/master/MySQL)
- [MySQL数据库经典面试题解析](MySQL/mysql-interview.md)
- [MySQL InnoDB MVCC 机制的原理及实现](MySQL/mysql-mvcc.md)
- [为什么MySQL使用B+树做索引？](MySQL/mysql-index-b-plus.md)
- [20 道 MySQL 面试题](https://mp.weixin.qq.com/s/KVnMi45dvuLaRjoxcmpbNw)
- [看一遍就理解：order by 详解](https://mp.weixin.qq.com/s/h9jWeoyiBGnQLvDrtXqVWw)

### Redis

- [Redis 常考题](https://github.com/yongxinz/backend-interview/tree/master/Redis)
- [Redis 基础数据结构](Redis/redis.md)
- [Redis中的底层数据结构](Redis/redis-data-structure.md)
- [Redis持久化的原理及优化](Redis/redis-rdb.md)
- [Redis中内存淘汰算法实现](Redis/redis-policy.md)
- [Redis主从复制原理](Redis/redis-master-slave.md)
- [Redis 夺命连环 20 问](https://mp.weixin.qq.com/s/PUSpuyh6dOi2zWM6J0-EJA)
- [Redis的事务满足原子性吗？](https://mp.weixin.qq.com/s/KAdivX9aYK2NgUJsDeKCpA)
- [缓存和数据库一致性问题](https://mp.weixin.qq.com/s/4W7vmICGx6a_WX701zxgPQ)

### MongoDB

- [MongoDB 常考题](https://github.com/yongxinz/backend-interview/tree/master/MongoDB)

## 中间件

- [中间件常考题](中间件/README.md)

## 项目与架构

- [项目与架构常考题](项目与架构/README.md)

## 数据结构与算法

- [数据结构与算法常考题](https://github.com/yongxinz/backend-interview/tree/master/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%8E%E7%AE%97%E6%B3%95)

## 系统编程

- [系统编程常考题](https://github.com/yongxinz/backend-interview/tree/master/%E7%B3%BB%E7%BB%9F%E7%BC%96%E7%A8%8B)

## 网络

- [网络常考题](https://github.com/yongxinz/backend-interview/tree/master/%E7%BD%91%E7%BB%9C)

## 编程题

- [编程题常考题](https://github.com/yongxinz/backend-interview/tree/master/%E7%BC%96%E7%A8%8B%E9%A2%98)

### 1、Redis 是什么？

1. 是一个完全开源免费的 key-value 内存数据库 
2. 通常被认为是一个数据结构服务器，主要是因为其有着丰富的数据结构 strings、hash、list、sets、sorted sets

通常局限点来说，Redis也以消息队列的形式存在，作为内嵌的List存在，满足实时的高并发需求。在使用缓存的时候，redis比memcached具有更多的优势，并且支持更多的数据类型，把redis当作一个中间存储系统，用来处理高并发的数据库操作。

- 速度快：使用标准C写，所有数据都在内存中完成，读写速度分别达到10万/20万 
- 持久化：对数据的更新采用Copy-on-write技术，可以异步地保存到磁盘上，主要有两种策略，一是根据时间，更新次数的快照（save 300 10 ）二是基于语句追加方式(Append-only file，aof) 
- 自动操作：对不同数据类型的操作都是自动的，很安全 
- 快速的主--从复制，官方提供了一个数据，Slave在21秒即完成了对Amazon网站10G key set的复制。 
- Sharding技术：很容易将数据分布到多个Redis实例中，数据库的扩展是个永恒的话题，在关系型数据库中，主要是以添加硬件、以分区为主要技术形式的纵向扩展解决了很多的应用场景，但随着web2.0、移动互联网、云计算等应用的兴起，这种扩展模式已经不太适合了，所以近年来，像采用主从配置、数据库复制形式的，Sharding这种技术把负载分布到多个特定节点上去的横向扩展方式用处越来越多。

### 2、Redis 缺点

- 是数据库容量受到物理内存的限制,不能用作海量数据的高性能读写,因此Redis适合的场景主要局限在较小数据量的高性能操作和运算上。
- Redis较难支持在线扩容，在集群容量达到上限时在线扩容会变得很复杂。为避免这一问题，运维人员在系统上线时必须确保有足够的空间，这对资源造成了很大的浪费。
  
### 3、Redis宕机怎么解决？

宕机:服务器停止服务

如果只有一台redis，肯定会造成数据丢失，无法挽救

多台redis或者是redis集群，宕机则需要分为在主从模式下区分来看：

slave从redis宕机，配置主从复制的时候才配置从的redis，从的会从主的redis中读取主的redis的操作日志，在redis中从库重新启动后会自动加入到主从架构中，自动完成同步数据;

如果从数据库实现了持久化，此时千万不要立马重启服务，否则可能会造成数据丢失，正确的操作如下：在slave数据上执行SLAVEOF ON ONE,来断开主从关系并把slave升级为主库，此时重新启动主数据库，执行SLAVEOF，把它设置为从库，连接到主的redis上面做主从复制，自动备份数据。

以上过程很容易配置错误，可以使用redis提供的哨兵机制来简化上面的操作。简单的方法:redis的哨兵(sentinel)的功能

### 4、redis和mecached的区别，以及使用场景

区别

1、redis和Memcache都是将数据存放在内存中，都是内存数据库。不过memcache还可以用于缓存其他东西，例如图片，视频等等

2、Redis不仅仅支持简单的k/v类型的数据，同时还提供list,set,hash等数据结构的存储

3、虚拟内存-redis当物理内存用完时，可以将一些很久没用的value交换到磁盘

4、过期策略-memcache在set时就指定，例如set key1 0 0 8，即永不过期。Redis可以通过例如expire设定，例如expire name 10

5、分布式-设定memcache集群，利用magent做一主多从，redis可以做一主多从。都可以一主一丛

6、存储数据安全-memcache挂掉后，数据没了，redis可以定期保存到磁盘(持久化)

7、灾难恢复-memcache挂掉后，数据不可恢复，redis数据丢失后可以通过aof恢复

8、Redis支持数据的备份，即master-slave模式的数据备份

9、应用场景不一样，redis除了作为NoSQL数据库使用外，还能用做消息队列，数据堆栈和数据缓存等;Memcache适合于缓存SQL语句，数据集，用户临时性数据，延迟查询数据和session等

使用场景

1、如果有持久方面的需求或对数据类型和处理有要求的应该选择redis

2、如果简单的key/value存储应该选择memcached.

### 5、Redis集群方案该怎么做?都有哪些方案?

1、redis 目前用的最多的集群方案，基本和twemproxy一致的效果，但它支持在节点数量改变情况下，旧节点数据客恢复到新hash节点

2、redis cluster3.0自带的集群，特点在于他的分布式算法不是一致性hash，而是hash槽的概念，以及自身支持节点设置从节点。具体看官方介绍

3、在业务代码层实现，起几个毫无关联的redis实例，在代码层，对key进行hash计算，然后去对应的redis实例操作数据。这种方式对hash层代码要求比较高，考虑部分包括，节点失效后的替代算法方案，数据震荡后的字典脚本恢复，实例的监控，等等

### 6、Redis回收进程是如何工作的

一个客户端运行了新的命令，添加了新的数据。

redis检查内存使用情况，如果大于maxmemory的限制，则根据设定好的策略进行回收。

一个新的命令被执行等等，所以我们不断地穿越内存限制的边界，通过不断达到边界然后不断回收回到边界以下。

如果一个命令的结果导致大量内存被使用(例如很大的集合的交集保存到一个新的键)，不用多久内存限制就会被这个内存使用量超越。

### 7、Redis 跟 MySQL 缓存一致性

https://zhuanlan.zhihu.com/p/59167071

### 8、Redis 的几个基本数据类型，底层实现

https://zhuanlan.zhihu.com/p/344918922

### 9、Redis 为什么那么快

- https://zhuanlan.zhihu.com/p/160157573
- https://xie.infoq.cn/article/b3816e9fe3ac77684b4f29348

### 10、Redis 中常见集群部署情况，出现性能问题如何排查。

https://mp.weixin.qq.com/s/q79ji-cgfUMo7H0p254QRg

### 11、Redis 中的事务。

https://mp.weixin.qq.com/s/Hevg_4YJT_PzVd1Z_yE1TQ

### 12、缓存雪崩、击穿、穿透

https://mp.weixin.qq.com/s/_StOUX9Nu-Bo8UpX7ThZmg

### 13、Redis 的持久化

https://mp.weixin.qq.com/s/yP2HH8840OMY4e7tKMymiA

### 14、Redis不是号称单线程也有很高的性能么？为啥还需要多线程？

https://mp.weixin.qq.com/s/SYUYvKCxsyMbdBsRrJOZqA

### 15、Redis 过期策略和内存淘汰机制

https://segmentfault.com/a/1190000023060522

### 16、Redis 分布式锁怎么用？有什么问题？

https://mp.weixin.qq.com/s/IoDPieqgY995cyyWAQrQew

### 17、Redis为什么变慢了？一文讲透如何排查Redis性能问题

https://mp.weixin.qq.com/s/Qc4t_-_pL4w8VlSoJhRDcg
# Redis中的数据结构

原文地址  [Redis中的数据结构](https://www.cnblogs.com/neooelric/p/9621736.html)

## 1. 底层数据结构, 与Redis Value Type之间的关系

对于Redis的使用者来说, Redis作为Key-Value型的内存数据库, 其Value有多种类型.

- String
- Hash
- List
- Set
- ZSet

这些Value的类型, 只是"Redis的用户认为的, Value存储数据的方式". 而在具体实现上, 各个Type的Value到底如何存储, 这对于Redis的使用者来说是不公开的.

举个粟子: 使用下面的命令创建一个Key-Value

```bash
SET "Hello" "World"
```

对于Redis的使用者来说, `Hello`这个Key, 对应的Value是String类型, 其值为五个ASCII字符组成的二进制数据. 但具体在底层实现上, 这五个字节是如何存储的, 是不对用户公开的. 即, Value的Type, 只是表象, 具体数据在内存中以何种数据结构存放, 这对于用户来说是不必要了解的.

Redis对使用者暴露了五种`Value Type`, 其底层实现的数据结构有8种, 分别是:

- SDS - simple synamic string - 支持自动动态扩容的字节数组
- list - 平平无奇的链表
- dict - 使用双哈希表实现的, 支持平滑扩容的字典
- zskiplist - 附加了后向指针的跳跃表
- intset - 用于存储整数数值集合的自有结构
- ziplist - 一种实现上类似于TLV, 但比TLV复杂的, 用于存储任意数据的有序序列的数据结构
- quicklist - 一种以ziplist作为结点的双链表结构, 实现的非常苟
- zipmap - 一种用于在小规模场合使用的轻量级字典结构

而衔接"底层数据结构"与"Value Type"的桥梁的, 则是Redis实现的另外一种数据结构: `redisObject`. Redis中的Key与Value在表层都是一个`redisObject`实例, 故该结构有所谓的"类型", 即是`ValueType`. 对于每一种`Value Type`类型的`redisObject`, 其底层至少支持两种不同的底层数据结构来实现. 以应对在不同的应用场景中, Redis的运行效率, 或内存占用.


## 2. 底层数据结构

### 2.1 SDS - simple dynamic string

这是一种用于存储二进制数据的一种结构, 具有动态扩容的特点. 其实现位于src/sds.h与src/sds.c中, 其关键定义如下:

```cgo
typedef char *sds;

/* Note: sdshdr5 is never used, we just access the flags byte directly.
 * However is here to document the layout of type 5 SDS strings. */
struct __attribute__ ((__packed__)) sdshdr5 {
    unsigned char flags; /* 3 lsb of type, and 5 msb of string length */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr8 {
    uint8_t len; /* used */
    uint8_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr16 {
    uint16_t len; /* used */
    uint16_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr32 {
    uint32_t len; /* used */
    uint32_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr64 {
    uint64_t len; /* used */
    uint64_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
```

SDS的总体概览如下图:

![](../images/668722-20180910183925685-224282854.png)

其中sdshdr是头部, buf是真实存储用户数据的地方. 另外注意, 从命名上能看出来, 这个数据结构除了能存储二进制数据, 显然是用于设计作为字符串使用的, 所以在buf中, 用户数据后总跟着一个\0. 即图中 "数据" + "\0" 是为所谓的buf

SDS有五种不同的头部. 其中sdshdr5实际并未使用到. 所以实际上有四种不同的头部, 分别如下:

![](../images/668722-20180910183940043-66166526.png)


- len分别以uint8, uint16, uint32, uint64表示用户数据的长度(不包括末尾的\0)
- alloc分别以uint8, uint16, uint32, uint64表示整个SDS, 除过头部与末尾的\0, 剩余的字节数.
- flag始终为一字节, 以低三位标示着头部的类型, 高5位未使用.

当在程序中持有一个SDS实例时, 直接持有的是数据区的头指针, 这样做的用意是: 通过这个指针, 向前偏一个字节, 就能取到flag, 通过判断flag低三位的值, 能迅速判断: 头部的类型, 已用字节数, 总字节数, 剩余字节数. 这也是为什么sds类型即是char *指针类型别名的原因.

创建一个SDS实例有三个接口, 分别是:

```cgo
// 创建一个不含数据的sds: 
//  头部    3字节 sdshdr8
//  数据区  0字节
//  末尾    \0 占一字节
sds sdsempty(void);
// 带数据创建一个sds:
//  头部    按initlen的值, 选择最小的头部类型
//  数据区  从入参指针init处开始, 拷贝initlen个字节
//  末尾    \0 占一字节
sds sdsnewlen(const void *init, size_t initlen);
// 带数据创建一个sds:
//  头部    按strlen(init)的值, 选择最小的头部类型
//  数据区  入参指向的字符串中的所有字符, 不包括末尾 \0
//  末尾    \0 占一字节
sds sdsnew(const char *init);
```

- 所有创建sds实例的接口, 都不会额外分配预留内存空间
- `sdsnewlen`用于带二进制数据创建sds实例, sdsnew用于带字符串创建sds实例. 接口返回的sds可以直接传入libc中的字符串输出函数中进行操作, 由于无论其中存储的是用户的二进制数据, 还是字符串, 其末尾都带一个\0, 所以至少调用libc中的字符串输出函数是安全的.

在对SDS中的数据进行修改时, 若剩余空间不足, 会调用sdsMakeRoomFor函数用于扩容空间, 这是一个很低级的API, 通常情况下不应当由SDS的使用者直接调用. 其实现中核心的几行如下:

```cgo
sds sdsMakeRoomFor(sds s, size_t addlen) {
    ...
    /* Return ASAP if there is enough space left. */
    if (avail >= addlen) return s;

    len = sdslen(s);
    sh = (char*)s-sdsHdrSize(oldtype);
    newlen = (len+addlen);
    if (newlen < SDS_MAX_PREALLOC)
        newlen *= 2;
    else
        newlen += SDS_MAX_PREALLOC;
    ...
}
```

可以看到, 在扩充空间时

- 先保证至少有addlen可用
- 然后再进一步扩充, 在总体占用空间不超过阈值`SDS_MAC_PREALLOC`时, 申请空间再翻一倍. 若总体空间已经超过了阈值, 则步进增长`SDS_MAC_PREALLOC`. 这个阈值的默认值为 `1024 * 1024`

SDS也提供了接口用于移除所有未使用的内存空间. `sdsRemoveFreeSpace`, 该接口没有间接的被任何SDS其它接口调用, 即默认情况下, SDS不会自动回收预留空间. 在SDS的使用者需要节省内存时, 由使用者自行调用:

```cgo
sds sdsRemoveFreeSpace(sds s);
```

总结:

- SDS除了是某些Value Type的底层实现, 也被大量使用在Redis内部, 用于替代C-Style字符串. 所以默认的创建SDS实例接口, 不分配额外的预留空间. 因为多数字符串在程序运行期间是不变的. 而对于变更数据区的API, 其内部则是调用了 sdsMakeRoomFor, 每一次扩充空间, 都会预留大量的空间. 这样做的考量是: 如果一个SDS实例中的数据被变更了, 那么很有可能会在后续发生多次变更.
- SDS的API内部不负责清除未使用的闲置内存空间, 因为内部API无法判断这样做的合适时机. 即便是在操作数据区的时候导致数据区占用内存减少时, 内部API也不会清除闲置内在空间. 清除闲置内存空间责任应当由SDS的使用者自行担当.
- 用SDS替代C-Style字符串时, 由于其头部额外存储了数据区的长度信息, 所以字符串的求长操作时间复杂度为O(1)


### 2.2 list

这是普通的链表实现, 链表结点不直接持有数据, 而是通过void *指针来间接的指向数据. 其实现位于 src/adlist.h与src/adlist.c中, 关键定义如下:

```cgo
typedef struct listNode {
    struct listNode *prev;
    struct listNode *next;
    void *value;
} listNode;

typedef struct listIter {
    listNode *next;
    int direction;
} listIter;

typedef struct list {
    listNode *head;
    listNode *tail;
    void *(*dup)(void *ptr);
    void (*free)(void *ptr);
    int (*match)(void *ptr, void *key);
    unsigned long len;
} list;
```

其内存布局如下图所示:

![](../images/668722-20180910184001095-98860783.png)

这是一个平平无奇的链表的实现. list在Redis除了作为一些Value Type的底层实现外, 还广泛用于Redis的其它功能实现中, 作为一种数据结构工具使用. 在list的实现中, 除了基本的链表定义外, 还额外增加了:

- 迭代器`listIter`的定义, 与相关接口的实现.
- 由于list中的链表结点本身并不直接持有数据, 而是通过value字段, 以void *指针的形式间接持有, 所以数据的生命周期并不完全与链表及其结点一致. 这给了list的使用者相当大的灵活性. 比如可以多个结点持有同一份数据的地址. 但与此同时, 在对链表进行销毁, 结点复制以及查找匹配时, 就需要list的使用者将相关的函数指针赋值于list.dup, list.free, list.match字段.

### 2.3 dict

dict是Redis底层数据结构中实现最为复杂的一个数据结构, 其功能类似于C++标准库中的std::unordered_map, 其实现位于 src/dict.h 与 src/dict.c中, 其关键定义如下:

```cgo
typedef struct dictEntry {
    void *key;
    union {
        void *val;
        uint64_t u64;
        int64_t s64;
        double d;
    } v;
    struct dictEntry *next;
} dictEntry;

typedef struct dictType {
    uint64_t (*hashFunction)(const void *key);
    void *(*keyDup)(void *privdata, const void *key);
    void *(*valDup)(void *privdata, const void *obj);
    int (*keyCompare)(void *privdata, const void *key1, const void *key2);
    void (*keyDestructor)(void *privdata, void *key);
    void (*valDestructor)(void *privdata, void *obj);
} dictType;

/* This is our hash table structure. Every dictionary has two of this as we
 * implement incremental rehashing, for the old to the new table. */
typedef struct dictht {
    dictEntry **table;
    unsigned long size;
    unsigned long sizemask;
    unsigned long used;
} dictht;

typedef struct dict {
    dictType *type;
    void *privdata;
    dictht ht[2];
    long rehashidx; /* rehashing not in progress if rehashidx == -1 */
    unsigned long iterators; /* number of iterators currently running */
} dict;

/* If safe is set to 1 this is a safe iterator, that means, you can call
 * dictAdd, dictFind, and other functions against the dictionary even while
 * iterating. Otherwise it is a non safe iterator, and only dictNext()
 * should be called while iterating. */
typedef struct dictIterator {
    dict *d;
    long index;
    int table, safe;
    dictEntry *entry, *nextEntry;
    /* unsafe iterator fingerprint for misuse detection. */
    long long fingerprint;
} dictIterator;
```

其内存布局如下所示:

![](../images/668722-20180910184019522-1324296109.png)

- dict中存储的键值对, 是通过dictEntry这个结构间接持有的, k通过指针间接持有键, v通过指针间接持有值. 注意, 若值是整数值的话, 是直接存储在v字段中的, 而不是间接持有. 同时next指针用于指向, 在bucket索引值冲突时, 以链式方式解决冲突, 指向同索引的下一个dictEntry结构.
- 传统的哈希表实现, 是一块连续空间的顺序表, 表中元素即是结点. 在dictht.table中, 结点本身是散布在内存中的, 顺序表中存储的是dictEntry的指针
- 哈希表即是dictht结构, 其通过table字段间接的持有顺序表形式的bucket, bucket的容量存储在size字段中, 为了加速将散列值转化为bucket中的数组索引, 引入了sizemask字段, 计算指定键在哈希表中的索引时, 执行的操作类似于dict->type->hashFunction(键) & dict->ht[x].sizemask. 从这里也可以看出来, bucket的容量适宜于为2的幂次, 这样计算出的索引值能覆盖到所有bucket索引位.
- dict即为字典. 其中type字段中存储的是本字典使用到的各种函数指针, 包括散列函数, 键与值的复制函数, 释放函数, 以及键的比较函数. privdata是用于存储用户自定义数据. 这样, 字典的使用者可以最大化的自定义字典的实现, 通过自定义各种函数实现, 以及可以附带私有数据, 保证了字典有很大的调优空间.
- 字典为了支持平滑扩容, 定义了ht[2]这个数组字段. 其用意是这样的:
    1. 一般情况下, 字典dict仅持有一个哈希表dictht的实例, 即整个字典由一个bucket实现.
    2. 随着插入操作, bucket中出现冲突的概率会越来越大, 当字典中存储的结点数目, 与bucket数组长度的比值达到一个阈值(1:1)时, 字典为了缓解性能下降, 就需要扩容
    3. 扩容的操作是平滑的, 即在扩容时, 字典会持有两个dictht的实例, ht[0]指向旧哈希表, ht[1]指向扩容后的新哈希表. 平滑扩容的重点在于两个策略:
    4. 后续每一次的插入, 替换, 查找操作, 都插入到ht[1]指向的哈希表中
    5. 每一次插入, 替换, 查找操作执行时, 会将旧表ht[0]中的一个bucket索引位持有的结点链表, 迁移到ht[1]中去. 迁移的进度保存在rehashidx这个字段中.在旧表中由于冲突而被链接在同一索引位上的结点, 迁移到新表后, 可能会散布在多个新表索引中去.
    6. 当迁移完成后, ht[0]指向的旧表会被释放, 之后会将新表的持有权转交给ht[0], 再重置ht[1]指向NULL
- 这种平滑扩容的优点有两个:

    1. 平滑扩容过程中, 所有结点的实际数据, 即dict->ht[0]->table[rehashindex]->k与dict->ht[0]->table[rehashindex]->v分别指向的实际数据, 内存地址都不会变化. 没有发生键数据与值数据的拷贝或移动, 扩容整个过程仅是各种指针的操作. 速度非常快
    2. 扩容操作是步进式的, 这保证任何一次插入操作都是顺畅的, dict的使用者是无感知的. 若扩容是一次性的, 当新旧bucket容量特别大时, 迁移所有结点必然会导致耗时陡增.

除了字典本身的实现外, 其中还顺带实现了一个迭代器, 这个迭代器中有字段safe以标示该迭代器是"安全迭代器"还是"非安全迭代器", 所谓的安全与否, 指是的这种场景:
设想在运行迭代器的过程中, 字典正处于平滑扩容的过程中. 在平滑扩容的过程中时, 旧表一个索引位上的, 由冲突而链起来的多个结点, 迁移到新表后, 可能会散布到新表的多个索引位上. 且新的索引位的值可能比旧的索引位要低.

遍历操作的重点是, 保证在迭代器遍历操作开始时, 字典中持有的所有结点, 都会被遍历到. 而若在遍历过程中, 一个未遍历的结点, 从旧表迁移到新表后, 索引值减小了, 那么就可能会导致这个结点在遍历过程中被遗漏.

所以, 所谓的"安全"迭代器, 其在内部实现时: 在迭代过程中, 若字典正处于平滑扩容过程, 则暂停结点迁移, 直至迭代器运行结束. 这样虽然不能保证在迭代过程中插入的结点会被遍历到, 但至少保证在迭代起始时, 字典中持有的所有结点都会被遍历到.

这也是为什么dict结构中有一个iterators字段的原因: 该字段记录了运行于该字典上的安全迭代器的数目. 若该数目不为0, 字典是不会继续进行结点迁移平滑扩容的.

下面是字典的扩容操作中的核心代码, 我们以插入操作引起的扩容为例:

**先是插入操作的外部逻辑:**

1. 如果插入时, 字典正处于平滑扩容过程中, 那么无论本次插入是否成功, 先迁移一个bucket索引中的结点至新表
2. 在计算新插入结点键的bucket索引值时, 内部会探测哈希表是否需要扩容(若当前不在平滑扩容过程中)

```cgo
int dictAdd(dict *d, void *key, void *val)
{
    dictEntry *entry = dictAddRaw(d,key,NULL);          // 调用dictAddRaw

    if (!entry) return DICT_ERR;
    dictSetVal(d, entry, val);
    return DICT_OK;
}

dictEntry *dictAddRaw(dict *d, void *key, dictEntry **existing)
{
    long index;
    dictEntry *entry;
    dictht *ht;

    if (dictIsRehashing(d)) _dictRehashStep(d); // 若在平滑扩容过程中, 先步进迁移一个bucket索引

    /* Get the index of the new element, or -1 if
     * the element already exists. */

    // 在计算键在bucket中的索引值时, 内部会检查是否需要扩容
    if ((index = _dictKeyIndex(d, key, dictHashKey(d,key), existing)) == -1)
        return NULL;

    /* Allocate the memory and store the new entry.
     * Insert the element in top, with the assumption that in a database
     * system it is more likely that recently added entries are accessed
     * more frequently. */
    ht = dictIsRehashing(d) ? &d->ht[1] : &d->ht[0];
    entry = zmalloc(sizeof(*entry));
    entry->next = ht->table[index];
    ht->table[index] = entry;
    ht->used++;

    /* Set the hash entry fields. */
    dictSetKey(d, entry, key);
    return entry;
}

```

下面是计算bucket索引值的函数, 内部会探测该哈希表是否需要扩容, 如果需要扩容(结点数目与bucket数组长度比例达到1:1), 就使字典进入平滑扩容过程:

```cgo
static long _dictKeyIndex(dict *d, const void *key, uint64_t hash, dictEntry **existing)
{
    unsigned long idx, table;
    dictEntry *he;
    if (existing) *existing = NULL;

    /* Expand the hash table if needed */
    if (_dictExpandIfNeeded(d) == DICT_ERR) // 探测是否需要扩容, 如果需要, 则开始扩容
        return -1;
    for (table = 0; table <= 1; table++) {
        idx = hash & d->ht[table].sizemask;
        /* Search if this slot does not already contain the given key */
        he = d->ht[table].table[idx];
        while(he) {
            if (key==he->key || dictCompareKeys(d, key, he->key)) {
                if (existing) *existing = he;
                return -1;
            }
            he = he->next;
        }
        if (!dictIsRehashing(d)) break;
    }
    return idx;
}

/* Expand the hash table if needed */
static int _dictExpandIfNeeded(dict *d)
{
    /* Incremental rehashing already in progress. Return. */
    if (dictIsRehashing(d)) return DICT_OK; // 如果正在扩容过程中, 则什么也不做

    /* If the hash table is empty expand it to the initial size. */
    // 若字典中本无元素, 则初始化字典, 初始化时的bucket数组长度为4
    if (d->ht[0].size == 0) return dictExpand(d, DICT_HT_INITIAL_SIZE);

    /* If we reached the 1:1 ratio, and we are allowed to resize the hash
     * table (global setting) or we should avoid it but the ratio between
     * elements/buckets is over the "safe" threshold, we resize doubling
     * the number of buckets. */
    // 若字典中元素的个数与bucket数组长度比值大于1:1时, 则调用dictExpand进入平滑扩容状态
    if (d->ht[0].used >= d->ht[0].size &&
        (dict_can_resize ||
         d->ht[0].used/d->ht[0].size > dict_force_resize_ratio))
    {
        return dictExpand(d, d->ht[0].used*2);
    }
    return DICT_OK;
}

int dictExpand(dict *d, unsigned long size)
{
    dictht n; /* the new hash table */  // 新建一个dictht结构
    unsigned long realsize = _dictNextPower(size);  

    /* the size is invalid if it is smaller than the number of
     * elements already inside the hash table */
    if (dictIsRehashing(d) || d->ht[0].used > size)
        return DICT_ERR;

    /* Rehashing to the same table size is not useful. */
    if (realsize == d->ht[0].size) return DICT_ERR;

    /* Allocate the new hash table and initialize all pointers to NULL */
    n.size = realsize;
    n.sizemask = realsize-1;
    n.table = zcalloc(realsize*sizeof(dictEntry*));// 初始化dictht下的table, 即bucket数组
    n.used = 0;

    /* Is this the first initialization? If so it's not really a rehashing
     * we just set the first hash table so that it can accept keys. */
    // 若是新字典初始化, 直接把dictht结构挂在ht[0]中
    if (d->ht[0].table == NULL) {
        d->ht[0] = n;
        return DICT_OK;
    }

    // 否则, 把新dictht结构挂在ht[1]中, 并开启平滑扩容(置rehashidx为0, 字典处于非扩容状态时, 该字段值为-1)
    /* Prepare a second hash table for incremental rehashing */
    d->ht[1] = n;
    d->rehashidx = 0;
    return DICT_OK;
}
```

下面是平滑扩容的实现:

```cgo
static void _dictRehashStep(dict *d) {
    // 若字典上还运行着安全迭代器, 则不迁移结点
    // 否则每次迁移一个旧bucket索引上的所有结点
    if (d->iterators == 0) dictRehash(d,1); 
}

int dictRehash(dict *d, int n) {
    int empty_visits = n*10; /* Max number of empty buckets to visit. */
    if (!dictIsRehashing(d)) return 0;

    while(n-- && d->ht[0].used != 0) {
        dictEntry *de, *nextde;

        /* Note that rehashidx can't overflow as we are sure there are more
         * elements because ht[0].used != 0 */
        assert(d->ht[0].size > (unsigned long)d->rehashidx);
        // 在旧bucket中, 找到下一个非空的索引位
        while(d->ht[0].table[d->rehashidx] == NULL) {
            d->rehashidx++;
            if (--empty_visits == 0) return 1;
        }
        // 取出该索引位上的结点链表
        de = d->ht[0].table[d->rehashidx];
        /* Move all the keys in this bucket from the old to the new hash HT */
        // 把所有结点迁移到新bucket中去
        while(de) {
            uint64_t h;

            nextde = de->next;
            /* Get the index in the new hash table */
            h = dictHashKey(d, de->key) & d->ht[1].sizemask;
            de->next = d->ht[1].table[h];
            d->ht[1].table[h] = de;
            d->ht[0].used--;
            d->ht[1].used++;
            de = nextde;
        }
        d->ht[0].table[d->rehashidx] = NULL;
        d->rehashidx++;
    }

    /* Check if we already rehashed the whole table... */
    // 检查是否旧表中的所有结点都被迁移到了新表
    // 如果是, 则置先释放原旧bucket数组, 再置ht[1]为ht[0]
    // 最后再置rehashidx=-1, 以示字典不处于平滑扩容状态
    if (d->ht[0].used == 0) {
        zfree(d->ht[0].table);
        d->ht[0] = d->ht[1];
        _dictReset(&d->ht[1]);
        d->rehashidx = -1;
        return 0;
    }

    /* More to rehash... */
    return 1;
}
```

**总结:**

字典的实现很复杂, 主要是实现了平滑扩容逻辑
用户数据均是以指针形式间接由dictEntry结构持有, 故在平滑扩容过程中, 不涉及用户数据的拷贝
有安全迭代器可用, 安全迭代器保证, 在迭代起始时, 字典中的所有结点, 都会被迭代到, 即使在迭代过程中对字典有插入操作
字典内部使用的默认散列函数其实也非常有讲究, 不过限于篇幅, 这里不展开讲. 并且字典的实现给了使用者非常大的灵活性(dictType结构与dict.privdata字段), 对于一些特定场合使用的键数据, 用户可以自行选择更高效更特定化的散列函数

### 2.4 zskiplist

zskiplist是Redis实现的一种特殊的跳跃表. 跳跃表是一种基于线性表实现简单的搜索结构, 其最大的特点就是: 实现简单, 性能能逼近各种搜索树结构. 血统纯正的跳跃表的介绍在维基百科中即可查阅. 在Redis中, 在原版跳跃表的基础上, 进行了一些小改动, 即是现在要介绍的zskiplist结构.

其定义在src/server.h中, 如下:

```cgo
/* ZSETs use a specialized version of Skiplists */
typedef struct zskiplistNode {
    sds ele;
    double score;
    struct zskiplistNode *backward;
    struct zskiplistLevel {
        struct zskiplistNode *forward;
        unsigned int span;
    } level[];
} zskiplistNode;

typedef struct zskiplist {
    struct zskiplistNode *header, *tail;
    unsigned long length;
    int level;
} zskiplist;
```

其内存布局如下图:

![](../images/668722-20180910184047816-693646977.png)

zskiplist的核心设计要点为:

1. 头结点不持有任何数据, 且其level[]的长度为32
2. 每个结点, 除了持有数据的ele字段, 还有一个字段score, 其标示着结点的得分, 结点之间凭借得分来判断先后顺序, 跳跃表中的结点按结点的得分升序排列.
3. 每个结点持有一个backward指针, 这是原版跳跃表中所没有的. 该指针指向结点的前一个紧邻结点.
4. 每个结点中最多持有32个zskiplistLevel结构. 实际数量在结点创建时, 按幂次定律随机生成(不超过32). 每个zskiplistLevel中有两个字段.
5. forward字段指向比自己得分高的某个结点(不一定是紧邻的), 并且, 若当前zskiplistLevel实例在level[]中的索引为X, 则其forward字段指向的结点, 其level[]字段的容量至少是X+1. 这也是上图中, 为什么forward指针总是画的水平的原因.
6. span字段代表forward字段指向的结点, 距离当前结点的距离. 紧邻的两个结点之间的距离定义为1.
7. zskiplist中持有字段level, 用以记录所有结点(除过头结点外), level[]数组最长的长度.

跳跃表主要用于, 在给定一个分值的情况下, 查找与该分值最接近的结点. 搜索时, 伪代码如下:

```cgo
int level = zskiplist->level - 1;
zskiplistNode p = zskiplist->head;

while(1 && p)
{
    zskiplistNode q = (p->level)[level]->forward:
    if(q->score > 分值)
    {
        if(level > 0)
        {
            level--;
        }
        else
        {
            return :
                q为整个跳跃表中, 分值大于指定分值的第一个结点
                q->backward为整个跳跃表中, 分值小于或等于指定分值的最后一个结点
        }
    }
    else
    {
        p = q;
    }
}
```

跳跃表的实现比较简单, 最复杂的操作即是插入与删除结点, 需要仔细处理邻近结点的所有level[]中的所有zskiplistLevel结点中的forward与span的值的变更.

另外, 关于新创建的结点, 其`level[]`数组长度的随机算法, 在接口zslInsert的实现中, 核心代码片断如下:


```cgo
zskiplistNode *zslInsert(zskiplist *zsl, double score, sds ele) {
    //...

    level = zslRandomLevel();   // 随机生成新结点的, level[]数组的长度
        if (level > zsl->level) {   
        // 若生成的新结点的level[]数组的长度比当前表中所有结点的level[]的长度都大
        // 那么头结点中需要新增几个指向该结点的指针
        // 并刷新ziplist中的level字段
        for (i = zsl->level; i < level; i++) {
            rank[i] = 0;
            update[i] = zsl->header;
            update[i]->level[i].span = zsl->length;
        }
        zsl->level = level;
    }
    x = zslCreateNode(level,score,ele); // 创建新结点
    //... 执行插入操作
}

// 按幂次定律生成小于32的随机数的函数
// 宏 ZSKIPLIST_MAXLEVEL 的定义为32, 宏 ZSKIPLIST_P 被设定为 0.25
// 即 
//      level == 1的概率为 75%
//      level == 2的概率为 75% * 25%
//      level == 3的概率为 75% * 25% * 25%
//      ...
//      level == 31的概率为 0.75 * 0.25^30
//      而
//      level == 32的概率为 0.75 * sum(i = 31 ~ +INF){ 0.25^i }
int zslRandomLevel(void) {
    int level = 1;
    while ((random()&0xFFFF) < (ZSKIPLIST_P * 0xFFFF))
        level += 1;
    return (level<ZSKIPLIST_MAXLEVEL) ? level : ZSKIPLIST_MAXLEVEL;
}
```

### 2.5 intset

这是一个用于存储在序的整数的数据结构, 也底层数据结构中最简单的一个, 其定义与实现在src/intest.h与src/intset.c中, 关键定义如下:

```cgo
typedef struct intset {
    uint32_t encoding;
    uint32_t length;
    int8_t contents[];
} intset;

#define INTSET_ENC_INT16 (sizeof(int16_t))
#define INTSET_ENC_INT32 (sizeof(int32_t))
#define INTSET_ENC_INT64 (sizeof(int64_t))
```

inset结构中的encoding的取值有三个, 分别是宏INTSET_ENC_INT16, INTSET_ENC_INT32, INTSET_ENC_INT64. length代表其中存储的整数的个数, contents指向实际存储数值的连续内存区域. 其内存布局如下图所示:

![](../images/668722-20180910184108826-1909275147.png)


- intset中各字段, 包括contents中存储的数值, 都是以主机序(小端字节序)存储的. 这意味着Redis若运行在PPC这样的大端字节序的机器上时, 存取数据都会有额外的字节序转换开销
- 当encoding == INTSET_ENC_INT16时, contents中以int16_t的形式存储着数值. 类似的, 当encoding == INTSET_ENC_INT32时, contents中以int32_t的形式存储着数值.
- 但凡有一个数值元素的值超过了int32_t的取值范围, 整个intset都要进行升级, 即所有的数值都需要以int64_t的形式存储. 显然升级的开销是很大的.
- intset中的数值是以升序排列存储的, 插入与删除的复杂度均为O(n). 查找使用二分法, 复杂度为O(log_2(n))
- intset的代码实现中, 不预留空间, 即每一次插入操作都会调用zrealloc接口重新分配内存. 每一次删除也会调用zrealloc接口缩减占用的内存. 省是省了, 但内存操作的时间开销上升了.
- intset的编码方式一经升级, 不会再降级.

总之, intset适合于如下数据的存储:

- 所有数据都位于一个稳定的取值范围中. 比如均位于int16_t或int32_t的取值范围中
- 数据稳定, 插入删除操作不频繁. 能接受O(lgn)级别的查找开销

### 2.6 ziplist

ziplist是Redis底层数据结构中, 最苟的一个结构. 它的设计宗旨就是: 省内存, 从牙缝里省内存. 设计思路和TLV一致, 但为了从牙缝里节省内存, 做了很多额外工作.

ziplist的内存布局与intset一样: 就是一块连续的内存空间. 但区域划分比较复杂, 概览如下图:


![](../images/668722-20180910184121575-550443104.png)

- 和intset一样, ziplist中的所有值都是以小端序存储的
- zlbytes字段的类型是uint32_t, 这个字段中存储的是整个ziplist所占用的内存的字节数
- zltail字段的类型是uint32_t, 它指的是ziplist中最后一个entry的偏移量. 用于快速定位最后一个entry, 以快速完成pop等操作
- zllen字段的类型是uint16_t, 它指的是整个ziplit中entry的数量. 这个值只占16位, 所以蛋疼的地方就来了: 如果ziplist中entry的数目小于65535, 那么该字段中存储的就是实际entry的值. 若等于或超过65535, 那么该字段的值固定为65535, 但实际数量需要一个个entry的去遍历所有entry才能得到.
- zlend是一个终止字节, 其值为全F, 即0xff. ziplist保证任何情况下, 一个entry的首字节都不会是255

在画图展示entry的内存布局之前, 先讲一下entry中都存储了哪些信息:

- 每个entry中存储了它前一个entry所占用的字节数. 这样支持ziplist反向遍历.
- 每个entry用单独的一块区域, 存储着当前结点的类型: 所谓的类型, 包括当前结点存储的数据是什么(二进制, 还是数值), 如何编码(如果是数值, 数值如何存储, 如果是二进制数据, 二进制数据的长度)
- 最后就是真实的数据了

entry的内存布局如下所示:

![](../images/668722-20180910184134043-2011419560.png)

`prevlen`即是"前一个entry所占用的字节数", 它本身是一个变长字段, 规约如下:

- 若前一个entry占用的字节数小于 254, 则prevlen字段占一字节
- 若前一个entry占用的字节数等于或大于 254, 则prevlen字段占五字节: 第一个字节值为 254, 即0xfe, 另外四个字节, 以uint32_t存储着值.

`encoding`字段的规约就复杂了许多

- 若数据是二进制数据, 且二进制数据长度小于64字节(不包括64), 那么encoding占一字节. 在这一字节中, 高两位值固定为0, 低六位值以无符号整数的形式存储着二进制数据的长度. 即 00xxxxxx, 其中低六位bitxxxxxx是用二进制保存的数据长度.
- 若数据是二进制数据, 且二进制数据长度大于或等于64字节, 但小于16384(不包括16384)字节, 那么encoding占用两个字节. 在这两个字节16位中, 第一个字节的高两位固定为01, 剩余的14个位, 以小端序无符号整数的形式存储着二进制数据的长度, 即 01xxxxxx, yyyyyyyy, 其中yyyyyyyy是高八位, xxxxxx是低六位.
- 若数据是二进制数据, 且二进制数据的长度大于或等于16384字节, 但小于2^32-1字节, 则encoding占用五个字节. 第一个字节是固定值10000000, 剩余四个字节, 按小端序uint32_t的形式存储着二进制数据的长度. 这也是ziplist能存储的二进制数据的最大长度, 超过2^32-1字节的二进制数据, ziplist无法存储.
- 若数据是整数值, 则encoding和data的规约如下:
    1. 首先, 所有存储数值的entry, 其encoding都仅占用一个字节. 并且最高两位均是11
    2. 若数值取值范围位于[0, 12]中, 则encoding和data挤在同一个字节中. 即为1111 0001~1111 1101, 高四位是固定值, 低四位的值从0001至1101, 分别代表 0 ~ 12这十五个数值
    3. 若数值取值范围位于[-128, -1] [13, 127]中, 则encoding == 0b 1111 1110. 数值存储在紧邻的下一个字节, 以int8_t形式编码
    4. 若数值取值范围位于[-32768, -129] [128, 32767]中, 则encoding == 0b 1100 0000. 数值存储在紧邻的后两个字节中, 以小端序int16_t形式编码
    5. 若数值取值范围位于[-8388608, -32769] [32768, 8388607]中, 则encoding == 0b 1111 0000. 数值存储在紧邻的后三个字节中, 以小端序存储, 占用三个字节.
    6. 若数值取值范围位于[-2^31, -8388609] [8388608, 2^31 - 1]中, 则encoding == 0b 1101 0000. 数值存储在紧邻的后四个字节中, 以小端序int32_t形式编码
    7. 若数值取值均不在上述范围, 但位于int64_t所能表达的范围内, 则encoding == 0b 1110 0000, 数值存储在紧邻的后八个字节中, 以小端序int64_t形式编码

在大规模数值存储中, ziplist几乎不浪费内存空间, 其苟的程序到达了字节级别, 甚至对于[0, 12]区间的数值, 连data里的那一个字节也要省下来. 显然, ziplist是一种特别节省内存的数据结构, 但它的缺点也十分明显:

- 和intset一样, ziplist也不预留内存空间, 并且在移除结点后, 也是立即缩容, 这代表每次写操作都会进行内存分配操作.
- ziplist最蛋疼的一个问题是: 结点如果扩容, 导致结点占用的内存增长, 并且超过254字节的话, 可能会导致链式反应: 其后一个结点的entry.prevlen需要从一字节扩容至五字节. 最坏情况下, 第一个结点的扩容, 会导致整个ziplist表中的后续所有结点的entry.prevlen字段扩容. 虽然这个内存重分配的操作依然只会发生一次, 但代码中的时间复杂度是o(N)级别, 因为链式扩容只能一步一步的计算. 但这种情况的概率十分的小, 一般情况下链式扩容能连锁反映五六次就很不幸了. 之所以说这是一个蛋疼问题, 是因为, 这样的坏场景下, 其实时间复杂度并不高: 依次计算每个entry新的空间占用, 也就是o(N), 总体占用计算出来后, 只执行一次内存重分配, 与对应的memmove操作, 就可以了. 蛋疼说的是: 代码特别难写, 难读. 下面放一段处理插入结点时处理链式反应的代码片断, 大家自行感受一下:

```cgo
unsigned char *__ziplistInsert(unsigned char *zl, unsigned char *p, unsigned char *s, unsigned int slen) {
    size_t curlen = intrev32ifbe(ZIPLIST_BYTES(zl)), reqlen;
    unsigned int prevlensize, prevlen = 0;
    size_t offset;
    int nextdiff = 0;
    unsigned char encoding = 0;
    long long value = 123456789; /* initialized to avoid warning. Using a value
                                    that is easy to see if for some reason
                                    we use it uninitialized. */
    zlentry tail;

    /* Find out prevlen for the entry that is inserted. */
    if (p[0] != ZIP_END) {
        ZIP_DECODE_PREVLEN(p, prevlensize, prevlen);
    } else {
        unsigned char *ptail = ZIPLIST_ENTRY_TAIL(zl);
        if (ptail[0] != ZIP_END) {
            prevlen = zipRawEntryLength(ptail);
        }
    }

    /* See if the entry can be encoded */
    if (zipTryEncoding(s,slen,&value,&encoding)) {
        /* 'encoding' is set to the appropriate integer encoding */
        reqlen = zipIntSize(encoding);
    } else {
        /* 'encoding' is untouched, however zipStoreEntryEncoding will use the
         * string length to figure out how to encode it. */
        reqlen = slen;
    }
    /* We need space for both the length of the previous entry and
     * the length of the payload. */
    reqlen += zipStorePrevEntryLength(NULL,prevlen);
    reqlen += zipStoreEntryEncoding(NULL,encoding,slen);

    /* When the insert position is not equal to the tail, we need to
     * make sure that the next entry can hold this entry's length in
     * its prevlen field. */
    int forcelarge = 0;
    nextdiff = (p[0] != ZIP_END) ? zipPrevLenByteDiff(p,reqlen) : 0;
    if (nextdiff == -4 && reqlen < 4) {
        nextdiff = 0;
        forcelarge = 1;
    }

    /* Store offset because a realloc may change the address of zl. */
    offset = p-zl;
    zl = ziplistResize(zl,curlen+reqlen+nextdiff);
    p = zl+offset;

    /* Apply memory move when necessary and update tail offset. */
    if (p[0] != ZIP_END) {
        /* Subtract one because of the ZIP_END bytes */
        memmove(p+reqlen,p-nextdiff,curlen-offset-1+nextdiff);

        /* Encode this entry's raw length in the next entry. */
        if (forcelarge)
            zipStorePrevEntryLengthLarge(p+reqlen,reqlen);
        else
            zipStorePrevEntryLength(p+reqlen,reqlen);

        /* Update offset for tail */
        ZIPLIST_TAIL_OFFSET(zl) =
            intrev32ifbe(intrev32ifbe(ZIPLIST_TAIL_OFFSET(zl))+reqlen);

        /* When the tail contains more than one entry, we need to take
         * "nextdiff" in account as well. Otherwise, a change in the
         * size of prevlen doesn't have an effect on the *tail* offset. */
        zipEntry(p+reqlen, &tail);
        if (p[reqlen+tail.headersize+tail.len] != ZIP_END) {
            ZIPLIST_TAIL_OFFSET(zl) =
                intrev32ifbe(intrev32ifbe(ZIPLIST_TAIL_OFFSET(zl))+nextdiff);
        }
    } else {
        /* This element will be the new tail. */
        ZIPLIST_TAIL_OFFSET(zl) = intrev32ifbe(p-zl);
    }

    /* When nextdiff != 0, the raw length of the next entry has changed, so
     * we need to cascade the update throughout the ziplist */
    if (nextdiff != 0) {
        offset = p-zl;
        zl = __ziplistCascadeUpdate(zl,p+reqlen);
        p = zl+offset;
    }

    /* Write the entry */
    p += zipStorePrevEntryLength(p,prevlen);
    p += zipStoreEntryEncoding(p,encoding,slen);
    if (ZIP_IS_STR(encoding)) {
        memcpy(p,s,slen);
    } else {
        zipSaveInteger(p,value,encoding);
    }
    ZIPLIST_INCR_LENGTH(zl,1);
    return zl;
}

unsigned char *__ziplistCascadeUpdate(unsigned char *zl, unsigned char *p) {
    size_t curlen = intrev32ifbe(ZIPLIST_BYTES(zl)), rawlen, rawlensize;
    size_t offset, noffset, extra;
    unsigned char *np;
    zlentry cur, next;

    while (p[0] != ZIP_END) {
        zipEntry(p, &cur);
        rawlen = cur.headersize + cur.len;
        rawlensize = zipStorePrevEntryLength(NULL,rawlen);

        /* Abort if there is no next entry. */
        if (p[rawlen] == ZIP_END) break;
        zipEntry(p+rawlen, &next);

        /* Abort when "prevlen" has not changed. */
        if (next.prevrawlen == rawlen) break;

        if (next.prevrawlensize < rawlensize) {
            /* The "prevlen" field of "next" needs more bytes to hold
             * the raw length of "cur". */
            offset = p-zl;
            extra = rawlensize-next.prevrawlensize;
            zl = ziplistResize(zl,curlen+extra);
            p = zl+offset;

            /* Current pointer and offset for next element. */
            np = p+rawlen;
            noffset = np-zl;

            /* Update tail offset when next element is not the tail element. */
            if ((zl+intrev32ifbe(ZIPLIST_TAIL_OFFSET(zl))) != np) {
                ZIPLIST_TAIL_OFFSET(zl) =
                    intrev32ifbe(intrev32ifbe(ZIPLIST_TAIL_OFFSET(zl))+extra);
            }

            /* Move the tail to the back. */
            memmove(np+rawlensize,
                np+next.prevrawlensize,
                curlen-noffset-next.prevrawlensize-1);
            zipStorePrevEntryLength(np,rawlen);

            /* Advance the cursor */
            p += rawlen;
            curlen += extra;
        } else {
            if (next.prevrawlensize > rawlensize) {
                /* This would result in shrinking, which we want to avoid.
                 * So, set "rawlen" in the available bytes. */
                zipStorePrevEntryLengthLarge(p+rawlen,rawlen);
            } else {
                zipStorePrevEntryLength(p+rawlen,rawlen);
            }

            /* Stop here, as the raw length of "next" has not changed. */
            break;
        }
    }
    return zl;
}
```

这种代码的特点就是: 最好由作者去维护, 最好一次性写对. 因为读起来真的费劲, 改起来也很费劲.

### 2.7 quicklist

如果说ziplist是整个Redis中为了节省内存, 而写的最苟的数据结构, 那么称quicklist就是在最苟的基础上, 再苟了一层. 这个结构是Redis在3.2版本后新加的, 在3.2版本之前, 我们可以讲, dict是最复杂的底层数据结构, ziplist是最苟的底层数据结构. 在3.2版本之后, 这两个记录被双双刷新了.

这是一种, 以ziplist为结点的, 双端链表结构. 宏观上, quicklist是一个链表, 微观上, 链表中的每个结点都是一个ziplist.

它的定义与实现分别在src/quicklist.h与src/quicklist.c中, 其中关键定义如下:

```cgo
/* Node, quicklist, and Iterator are the only data structures used currently. */

/* quicklistNode is a 32 byte struct describing a ziplist for a quicklist.
 * We use bit fields keep the quicklistNode at 32 bytes.
 * count: 16 bits, max 65536 (max zl bytes is 65k, so max count actually < 32k).
 * encoding: 2 bits, RAW=1, LZF=2.
 * container: 2 bits, NONE=1, ZIPLIST=2.
 * recompress: 1 bit, bool, true if node is temporarry decompressed for usage.
 * attempted_compress: 1 bit, boolean, used for verifying during testing.
 * extra: 12 bits, free for future use; pads out the remainder of 32 bits */
typedef struct quicklistNode {
    struct quicklistNode *prev;
    struct quicklistNode *next;
    unsigned char *zl;
    unsigned int sz;             /* ziplist size in bytes */
    unsigned int count : 16;     /* count of items in ziplist */
    unsigned int encoding : 2;   /* RAW==1 or LZF==2 */
    unsigned int container : 2;  /* NONE==1 or ZIPLIST==2 */
    unsigned int recompress : 1; /* was this node previous compressed? */
    unsigned int attempted_compress : 1; /* node can't compress; too small */
    unsigned int extra : 10; /* more bits to steal for future usage */
} quicklistNode;

/* quicklistLZF is a 4+N byte struct holding 'sz' followed by 'compressed'.
 * 'sz' is byte length of 'compressed' field.
 * 'compressed' is LZF data with total (compressed) length 'sz'
 * NOTE: uncompressed length is stored in quicklistNode->sz.
 * When quicklistNode->zl is compressed, node->zl points to a quicklistLZF */
typedef struct quicklistLZF {
    unsigned int sz; /* LZF size in bytes*/
    char compressed[];
} quicklistLZF;

/* quicklist is a 40 byte struct (on 64-bit systems) describing a quicklist.
 * 'count' is the number of total entries.
 * 'len' is the number of quicklist nodes.
 * 'compress' is: -1 if compression disabled, otherwise it's the number
 *                of quicklistNodes to leave uncompressed at ends of quicklist.
 * 'fill' is the user-requested (or default) fill factor. */
typedef struct quicklist {
    quicklistNode *head;
    quicklistNode *tail;
    unsigned long count;        /* total count of all entries in all ziplists */
    unsigned long len;          /* number of quicklistNodes */
    int fill : 16;              /* fill factor for individual nodes */
    unsigned int compress : 16; /* depth of end nodes not to compress;0=off */
} quicklist;

typedef struct quicklistIter {
    const quicklist *quicklist;
    quicklistNode *current;
    unsigned char *zi;
    long offset; /* offset in current ziplist */
    int direction;
} quicklistIter;

typedef struct quicklistEntry {
    const quicklist *quicklist;
    quicklistNode *node;
    unsigned char *zi;
    unsigned char *value;
    long long longval;
    unsigned int sz;
    int offset;
} quicklistEntry;
```

这里定义了五个结构体:

- quicklistNode, 宏观上, quicklist是一个链表, 这个结构描述的就是链表中的结点. 它通过zl字段持有底层的ziplist. 简单来讲, 它描述了一个ziplist实例
- quicklistLZF, ziplist是一段连续的内存, 用LZ4算法压缩后, 就可以包装成一个quicklistLZF结构. 是否压缩quicklist中的每个ziplist实例是一个可配置项. 若这个配置项是开启的, 那么quicklistNode.zl字段指向的就不是一个ziplist实例, 而是一个压缩后的quicklistLZF实例
- quicklist. 这就是一个双链表的定义. head, tail分别指向头尾指针. len代表链表中的结点. count指的是整个quicklist中的所有ziplist中的entry的数目. fill字段影响着每个链表结点中ziplist的最大占用空间, compress影响着是否要对每个ziplist以LZ4算法进行进一步压缩以更节省内存空间.
- quicklistIter是一个迭代器
- quicklistEntry是对ziplist中的entry概念的封装. quicklist作为一个封装良好的数据结构, 不希望使用者感知到其内部的实现, 所以需要把ziplist.entry的概念重新包装一下.

quicklist的内存布局图如下所示:

![](../images/668722-20180910184206679-1690711957.png)

下面是有关quicklist的更多额外信息:

quicklist.fill的值影响着每个链表结点中, ziplist的长度.

1. 当数值为负数时, 代表以字节数限制单个ziplist的最大长度. 具体为:
    1. -1 不超过4kb
    2. -2 不超过 8kb
    3. -3 不超过 16kb
    4. -4 不超过 32kb
    5. -5 不超过 64kb
    6. 当数值为正数时, 代表以entry数目限制单个ziplist的长度. 值即为数目. 由于该字段仅占16位, 所以以entry数目限制ziplist的容量时, 最大值为2^15个
2. quicklist.compress的值影响着quicklistNode.zl字段指向的是原生的ziplist, 还是经过压缩包装后的quicklistLZF
    1. 0 表示不压缩, zl字段直接指向ziplist
    2. 1 表示quicklist的链表头尾结点不压缩, 其余结点的zl字段指向的是经过压缩后的quicklistLZF
    3. 2 表示quicklist的链表头两个, 与末两个结点不压缩, 其余结点的zl字段指向的是经过压缩后的quicklistLZF
    4. 以此类推, 最大值为2^16
3. quicklistNode.encoding字段, 以指示本链表结点所持有的ziplist是否经过了压缩. 1代表未压缩, 持有的是原生的ziplist, 2代表压缩过
4. quicklistNode.container字段指示的是每个链表结点所持有的数据类型是什么. 默认的实现是ziplist, 对应的该字段的值是2, 目前Redis没有提供其它实现. 所以实际上, 该字段的值恒为2
5. quicklistNode.recompress字段指示的是当前结点所持有的ziplist是否经过了解压. 如果该字段为1即代表之前被解压过, 且需要在下一次操作时重新压缩.

`quicklist`的具体实现代码篇幅很长, 这里就不贴代码片断了, 从内存布局上也能看出来, 由于每个结点持有的ziplist是有上限长度的, 所以在与操作时要考虑的分支情况比较多. 想想都蛋疼.

quicklist有自己的优点, 也有缺点, 对于使用者来说, 其使用体验类似于线性数据结构, list作为最传统的双链表, 结点通过指针持有数据, 指针字段会耗费大量内存. ziplist解决了耗费内存这个问题. 但引入了新的问题: 每次写操作整个ziplist的内存都需要重分配. quicklist在两者之间做了一个平衡. 并且使用者可以通过自定义quicklist.fill, 根据实际业务情况, 经验主义调参.

### 2.8 zipmap

dict作为字典结构, 优点很多, 扩展性强悍, 支持平滑扩容等等, 但对于字典中的键值均为二进制数据, 且长度都很小时, dict的中的一坨指针会浪费不少内存, 因此Redis又实现了一个轻量级的字典, 即为zipmap.

zipmap适合使用的场合是:

- 键值对量不大, 单个键, 单个值长度小
- 键值均是二进制数据, 而不是复合结构或复杂结构. dict支持各种嵌套, 字典本身并不持有数据, 而仅持有数据的指针. 但zipmap是直接持有数据的.

zipmap的定义与实现在src/zipmap.h与src/zipmap.c两个文件中, 其定义与实现均未定义任何struct结构体, 因为zipmap的内存布局就是一块连续的内存空间. 其内存布局如下所示:

![](../images/668722-20180910184223910-1797391394.png)


- zipmap起始的第一个字节存储的是zipmap中键值对的个数. 如果键值对的个数大于254的话, 那么这个字节的值就是固定值254, 真实的键值对个数需要遍历才能获得.
- zipmap的最后一个字节是固定值0xFF
- zipmap中的每一个键值对, 称为一个entry, 其内存占用如上图, 分别六部分:
    1. len_of_key, 一字节或五字节. 存储的是键的二进制长度. 如果长度小于254, 则用1字节存储, 否则用五个字节存储, 第一个字节的值固定为0xFE, 后四个字节以小端序uint32_t类型存储着键的二进制长度.
    2. key_data为键的数据
    3. len_of_val, 一字节或五字节, 存储的是值的二进制长度. 编码方式同len_of_key
    4. len_of_free, 固定值1字节, 存储的是entry中未使用的空间的字节数. 未使用的空间即为图中的free, 它一般是由于键值对中的值被替换发生的. 比如, 键值对hello <-> word被修改为hello <-> w后, 就空了四个字节的闲置空间
    5. val_data, 为值的数据
    6. free, 为闲置空间. 由于len_of_free的值最大只能是254, 所以如果值的变更导致闲置空间大于254的话, zipmap就会回收内存空间.




# Redis主从复制原理

相信很多小伙伴都已经配置过主从复制，但是对于redis主从复制的工作流程和常见问题很多都没有深入的了解。咔咔这次用时俩天时间给大家整理一份redis主从复制的全部知识点。本文实现所需环境 centos7.0 redis4.0

## 一、什么是Redis主从复制？

主从复制就是现在有俩台redis服务器，把一台redis的数据同步到另一台redis数据库上。前者称之为主节点（master），后者为从节点（slave）。数据是只能master往slave同步单向。

但是在实际过程中是不可能只有俩台redis服务器来做主从复制的，这也就意味这每台redis服务器都有可能会称为主节点（master）

下图案例中，我们的slave3既是master的从节点，也是slave的主节点。

先知道这么个概念，更多详解继续查看下文。

![](../images/af2760bc01cc4da6808cd34087b7176a.jpeg)

## 二、为什么需要Redis主从复制？

假设我们现在就一台redis服务器，也就是单机状态。

在这种情况下会出现的第一个问题就是服务器宕机，直接导致数据丢失。如果项目是跟￥占关系的，那造成的后果就可想而知。

第二个情况就是内存问题了，当只有一台服务器时内存肯定会到达峰值的，不可能对一台服务器进行无限升级的。

![](../images/b781d9b31ebd4b718de3083cf036fa4b.jpeg)

所以针对以上俩个问题，我们就多准备几台服务器，配置主从复制。将数据保存在多个服务器上。并且保证每个服务器的数据是同步的。即使有一个服务器宕机了，也不会影响用户的使用。redis可以继续实现高可用、同时实现数据的冗余备份。

这会应该会有很多疑问，master跟slave怎么连接呢？ 如何同步数据呢？ 假如master服务器宕机了呢？别着急，一点一点解决你的问题。

![](../images/6289b6c04279481789a6459dc8da2cb1.jpeg)

## 三、Redis主从复制的作用

在上边我们说了为什么使用redis的主从复制，那么主从复制的作用就是针对为什么使用它来讲了。

我们继续使用这个图来谈论

- 第一点是数据冗余了，实现了数据的热备份，是持久化之外的另一种方式。
- 第二点是针对单机故障问题。当主节点也就是master出现问题时，可以由从节点来提供服务也就是slave，实现了快速恢复故障，也就是服务冗余。
- 第三点是读写分离，master服务器主要是写，slave主要用来读数据，可以提高服务器的负载能力。同时可以根据需求的变化，添加从节点的数量。
- 第四点是负载均衡，配合读写分离，有主节点提供写服务，从节点提供读服务，分担服务器负载，尤其在写少读多的情况下，通过多个从节点分担读负载，可以大大提高redis服务器的并发量和负载。
- 第五点是高可用的基石，主从复制是哨兵和集群能够实施的基础，因此我们可以说主从复制是高可用的基石。

![](../images/11b30c167de447ffad6d1ab741f921f6.jpeg)

## 四、配置Redis主从复制
说了这么多，我们先简单的配置一个主从复制案例，然后在谈实现的原理。

redis存储路径为：usr/local/redis

日志跟配置文件存储在：usr/local/redis/data

首先我们先配置俩个配置文件，分别为redis6379.conf 和 redis6380.conf

![](../images/9823874fa8364612b5a4bdf90b705c4c.jpeg)

修改配置文件，主要就是修改端口。为了查看方便在把日志文件和持久化文件的名字都用各自的端口来做标识。

![](../images/94dee06ef9b64c118916593c0a47f40e.jpeg)

然后分别开启俩个redis服务，一个端口为6379，一个端口为6380。执行命令redis-server redis6380.conf,然后使用redis-cli -p 6380连接，因为redis的默认端口就是6379所以我们启动另外一台redis服务器直接使用redis-server redis6379.conf 然后直接使用redis-cli直接连接就可以。

![](../images/cc285906b45845efa6a5193ec1747ce0.jpeg)

这个时候我们就成功的配置了俩个redis服务，一台为6380，一台为6379，这里只是为了演示。实际工作中是需要配置在俩台不同的服务器的。

![](../images/745a5d13ff6a4dbc89e287f3188ca111.jpeg)

### 1. 使用客户端命令行启动

我们先得有一个概念，就是在配置主从复制时，所有的操作都是在从节点来操作，也就是slave。

那么我们在从节点执行一个命令为 slaveof 127.0.0.1 6379，执行完就代表我们连接上了。

![](../images/789513781c64485b9130dc239706fbbf.jpeg)

我们先测试一下看是否实现主从复制。在master这台服务器上执行俩个set kaka 123 和 set master 127.0.0.1，然后在slave6380端口是可以成功获取到的，也就说明我们的主从复制就已经配置完成了。但是在实现生产环境可不是就这样完事了，后边会在进一步对主从复制进行优化，直到实现高可用。

![](../images/a4d3cb957f3f4b35b280118bb129fe58.jpeg)

### 2. 使用配置文件启用

在使用配置文件启动主从复制之前呢！先需要把之前使用客户端命令行连接的断开，在从主机执行slaveof no one即可断开主从复制。

![](../images/fd9f917bf17e43d5b7d01ed513e05635.jpeg)

在哪可以查看从节点已经断开了主节点呢！在主节点的客户端输入命令行info查看

这张图是使用从节点使用客户端命令行连接主节点后，在主节点的客户端输入info打印的信息，可以看到有一个slave0的一个信息。

![](../images/a2e38eaa9f0541769c7569abffe6da48.jpeg)

这个图是在从节点执行完slaveof no one 后，在主节点打印的info，说明从节点已经跟主节点断开连接了。

![](../images/dab19075906049dfa6156de45a8bdc1e.jpeg)

在根据配置文件启动redis服务，redis-server redis6380.conf

当在从节点重新启动后就可以在主节点直接查看到从节点的连接信息。

![](../images/d33af09c79c04875b884f51b0c752eeb.jpeg)

测试数据，主节点写的东西，从节点还是会自动同步的。

![](../images/5a043effdcc54f7bb67e3fa13fcbb2cc.jpeg)

### 3. 启动redis服务器时启动

这种方式配置也是很简单，在启动redis服务器时直接就启动主从复制，执行命令：redis-server --slaveof host port 即可。

### 4. 主从复制启动后的日志信息查看

这个是主节点的日志信息

![](../images/b68dbf0d64844cf8ab83fca534b04e4a.jpeg)

这个是从节点的信息，其中有连接主节点信息，还有RDB快照保存。

![](../images/1d905286e19f4a3b960d596b184fa8d5.jpeg)

## 五、主从复制工作原理

### 1. 主从复制的三个阶段

主从复制完整的工作流程分为以下三个阶段。每一段都有自己的内部工作流程，那么我们会对这三个过程进行谈论。

- 建立连接过程：这个过程就是slave跟master连接的过程
- 数据同步过程：是master给slave同步数据的过程
- 命令传播过程：是反复同步数据

![](../images/1727327958931dbc.jpg)

### 2. 第一阶段：建立连接过程

![](../images/17188139b47a27ca.jpg)

上图是一个完整主从复制建立连接工作流程。然后使用简短的话语来描述上边的工作流程。

1. 设置master的地址和端口，保存master的信息
2. 建立socket连接（这个连接做的事情下文会说）
3. 持续发送ping命令
4. 身份验证
5. 发送slave端口信息

在建立连接的过程中，从节点会保存master的地址和端口、主节点master保存从节点slave的端口。

### 3. 第二阶段：数据同步阶段过程

![](../images/17273279684debb7.jpg)

这张图是详细描述第一次从节点连接主节点时的数据同步过程。
当从节点第一次连接主节点时，先会执行一次全量复制这次的全量复制是无法避免的。
全量复制执行完成后，主节点就会发送复制积压缓冲区的数据，然后从节点就会执行bgrewriteaof恢复数据，这也就是部分复制。
在这个阶段提到了三个新点，全量复制、部分复制、复制缓冲积压区。会在下文的常见问题里详细说明这几个点。

### 4. 第三阶段：命令传播阶段

当master数据库被修改后，主从服务器的数据不一致后，此时就会让主从数据同步到一致，这个过程称之为命令传播。
master会将接收到的数据变更命令发送给slave，slave接收命令后执行命令，让主从数据达到一致。
命令传播阶段的部分复制


- 在命令传播阶段出现断网的情况，或者网络抖动时会导致连接断开（connection  lost）
- 这个时候主节点master还是会继续往replbackbuffer（复制缓冲积压区）写数据
- 从节点会继续尝试连接主机（connect to master）
- 当从节点把自己的runid和复制偏移量发送给主节点，并且执行pysnc命令同步
- 如果master判断偏移量是在复制缓冲区范围内，就会返回continue命令。并且发送复制缓冲区的数据给从节点。
- 从节点接收数据执行bgrewriteaof，恢复数据


## 六. 详细介绍主从复制原理（全量复制+部分复制）

![](../images/1727327968bf0895.jpg)

这个过程就是主从复制最齐全的流程讲解。那么下来我们对每一步进程简单的介绍

1. 从节点发送指令psync ? 1 psync runid offset   找对应的runid索取数据。但是这里可以考虑一下，当从节点第一次连接的时候根本就不知道主节点的runid 和 offset  。所以第一次发送的指令是psync ？ 1意思就是主节点的数据我全要。
2. 主节点开始执行bgsave生成RDB文件，记录当前的复制偏移量offset
3. 主节点这个时候会把自己的runid  和  offset  通过 +FULLRESYNC  runid  offset   指令  通过socket发送RDB文件给从节点。
4. 从节点接收到+FULLRESYNC  保存主节点的runid和offset    然后清空当前所有数据，通过socket接收RDB文件，开始恢复RDB数据。
5. 在全量复制后，从节点已经获取到了主节点的runid和offset，开始发送指令 psync runid offset
6. 主节点接收指令，判断runid是否匹配，判断offset是否在复制缓冲区中。
7. 主节点判断runid和offset有一个不满足，就会在返回到步骤2继续执行全量复制。这里的runid不匹配只有的可能是从节点重启了这个问题后边会解决，offset（偏移量）不匹配就是复制积压缓冲区溢出了。 如果runid或offset校验通过，从节点的offset和主节点的offset相同时则忽略。 如果runid或offset检验通过，从节点的offset与offset不相同，则会发送 +CONTINUE  offset(这个offset为主节点的)，通过socket发送复制缓冲区中从节点offset到主节点offset的数据。
8. 从节点收到+CONTINUE 保存master的offset  通过socket接收到信息后，执行bgrewriteaof，恢复数据。

**1-4是全量复制    5-8是部分复制**

在主节点的第3步下面  主节点在主从复制的期间是一直在接收客户端的数据，主节点的offset是一直变化的。只有有变化就会给每个slave进行发送，这个发送的过程称之为心跳机制

## 七. 心跳机制

在命令传播阶段是，主节点与从节点之间一直都需要进行信息互换，使用心跳机制进行维护，实现主节点和从节点连接保持在线。


- master心跳
    - 指令：ping
    - 默认10秒进行一次，是由参数repl-ping-slave-period决定的
    - 主要做的事情就是判断从节点是否在线
    - 可以使用info replication  来查看从节点租后一次连接时间的间隔，lag为0或者为1就是正常状态。
- slave心跳任务
    - 指令：replconf ack {offset}
    - 每秒执行一次
    - 主要做的事情是给主节点发送自己的复制偏移量，从主节点获取到最新的数据变更命令，还做一件事情就是判断主节点是否在线。

**心跳阶段的注意事项** 主节点为保障数据稳定性，当从节点挂掉的数量或者延迟过高时。将会拒绝所有信息同步。

这里有俩个参数可以进行配置调整：

```bash
min-slaves-to-write   2
min-slaves-max-lag 8
```

这俩个参数表示从节点的数量就剩余2个，或者从节点的延迟大于8秒时，主节点就会强制关闭maste功能，停止数据同步。

那么主节点是如何知道从节点挂掉的数量和延迟时间呢！  在心跳机制里边slave 会每隔一秒发送perlconf  ack  这个指令，这个指令可携带偏移量，也可以携带从节点的延迟时间和从节点的数量。

## 八、部分复制的三个核心要素

### 1. 服务器的运行id （run id）

我们先看一下这个run id是什么，执行info命令即可看到。在上文中我们查看启动日志信息也可以看到。

![](../images/172732797248305f.jpg)

redis在启动时会自动生成一个随机的id（这里需要注意的是每次启动的id都会不一样），是由40个随机的十六进制字符串组成，用来唯一识别一个redis节点。
在主从复制初次启动时，master会把自己的runid发送给slave，slave会保存master的这个id，我们可以使用info命令查看

![](../images/17273279bd947317.jpg)

当断线重连时，slave把这个id发送给master，如果slave保存的runid与master现在的runid相同，master会尝试使用部分复制（这块能否复制成功还有一个因素就是偏移量）。如果slave保存的runid与master现在的runid不同，则会直接进行全量复制。

### 2. 复制积压缓冲区

复制缓冲积压区是一个先进先出的队列，用户存储master收集数据的命令记录。复制缓冲区的默认存储空间是1M。
可以在配置文件修改repl-backlog-size 1mb来控制缓冲区大小，这个比例可以根据自己的服务器内存来修改，咔咔这边是预留出了30%左右。

**复制缓冲区到底存储的是什么？**

当执行一个命令为set name kaka时，我们可以查看持久化文件查看

![](../images/172732798545411e.jpg)

那么复制积压缓冲区就是存储的aof持久化的数据，并且以字节分开，并且每个字节都有自己的偏移量。这个偏移量也就是复制偏移量（offset）

![](../images/172732798af59f73.jpg)

那为什么会说复制缓冲积压区有可能会导致全量复制呢
在命令传播阶段，主节点会把收集的数据存储到复制缓冲区中，然后在发送给从节点。就是这里出现了问题，当主节点数据量在一瞬间特别大的时候，超出了复制缓冲区的内存，就会有一部分数据会被挤出去，从而导致主节点和从节点的数据不一致。从而进行全量复制。如果这个缓冲区大小设置不合理那么很大可能会造成死循环，从节点就会一直全量复制，清空数据，全量复制。

### 3. 复制偏移量（offset）

![](../images/17273279906f511c.jpg)

主节点复制偏移量是给从节点发送一次记录一次，从节点是接收一次记录一次。
用于同步信息，对比主节点和从节点的差异，当slave断联时恢复数据使用。
这个值也就是来自己于复制缓冲积压区里边的那个偏移量。

## 九. 主从复制常见的问题

### 1. 主节点重启问题（内部优化）

当主节点重启后，runid的值将发生变化，会导致所有的从节点进行全量复制。

这个问题我们无需考虑，知道系统是怎么优化的即可。

在建立完主从复制后主节点会创建master-replid变量，这个生成的策略跟runid一样，长度是41位，runid长度是40位，然后发送给从节点。

在主节点执行shutdown  save命令时，进行了一次RDB持久化会把runid 和 offset保存到RDB文件中。可以使用命令redis-check-rdb查看该信息。

![](../images/17273279a6e90503.jpg)

主节点重启后加载RDB文件，将文件中的repl-id  和repl-offset加载到内存中。纵使让所有从节点认为还是之前的主节点。

### 2. 从节点网络中断偏移量越界导致全量复制

由于网络环境不佳，从节点网络中断。复制积压缓冲区内存过小导致数据溢出，伴随着从节点偏移量越界，导致全量复制。有可能会导致反复的全量复制。
解决方案：修改复制积压缓冲区的大小：repl-backlog-size
设置建议：测试主节点连接从节点的时间，获取主节点每秒平均产生的命令总量write_size_per_second
复制缓冲区空间设置  =   2  *  主从连接时间   *   主节点每秒产生的数据总量

### 3. 频繁的网路中断

由于主节点的cpu占用过高，或者从节点频繁连接。出现这种情况造成的结果就是主节点各种资源被严重占用，其中包括但不限于缓冲区，宽带，连接等。
为什么会出现主节点资源被严重占用？
在心跳机制中，从节点每秒会发送一个指令replconf  ack指令到主节点。
从节点执行了慢查询，占用大量的cpu
主节点每秒调用复制定时函数replicationCron，然后从节点长时间没有相应。

**解决方案：**

- 设置从节点超时释放
- 设置参数：repl-timeout
- 这个参数默认为60秒。超过60秒，释放slave。

### 4. 数据不一致问题

由于网络因素，多个从节点的数据会不一致。这个因素是没有办法避免的。

**关于这个问题给出俩个解决方案：**

- 第一个数据需要高度一致配置一台redis服务器，读写都用一台服务器，这种方式仅限于少量数据，并且数据需高度一直。
- 第二个监控主从节点的偏移量，如果从节点的延迟过大，暂时屏蔽客户端对该从节点的访问。设置参数为slave-serve-stale-data  yes|no。  这个参数一但设置就只能响应info  slaveof等少数命令。

**5. 从节点故障**

这个问题直接在客户端维护一个可用节点列表，当从节点故障时，切换到其他节点进行工作，这个问题在后边集群会说到。

## 十. 总结

本文主要讲解了什么是主从复制、主从复制工作的三大阶段以及工作流程、部分复制的三大核心。命令传播阶段的心跳机制。最后说明了主从复制常见问题。


>作者：原来是咔咔
>链接：https://juejin.im/post/5ed5ccb66fb9a047df7ca9a4
>来源：掘金
>著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。




















# Redis中内存淘汰算法实现

Redis的`maxmemory`支持的内存淘汰机制使得其成为一种有效的缓存方案，成为memcached的有效替代方案。

当内存达到`maxmemory`后，Redis会按照`maxmemory-policy`启动淘汰策略。

Redis 3.0中已有淘汰机制：

- noeviction
- allkeys-lru
- volatile-lru
- allkeys-random
- volatile-random
- volatile-ttl

| maxmemory-policy	| 含义	|特性 |
| ----- | ----- | ----- |
noeviction	|不淘汰	|内存超限后写命令会返回错误(如OOM, del命令除外)	
allkeys-lru	|所有key的LRU机制	在|所有key中按照最近最少使用LRU原则剔除key，释放空间	
volatile-lru	|易失key的LRU	|仅以设置过期时间key范围内的LRU(如均为设置过期时间，则不会淘汰)	
allkeys-random	|所有key随机淘汰|	一视同仁，随机	
volatile-random	|易失Key的随机	|仅设置过期时间key范围内的随机	
volatile-ttl	|易失key的TTL淘汰|	按最小TTL的key优先淘汰

其中LRU(less recently used)经典淘汰算法在Redis实现中有一定优化设计，来保证内存占用与实际效果的平衡，这也体现了工程应用是空间与时间的平衡性。

> PS：值得注意的，在主从复制模式Replication下，从节点达到maxmemory时不会有任何异常日志信息，但现象为增量数据无法同步至从节点。

## Redis 3.0中近似LRU算法

Redis中LRU是近似LRU实现，并不能取出理想LRU理论中最佳淘汰Key，而是通过从小部分采样后的样本中淘汰局部LRU键。

Redis 3.0中近似LRU算法通过增加待淘汰元素池的方式进一步优化，最终实现与精确LRU非常接近的表现。

> 精确LRU会占用较大内存记录历史状态，而近似LRU则用较小内存支出实现近似效果。

以下是理论LRU和近似LRU的效果对比：

![](../images/lru_comparison.png)

- 按时间顺序接入不同键，此时最早写入也就是最佳淘汰键
- 浅灰色区域：被淘汰的键
- 灰色区域：未被淘汰的键
- 绿色区域：新增写入的键

总结图中展示规律，

- 图1Theoretical LRU符合预期：最早写入键逐步被淘汰
- 图2Approx LRU Redis 3.0 10 samples：Redis 3.0中近似LRU算法(采样值为10)
- 图3Approx LRU Redis 2.8 5 samples：Redis 2.8中近似LRU算法(采样值为5)
- 图4Approx LRU Redis 3.0 5 samples：Redis 3.0中近似LRU算法(采样值为5)

结论：

- 通过图4和图3对比：得出相同采样值下，3.0比2.8的LRU淘汰机制更接近理论LRU
- 通过图4和图2对比：得出增加采样值，在3.0中将进一步改善LRU淘汰效果逼近理论LRU
- 对比图2和图1：在3.0中采样值为10时，效果非常接近理论LRU

采样值设置通过maxmemory-samples指定，可通过CONFIG SET maxmemory-samples <count>动态设置，也可启动配置中指定maxmemory-samples <count>

源码解析

```cgo
int freeMemoryIfNeeded(void){
    while (mem_freed < mem_tofree) {
        if (server.maxmemory_policy == REDIS_MAXMEMORY_NO_EVICTION)
        return REDIS_ERR; /* We need to free memory, but policy forbids. */

        if (server.maxmemory_policy == REDIS_MAXMEMORY_ALLKEYS_LRU ||
                server.maxmemory_policy == REDIS_MAXMEMORY_ALLKEYS_RANDOM)
            {......}
        /* volatile-random and allkeys-random policy */
        if (server.maxmemory_policy == REDIS_MAXMEMORY_ALLKEYS_RANDOM ||
                server.maxmemory_policy == REDIS_MAXMEMORY_VOLATILE_RANDOM)
            {......}
        /* volatile-lru and allkeys-lru policy */
        else if (server.maxmemory_policy == REDIS_MAXMEMORY_ALLKEYS_LRU ||
            server.maxmemory_policy == REDIS_MAXMEMORY_VOLATILE_LRU)
        {
            // 淘汰池函数
            evictionPoolPopulate(dict, db->dict, db->eviction_pool);
            while(bestkey == NULL) {
                evictionPoolPopulate(dict, db->dict, db->eviction_pool);
                // 从后向前逐一淘汰
                for (k = REDIS_EVICTION_POOL_SIZE-1; k >= 0; k--) {
                    if (pool[k].key == NULL) continue;
                    de = dictFind(dict,pool[k].key); // 定位目标

                    /* Remove the entry from the pool. */
                    sdsfree(pool[k].key);
                    /* Shift all elements on its right to left. */
                    memmove(pool+k,pool+k+1,
                        sizeof(pool[0])*(REDIS_EVICTION_POOL_SIZE-k-1));
                    /* Clear the element on the right which is empty
                     * since we shifted one position to the left.  */
                    pool[REDIS_EVICTION_POOL_SIZE-1].key = NULL;
                    pool[REDIS_EVICTION_POOL_SIZE-1].idle = 0;

                    /* If the key exists, is our pick. Otherwise it is
                     * a ghost and we need to try the next element. */
                    if (de) {
                        bestkey = dictGetKey(de); // 确定删除键
                        break;
                    } else {
                        /* Ghost... */
                        continue;
                    }
                }
            }
        }
        /* volatile-ttl */
        else if (server.maxmemory_policy == EDIS_MAXMEMORY_VOLATILE_TTL) {......}

        // 最终选定待删除键bestkey
        if (bestkey) {
            long long delta;
            robj *keyobj = createStringObject(bestkey,sdslenbestkey)); // 目标对象
            propagateExpire(db,keyobj);
            latencyStartMonitor(eviction_latency); // 延迟监控开始
            dbDelete(db,keyobj); // 从db删除对象
            latencyEndMonitor(eviction_latency);// 延迟监控结束
            latencyAddSampleIfNeeded("eviction-del",iction_latency); // 延迟采样
            latencyRemoveNestedEvent(latency,eviction_latency);
            delta -= (long long) zmalloc_used_memory();
            mem_freed += delta; // 释放内存计数
            server.stat_evictedkeys++; // 淘汰key计数，info中可见
            notifyKeyspaceEvent(REDIS_NOTIFY_EVICTED, "evicted", keyobj, db->id); // 事件通知
            decrRefCount(keyobj); // 引用计数更新
            keys_freed++;
            // 避免删除较多键导致的主从延迟，在循环内同步
            if (slaves) flushSlavesOutputBuffers();
        }
    }
}
```


## Redis 4.0中新的LFU算法

从Redis4.0开始，新增LFU淘汰机制，提供更好缓存命中率。LFU(Least Frequently Used)通过记录键使用频率来定位最可能淘汰的键。

对比LRU与LFU的差别：

- 在LRU中，某个键很少被访问，但在刚刚被访问后其被淘汰概率很低，从而出现这类异常持续存在的缓存；相对的，其他可能被访问的键会被淘汰
- 而LFU中，按访问频次淘汰最少被访问的键

Redis 4.0中新增两种LFU淘汰机制：

- volatile-lfu：设置过期时间的键按LFU淘汰
- allkeys-lfu：所有键按LFU淘汰

LFU使用Morris counters计数器占用少量位数来评估每个对象的访问频率，并随时间更新计数器。此机制实现与近似LRU中采样类似。但与LRU不同，LFU提供明确参数来指定计数更新频率。

- lfu-log-factor：0-255之间，饱和因子，值越小代表饱和速度越快
- lfu-decay-time：衰减周期，单位分钟，计数器衰减的分钟数

这两个因子形成一种平衡，通过少量访问 VS 多次访问 的评价标准最终形成对键重要性的评判。

> 原文： <http://fivezh.github.io/2019/01/10/Redis-LRU-algorithm/>










# Redis持久化的原理及优化

Redis为持久化提供了两种方式：

- RDB：在指定的时间间隔能对你的数据进行快照存储。
- AOF：记录每次对服务器写的操作,当服务器重启的时候会重新执行这些命令来恢复原始的数据。

本文将通过下面内容的介绍，希望能够让大家更全面、清晰的认识这两种持久化方式，同时理解这种保存数据的思路，应用于自己的系统设计中。

- 持久化的配置
- RDB与AOF持久化的工作原理
- 如何从持久化中恢复数据
- 关于性能与实践建议

## 持久化的配置

为了使用持久化的功能，我们需要先知道该如何开启持久化的功能。

### RDB的持久化配置

```bash
# 时间策略
save 900 1
save 300 10
save 60 10000

# 文件名称
dbfilename dump.rdb

# 文件保存路径
dir /home/work/app/redis/data/

# 如果持久化出错，主进程是否停止写入
stop-writes-on-bgsave-error yes

# 是否压缩
rdbcompression yes

# 导入时是否检查
rdbchecksum yes
```

配置其实非常简单，这里说一下持久化的时间策略具体是什么意思。

- save 900 1 表示900s内如果有1条是写入命令，就触发产生一次快照，可以理解为就进行一次备份
- save 300 10 表示300s内有10条写入，就产生快照

下面的类似，那么为什么需要配置这么多条规则呢？因为Redis每个时段的读写请求肯定不是均衡的，为了平衡性能与数据安全，我们可以自由定制什么情况下触发备份。所以这里就是根据自身Redis写入情况来进行合理配置。

`stop-writes-on-bgsave-error yes` 这个配置也是非常重要的一项配置，这是当备份进程出错时，主进程就停止接受新的写入操作，是为了保护持久化的数据一致性问题。如果自己的业务有完善的监控系统，可以禁止此项配置， 否则请开启。

关于压缩的配置 `rdbcompression yes` ，建议没有必要开启，毕竟Redis本身就属于CPU密集型服务器，再开启压缩会带来更多的CPU消耗，相比硬盘成本，CPU更值钱。

当然如果你想要禁用RDB配置，也是非常容易的，只需要在save的最后一行写上：`save ""`

### AOF的配置

```bash

# 是否开启aof
appendonly yes

# 文件名称
appendfilename "appendonly.aof"

# 同步方式
appendfsync everysec

# aof重写期间是否同步
no-appendfsync-on-rewrite no

# 重写触发配置
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

# 加载aof时如果有错如何处理
aof-load-truncated yes

# 文件重写策略
aof-rewrite-incremental-fsync yes
```

复制代码还是重点解释一些关键的配置：

`appendfsync everysec` 它其实有三种模式:

- always：把每个写命令都立即同步到aof，很慢，但是很安全
- everysec：每秒同步一次，是折中方案
- no：redis不处理交给OS来处理，非常快，但是也最不安全

一般情况下都采用 `everysec` 配置，这样可以兼顾速度与安全，最多损失1s的数据。

`aof-load-truncated yes` 如果该配置启用，在加载时发现aof尾部不正确是，会向客户端写入一个log，但是会继续执行，如果设置为 `no` ，发现错误就会停止，必须修复后才能重新加载。

## 工作原理

关于原理部分，我们主要来看RDB与AOF是如何完成持久化的，他们的过程是如何。

在介绍原理之前先说下Redis内部的定时任务机制，定时任务执行的频率可以在配置文件中通过 hz 10 来设置（这个配置表示1s内执行10次，也就是每100ms触发一次定时任务）。该值最大能够设置为：500，但是不建议超过：100，因为值越大说明执行频率越频繁越高，这会带来CPU的更多消耗，从而影响主进程读写性能。

定时任务使用的是Redis自己实现的 TimeEvent，它会定时去调用一些命令完成定时任务，这些任务可能会阻塞主进程导致Redis性能下降。因此我们在配置Redis时，一定要整体考虑一些会触发定时任务的配置，根据实际情况进行调整。

### RDB的原理

在Redis中RDB持久化的触发分为两种：自己手动触发与Redis定时触发。

**针对RDB方式的持久化，手动触发可以使用：**

- save：会阻塞当前Redis服务器，直到持久化完成，线上应该禁止使用。
- bgsave：该触发方式会fork一个子进程，由子进程负责持久化过程，因此阻塞只会发生在fork子进程的时候。

**而自动触发的场景主要是有以下几点：**

- 根据我们的 `save m n` 配置规则自动触发；
- 从节点全量复制时，主节点发送`rdb`文件给从节点完成复制操作，主节点会触发 `bgsave`；
- 执行 debug reload 时；
- 执行  shutdown时，如果没有开启aof，也会触发。

由于 save 基本不会被使用到，我们重点看看 bgsave 这个命令是如何完成RDB的持久化的。

![](../images/16530eac18882d66.jpg)

这里注意的是 fork 操作会阻塞，导致Redis读写性能下降。我们可以控制单个Redis实例的最大内存，来尽可能降低Redis在fork时的事件消耗。以及上面提到的自动触发的频率减少fork次数，或者使用手动触发，根据自己的机制来完成持久化。

### AOF的原理

AOF的整个流程大体来看可以分为两步，一步是命令的实时写入（如果是 `appendfsync everysec` 配置，会有`1s`损耗），第二步是对aof文件的重写。
对于增量追加到文件这一步主要的流程是：`命令写入=>追加到aof_buf =>同步到aof磁盘`。那么这里为什么要先写入buf在同步到磁盘呢？如果实时写入磁盘会带来非常高的磁盘IO，影响整体性能。
aof重写是为了减少aof文件的大小，可以手动或者自动触发，关于自动触发的规则请看上面配置部分。fork的操作也是发生在重写这一步，也是这里会对主进程产生阻塞。
手动触发： `bgrewriteaof`，自动触发 就是根据配置规则来触发，当然自动触发的整体时间还跟Redis的定时任务频率有关系。

下面来看看重写的一个流程图：

![](../images/16530eac181d94c8.jpg)

对于上图有四个关键点补充一下：

在重写期间，由于主进程依然在响应命令，为了保证最终备份的完整性；因此它依然会写入旧的AOF file中，如果重写失败，能够保证数据不丢失。
为了把重写期间响应的写入信息也写入到新的文件中，因此也会为子进程保留一个buf，防止新写的file丢失数据。
重写是直接把当前内存的数据生成对应命令，并不需要读取老的AOF文件进行分析、命令合并。
AOF文件直接采用的文本协议，主要是兼容性好、追加方便、可读性高可认为修改修复。


> 不能是RDB还是AOF都是先写入一个临时文件，然后通过 rename 完成文件的替换工作。

## 从持久化中恢复数据

数据的备份、持久化做完了，我们如何从这些持久化文件中恢复数据呢？如果一台服务器上有既有RDB文件，又有AOF文件，该加载谁呢？
其实想要从这些文件中恢复数据，只需要重新启动Redis即可。我们还是通过图来了解这个流程：

![](../images/16530eac182b1b66.jpg)


启动时会先检查AOF文件是否存在，如果不存在就尝试加载RDB。那么为什么会优先加载AOF呢？因为AOF保存的数据更完整，通过上面的分析我们知道AOF基本上最多损失1s的数据。

## 性能与实践

通过上面的分析，我们都知道RDB的快照、AOF的重写都需要fork，这是一个重量级操作，会对Redis造成阻塞。因此为了不影响Redis主进程响应，我们需要尽可能降低阻塞。

- 降低fork的频率，比如可以手动来触发RDB生成快照、与AOF重写；
- 控制Redis最大使用内存，防止fork耗时过长；
- 使用更牛逼的硬件；
- 合理配置Linux的内存分配策略，避免因为物理内存不足导致fork失败。

在线上我们到底该怎么做？我提供一些自己的实践经验。

- 如果Redis中的数据并不是特别敏感或者可以通过其它方式重写生成数据，可以关闭持久化，如果丢失数据可以通过其它途径补回；
- 自己制定策略定期检查Redis的情况，然后可以手动触发备份、重写数据；
- 单机如果部署多个实例，要防止多个机器同时运行持久化、重写操作，防止出现内存、CPU、IO资源竞争，让持久化变为串行；
- 可以加入主从机器，利用一台从机器进行备份处理，其它机器正常响应客户端的命令；
- RDB持久化与AOF持久化可以同时存在，配合使用。


> 作者：大愚Talk
> 链接：https://juejin.im/post/5b70dfcf518825610f1f5c16
> 来源：掘金
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。# Redis 基础

## Redis常见的数据结构？

String、Hash、List、Set、SortedSet。

### 1.String 字符串类型

是redis中最基本的数据类型，一个key对应一个value。
    
String类型是二进制安全的，意思是 redis 的 string 可以包含任何数据。如数字，字符串，jpg图片或者序列化的对象。

**实战场景：**

1. 缓存： 经典使用场景，把常用信息，字符串，图片或者视频等信息放到redis中，redis作为缓存层，mysql做持久化层，降低mysql的读写压力。
2. 计数器：redis是单线程模型，一个命令执行完才会执行下一个，同时数据可以一步落地到其他的数据源。
3. session：常见方案spring session + redis实现session共享

### 2.Hash （哈希）

是一个Mapmap，指值本身又是一种键值对结构，如 `value={{field1,value1},......fieldN,valueN}}`

![](../images/1289934-20190621232209365-1000366002.png)

**实战场景：**

1.缓存： 能直观，相比string更节省空间，的维护缓存信息，如用户信息，视频信息等。

###  3.链表
 
List 说白了就是链表（redis 使用双端链表实现的 List），是有序的，value可以重复，可以通过下标取出对应的value值，左右两边都能进行插入和删除数据。

![](../images/1289934-20190621233618769-504231907.png)

使用列表的技巧

- lpush+lpop=Stack(栈)
- lpush+rpop=Queue（队列）
- lpush+ltrim=Capped Collection（有限集合）
- lpush+brpop=Message Queue（消息队列）

**实战场景：**

1.timeline：例如微博的时间轴，有人发布微博，用lpush加入时间轴，展示新的列表信息。

### 4.Set   集合

集合类型也是用来保存多个字符串的元素，但和列表不同的是集合中  1. 不允许有重复的元素，2.集合中的元素是无序的，不能通过索引下标获取元素，3.支持集合间的操作，可以取多个集合取交集、并集、差集。


![](../images/1289934-20190622001013515-677922001.png)

**实战场景;**

1. 标签（tag）,给用户添加标签，或者用户给消息添加标签，这样有同一标签或者类似标签的可以给推荐关注的事或者关注的人。
2. 点赞，或点踩，收藏等，可以放到set中实现

### 5.zset  有序集合

有序集合和集合有着必然的联系，保留了集合不能有重复成员的特性，区别是，有序集合中的元素是可以排序的，它给每个元素设置一个分数，作为排序的依据。

（有序集合中的元素不可以重复，但是score 分数 可以重复，就和一个班里的同学学号不能重复，但考试成绩可以相同）。


![](../images/1289934-20190622000959260-539243592.png)

**实战场景：**

1. 排行榜：有序集合经典使用场景。例如小说视频等网站需要对用户上传的小说视频做排行榜，榜单可以按照用户关注数，更新时间，字数等打分，做排行。



















### Kafka

1、Kafka 为什么那么快？

https://mp.weixin.qq.com/s/gmzIcvYvig2cDIcHJAnDRg

2、Kafka 重平衡是怎么做的？

https://mp.weixin.qq.com/s/4DFup_NziFJ1xdc4bZnVcg

3、Kafka 架构体系

https://mp.weixin.qq.com/s/QF9b9vpncLS24xMzFkanTQ


### RabbitMQ

1、RabbitMQ 高频考题

https://mp.weixin.qq.com/s/XEMuc1QJX8TAe7_fqcmJBA

2、RabbitMQ 跟 Kafka 对比下，说下对 MQ 

https://mp.weixin.qq.com/s/XtMKQr7TaOjZkXgXZWh6lg<!-- TOC -->

- [数据结构](#数据结构)
    - [222.数组中出现次数超过一半的数字-Python版](#222数组中出现次数超过一半的数字-python版)
    - [223.求100以内的质数](#223求100以内的质数)
    - [224.无重复字符的最长子串-Python实现](#224无重复字符的最长子串-python实现)
    - [225.通过2个5/6升得水壶从池塘得到3升水](#225通过2个56升得水壶从池塘得到3升水)
    - [226.什么是MD5加密，有什么特点？](#226什么是md5加密有什么特点)
    - [227.什么是对称加密和非对称加密](#227什么是对称加密和非对称加密)
    - [228.冒泡排序的思想？](#228冒泡排序的思想)
    - [229.快速排序的思想？](#229快速排序的思想)
    - [230.如何判断单向链表中是否有环？](#230如何判断单向链表中是否有环)
    - [231.你知道哪些排序算法（一般是通过问题考算法）](#231你知道哪些排序算法一般是通过问题考算法)
    - [232.斐波那契数列](#232斐波那契数列)
    - [233.如何翻转一个单链表？](#233如何翻转一个单链表)
    - [234.青蛙跳台阶问题](#234青蛙跳台阶问题)
    - [235.两数之和 Two Sum](#235两数之和-two-sum)
    - [236.搜索旋转排序数组 Search in Rotated Sorted Array](#236搜索旋转排序数组-search-in-rotated-sorted-array)
    - [237.Python实现一个Stack的数据结构](#237python实现一个stack的数据结构)
    - [238.写一个二分查找](#238写一个二分查找)
    - [239.set 用 in 时间复杂度是多少，为什么？](#239set-用-in-时间复杂度是多少为什么)
    - [240.列表中有n个正整数范围在[0，1000]，进行排序；](#240列表中有n个正整数范围在01000进行排序)
    - [241.面向对象编程中有组合和继承的方法实现新的类](#241面向对象编程中有组合和继承的方法实现新的类)

<!-- /TOC -->

## 数据结构
### 222.数组中出现次数超过一半的数字-Python版

#### 方法一

```python
def majority_element(nums):
    nums.sort()
    return nums[len(nums) // 2]
```

#### 方法二

```python
from functools import reduce


def majority_element(nums):
    return reduce(lambda n, x: (n[0], n[1] + 1) if n[0] == x else ((x, 1) if n[1] - 1 < 0 else (n[0], n[1] - 1)), nums, (None, -1))[0]
```

#### 方法三

```python
from collections import Counter


def majority_element(nums):
    return Counter(nums).most_common(1)[0][0]
```

#### 方法四

```python
from random import choice


def majority_element(nums):
    length = len(nums) // 2
    while True:
        n = choice(nums)
        if nums.count(n) > length:
            return n
```

### 223.求100以内的质数
### 224.无重复字符的最长子串-Python实现
### 225.通过2个5/6升得水壶从池塘得到3升水
### 226.什么是MD5加密，有什么特点？
### 227.什么是对称加密和非对称加密
### 228.冒泡排序的思想？
### 229.快速排序的思想？
### 230.如何判断单向链表中是否有环？
### 231.你知道哪些排序算法（一般是通过问题考算法）
### 232.斐波那契数列

**数列定义: **

f 0 = f 1 = 1
f n = f (n-1) + f (n-2)

#### 根据定义

速度很慢，另外(暴栈注意！⚠️️） `O(fibonacci n)`

```python
def fibonacci(n):
    if n == 0 or n == 1:
        return 1
    return fibonacci(n - 1) + fibonacci(n - 2)
```

#### 线性时间的

**状态/循环**

```python
def fibonacci(n):
   a, b = 1, 1
   for _ in range(n):
       a, b = b, a + b
   return a
```

**递归**

```python
def fibonacci(n):
    def fib(n_, s):
        if n_ == 0:
            return s[0]
        a, b = s
        return fib(n_ - 1, (b, a + b))
    return fib(n, (1, 1))
```

**map(zipwith)**

```python
def fibs():
    yield 1
    fibs_ = fibs()
    yield next(fibs_)
    fibs__ = fibs()
    for fib in map(lambad a, b: a + b, fibs_, fibs__):
        yield fib
        
        
def fibonacci(n):
    fibs = fibs()
    for _ in range(n):
        next(fibs)
    return next(fibs)
```

#### Logarithmic

**矩阵**

```
import numpy as np
def fibonacci(n):
    return (np.matrix([[0, 1], [1, 1]]) ** n)[1, 1]
```

**不是矩阵**

```python
def fibonacci(n):
    def fib(n):
        if n == 0:
            return (1, 1)
        elif n == 1:
            return (1, 2)
        a, b = fib(n // 2 - 1)
        c = a + b
        if n % 2 == 0:
            return (a * a + b * b, c * c - a * a)
        return (c * c - a * a, b * b + c * c)
    return fib(n)[0]
```

### 233.如何翻转一个单链表？

```python
class Node:
    def __init__(self,data=None,next=None):
        self.data = data
        self.next = next
        
def rev(link):
    pre = link
    cur = link.next
    pre.next = None
    while cur:
        temp  = cur.next
        cur.next = pre
        pre = cur
        cur = tmp
    return pre

if __name__ == '__main__':
    link = Node(1,Node(2,Node(3,Node(4,Node(5,Node(6,Node7,Node(8.Node(9))))))))
    root = rev(link)
    while root:
        print(roo.data)
        root = root.next
```



### 234.青蛙跳台阶问题

一只青蛙要跳上n层高的台阶，一次能跳一级，也可以跳两级，请问这只青蛙有多少种跳上这个n层台阶的方法？

方法1：递归

设青蛙跳上n级台阶有f(n)种方法，把这n种方法分为两大类，第一种最后一次跳了一级台阶，这类共有f(n-1)种，第二种最后一次跳了两级台阶，这种方法共有f(n-2)种，则得出递推公式f(n)=f(n-1) + f(n-2),显然f(1)=1,f(2)=2，这种方法虽然代码简单，但效率低，会超出时间上限

```python
class Solution:
    def climbStairs(self,n):
        if n ==1:
            return 1
        elif n==2:
            return 2
        else:
            return self.climbStairs(n-1) + self.climbStairs(n-2)
```

方法2：用循环来代替递归

```python
class Solution:
    def climbStairs(self,n):
        if n==1 or n==2:
            return n
        a,b,c = 1,2,3
        for i in range(3,n+1):
            c = a+b
            a = b
            b = c
        return c
```

### 235.两数之和 Two Sum



### 236.搜索旋转排序数组 Search in Rotated Sorted Array
### 237.Python实现一个Stack的数据结构
### 238.写一个二分查找
### 239.set 用 in 时间复杂度是多少，为什么？
### 240.列表中有n个正整数范围在[0，1000]，进行排序；
### 241.面向对象编程中有组合和继承的方法实现新的类
## 判断字符串中字符是否全都不同

**问题描述**

请实现一个算法，确定一个字符串的所有字符【是否全都不同】。这里我们要求【不允许使用额外的存储结构】。
给定一个string，请返回一个bool值,true代表所有字符全都不同，false代表存在相同的字符。
保证字符串中的字符为【ASCII字符】。字符串的长度小于等于【3000】。


**解题思路**

这里有几个重点，第一个是`ASCII字符`，`ASCII字符`字符一共有256个，其中128个是常用字符，可以在键盘上输入。128之后的是键盘上无法找到的。

然后是全部不同，也就是字符串中的字符没有重复的，再次，不准使用额外的储存结构，且字符串小于等于3000。

如果允许其他额外储存结构，这个题目很好做。如果不允许的话，可以使用golang内置的方式实现。

**源码参考**

通过`strings.Count` 函数判断：

```
func isUniqueString(s string) bool {
	if strings.Count(s,"") > 3000{
		return  false
	}
	for _,v := range s {
		if v > 127 {
			return false
		}
		if strings.Count(s,string(v)) > 1 {
			return false
		}
	}
	return true
}
```

通过`strings.Index`和`strings.LastIndex`函数判断：

```
func isUniqueString2(s string) bool {
	if strings.Count(s,"") > 3000{
		return  false
	}
	for k,v := range s {
		if v > 127 {
			return false
		}
		if strings.Index(s,string(v)) != k {
			return false
		}
	}
	return true
}
```

**源码解析**

以上两种方法都可以实现这个算法。

第一个方法使用的是golang内置方法`strings.Count`,可以用来判断在一个字符串中包含的另外一个字符串的数量。

第二个方法使用的是golang内置方法`strings.Index`和`strings.LastIndex`，用来判断指定字符串在另外一个字符串的索引未知，分别是第一次发现位置和最后发现位置。
## 1 红黑树

红黑树与AVL的比较：

AVL是严格平衡树，因此在增加或者删除节点的时候，根据不同情况，旋转的次数比红黑树要多；

红黑是用非严格的平衡来换取增删节点时候旋转次数的降低；

所以简单说，如果你的应用中，搜索的次数远远大于插入和删除，那么选择AVL，如果搜索，插入删除次数几乎差不多，应该选择RB。

红黑树详解: https://xieguanglei.github.io/blog/post/red-black-tree.html

教你透彻了解红黑树: https://github.com/julycoding/The-Art-Of-Programming-By-July/blob/master/ebook/zh/03.01.md<!-- TOC -->

- [系统编程](#%e7%b3%bb%e7%bb%9f%e7%bc%96%e7%a8%8b)
  - [1.select,poll和epoll](#1selectpoll%e5%92%8cepoll)
  - [2.调度算法](#2%e8%b0%83%e5%ba%a6%e7%ae%97%e6%b3%95)
  - [3.死锁](#3%e6%ad%bb%e9%94%81)
  - [4.程序编译与链接](#4%e7%a8%8b%e5%ba%8f%e7%bc%96%e8%af%91%e4%b8%8e%e9%93%be%e6%8e%a5)
  - [5.静态链接和动态链接](#5%e9%9d%99%e6%80%81%e9%93%be%e6%8e%a5%e5%92%8c%e5%8a%a8%e6%80%81%e9%93%be%e6%8e%a5)
  - [6.虚拟内存技术](#6%e8%99%9a%e6%8b%9f%e5%86%85%e5%ad%98%e6%8a%80%e6%9c%af)
  - [7.分页和分段](#7%e5%88%86%e9%a1%b5%e5%92%8c%e5%88%86%e6%ae%b5)
  - [8.页面置换算法](#8%e9%a1%b5%e9%9d%a2%e7%bd%ae%e6%8d%a2%e7%ae%97%e6%b3%95)
  - [9.边沿触发和水平触发](#9%e8%be%b9%e6%b2%bf%e8%a7%a6%e5%8f%91%e5%92%8c%e6%b0%b4%e5%b9%b3%e8%a7%a6%e5%8f%91)
  - [10.unix进程间通信方式(IPC)](#10unix%e8%bf%9b%e7%a8%8b%e9%97%b4%e9%80%9a%e4%bf%a1%e6%96%b9%e5%bc%8fipc)
  - [11.进程总结](#11%e8%bf%9b%e7%a8%8b%e6%80%bb%e7%bb%93)
  - [12.Python异步使用场景有那些？](#12python%e5%bc%82%e6%ad%a5%e4%bd%bf%e7%94%a8%e5%9c%ba%e6%99%af%e6%9c%89%e9%82%a3%e4%ba%9b)
  - [13.多线程共同操作同一个数据互斥锁同步？](#13%e5%a4%9a%e7%ba%bf%e7%a8%8b%e5%85%b1%e5%90%8c%e6%93%8d%e4%bd%9c%e5%90%8c%e4%b8%80%e4%b8%aa%e6%95%b0%e6%8d%ae%e4%ba%92%e6%96%a5%e9%94%81%e5%90%8c%e6%ad%a5)
  - [14.什么是多线程竞争？](#14%e4%bb%80%e4%b9%88%e6%98%af%e5%a4%9a%e7%ba%bf%e7%a8%8b%e7%ab%9e%e4%ba%89)
  - [15.请介绍一下Python的线程同步？](#15%e8%af%b7%e4%bb%8b%e7%bb%8d%e4%b8%80%e4%b8%8bpython%e7%9a%84%e7%ba%bf%e7%a8%8b%e5%90%8c%e6%ad%a5)
  - [16.解释以下什么是锁，有哪几种锁？](#16%e8%a7%a3%e9%87%8a%e4%bb%a5%e4%b8%8b%e4%bb%80%e4%b9%88%e6%98%af%e9%94%81%e6%9c%89%e5%93%aa%e5%87%a0%e7%a7%8d%e9%94%81)
  - [17.什么是死锁？](#17%e4%bb%80%e4%b9%88%e6%98%af%e6%ad%bb%e9%94%81)
  - [18.多线程交互访问数据，如果访问到了就不访问了？](#18%e5%a4%9a%e7%ba%bf%e7%a8%8b%e4%ba%a4%e4%ba%92%e8%ae%bf%e9%97%ae%e6%95%b0%e6%8d%ae%e5%a6%82%e6%9e%9c%e8%ae%bf%e9%97%ae%e5%88%b0%e4%ba%86%e5%b0%b1%e4%b8%8d%e8%ae%bf%e9%97%ae%e4%ba%86)
  - [19.什么是线程安全，什么是互斥锁？](#19%e4%bb%80%e4%b9%88%e6%98%af%e7%ba%bf%e7%a8%8b%e5%ae%89%e5%85%a8%e4%bb%80%e4%b9%88%e6%98%af%e4%ba%92%e6%96%a5%e9%94%81)
  - [20.说说下面几个概念：同步，异步，阻塞，非阻塞？](#20%e8%af%b4%e8%af%b4%e4%b8%8b%e9%9d%a2%e5%87%a0%e4%b8%aa%e6%a6%82%e5%bf%b5%e5%90%8c%e6%ad%a5%e5%bc%82%e6%ad%a5%e9%98%bb%e5%a1%9e%e9%9d%9e%e9%98%bb%e5%a1%9e)
  - [21.什么是僵尸进程和孤儿进程？怎么避免僵尸进程？](#21%e4%bb%80%e4%b9%88%e6%98%af%e5%83%b5%e5%b0%b8%e8%bf%9b%e7%a8%8b%e5%92%8c%e5%ad%a4%e5%84%bf%e8%bf%9b%e7%a8%8b%e6%80%8e%e4%b9%88%e9%81%bf%e5%85%8d%e5%83%b5%e5%b0%b8%e8%bf%9b%e7%a8%8b)
  - [22.python中进程与线程的使用场景？](#22python%e4%b8%ad%e8%bf%9b%e7%a8%8b%e4%b8%8e%e7%ba%bf%e7%a8%8b%e7%9a%84%e4%bd%bf%e7%94%a8%e5%9c%ba%e6%99%af)
  - [23.线程是并发还是并行，进程是并发还是并行？](#23%e7%ba%bf%e7%a8%8b%e6%98%af%e5%b9%b6%e5%8f%91%e8%bf%98%e6%98%af%e5%b9%b6%e8%a1%8c%e8%bf%9b%e7%a8%8b%e6%98%af%e5%b9%b6%e5%8f%91%e8%bf%98%e6%98%af%e5%b9%b6%e8%a1%8c)
  - [24.并行（parallel）和并发（concurrency）?](#24%e5%b9%b6%e8%a1%8cparallel%e5%92%8c%e5%b9%b6%e5%8f%91concurrency)
  - [25.IO密集型和CPU密集型区别？](#25io%e5%af%86%e9%9b%86%e5%9e%8b%e5%92%8ccpu%e5%af%86%e9%9b%86%e5%9e%8b%e5%8c%ba%e5%88%ab)
  - [26.python asyncio的原理？](#26python-asyncio%e7%9a%84%e5%8e%9f%e7%90%86)
  - [27.谈谈你对多进程，多线程，以及协程的理解，项目是否用？](#27%e8%b0%88%e8%b0%88%e4%bd%a0%e5%af%b9%e5%a4%9a%e8%bf%9b%e7%a8%8b%e5%a4%9a%e7%ba%bf%e7%a8%8b%e4%bb%a5%e5%8f%8a%e5%8d%8f%e7%a8%8b%e7%9a%84%e7%90%86%e8%a7%a3%e9%a1%b9%e7%9b%ae%e6%98%af%e5%90%a6%e7%94%a8)

<!-- /TOC -->

## 系统编程

### 1.select,poll和epoll

其实所有的I/O都是轮询的方法，只不过实现的层面不同罢了。

这个问题可能有点深入了，但相信能回答出这个问题是对I/O多路复用有很好的了解了，其中tornado使用的就是 epoll。

[selec,poll和epoll区别总结](http://www.cnblogs.com/Anker/p/3265058.html)

基本上select有3个缺点:

1. 连接数受限
2. 查找配对速度慢
3. 数据由内核拷贝到用户态

poll改善了第一个缺点，epoll改了三个缺点。

关于epoll的: http://www.cnblogs.com/my_life/articles/3968782.html

### 2.调度算法

1. 先来先服务(FCFS, First Come First Serve)
2. 短作业优先(SJF, Shortest Job First)
3. 最高优先权调度(Priority Scheduling)
4. 时间片轮转(RR, Round Robin)
5. 多级反馈队列调度(multilevel feedback queue scheduling)

常见的调度算法总结:http://www.jianshu.com/p/6edf8174c1eb

实时调度算法:

1. 最早截至时间优先 EDF
2. 最低松弛度优先 LLF

### 3.死锁

原因:

1. 竞争资源
2. 程序推进顺序不当

必要条件:

1. 互斥条件
2. 请求和保持条件
3. 不剥夺条件
4. 环路等待条件

处理死锁基本方法:

1. 预防死锁(摒弃除1以外的条件)
2. 避免死锁(银行家算法)
3. 检测死锁(资源分配图)
4. 解除死锁
   1. 剥夺资源
   2. 撤销进程

死锁概念处理策略详细介绍:https://wizardforcel.gitbooks.io/wangdaokaoyan-os/content/10.html

### 4.程序编译与链接

推荐: http://www.ruanyifeng.com/blog/2014/11/compiler.html

Bulid过程可以分解为4个步骤:预处理(Prepressing), 编译(Compilation)、汇编(Assembly)、链接(Linking)

以c语言为例:

1、预处理

预编译过程主要处理那些源文件中的以“#”开始的预编译指令，主要处理规则有：

1. 将所有的“#define”删除，并展开所用的宏定义
2. 处理所有条件预编译指令，比如“#if”、“#ifdef”、 “#elif”、“#endif”
3. 处理“#include”预编译指令，将被包含的文件插入到该编译指令的位置，注：此过程是递归进行的
4. 删除所有注释
5. 添加行号和文件名标识，以便于编译时编译器产生调试用的行号信息以及用于编译时产生编译错误或警告时可显示行号
6. 保留所有的#pragma编译器指令。

2、编译

编译过程就是把预处理完的文件进行一系列的词法分析、语法分析、语义分析及优化后生成相应的汇编代码文件。这个过程是整个程序构建的核心部分。

3、汇编

汇编器是将汇编代码转化成机器可以执行的指令，每一条汇编语句几乎都是一条机器指令。经过编译、链接、汇编输出的文件成为目标文件(Object File)

4、链接

链接的主要内容就是把各个模块之间相互引用的部分处理好，使各个模块可以正确的拼接。主要过程包块地址和空间的分配（Address and Storage Allocation）、符号决议(Symbol Resolution)和重定位(Relocation)等步骤。

### 5.静态链接和动态链接

静态链接方法：静态链接的时候，载入代码就会把程序会用到的动态代码或动态代码的地址确定下来。静态库的链接可以使用静态链接，动态链接库也可以使用这种方法链接导入库。

动态链接方法：使用这种方式的程序并不在一开始就完成动态链接，而是直到真正调用动态库代码时，载入程序才计算(被调用的那部分)动态代码的逻辑地址，然后等到某个时候，程序又需要调用另外某块动态代码时，载入程序又去计算这部分代码的逻辑地址，所以，这种方式使程序初始化时间较短，但运行期间的性能比不上静态链接的程序。

### 6.虚拟内存技术

虚拟存储器是指具有请求调入功能和置换功能，能从逻辑上对内存容量加以扩充的一种存储系统.

### 7.分页和分段

分页: 用户程序的地址空间被划分成若干固定大小的区域，称为“页”，相应地，内存空间分成若干个物理块，页和块的大小相等。可将用户程序的任一页放在内存的任一块中，实现了离散分配。

分段: 将用户程序地址空间分成若干个大小不等的段，每段可以定义一组相对完整的逻辑信息。存储分配时，以段为单位，段与段在内存中可以不相邻接，也实现了离散分配。

分页与分段的主要区别：

1. 页是信息的物理单位,分页是为了实现非连续分配,以便解决内存碎片问题,或者说分页是由于系统管理的需要.段是信息的逻辑单位,它含有一组意义相对完整的信息,分段的目的是为了更好地实现共享,满足用户的需要.
2. 页的大小固定,由系统确定,将逻辑地址划分为页号和页内地址是由机器硬件实现的.而段的长度却不固定,决定于用户所编写的程序,通常由编译程序在对源程序进行编译时根据信息的性质来划分.
3. 分页的作业地址空间是一维的.分段的地址空间是二维的.

### 8.页面置换算法

1. 最佳置换算法OPT:不可能实现
2. 先进先出FIFO
3. 最近最久未使用算法LRU:最近一段时间里最久没有使用过的页面予以置换.
4. clock算法

### 9.边沿触发和水平触发

边缘触发是指每当状态变化时发生一个 io 事件，条件触发是只要满足条件就发生一个 io 事件。

### 10.unix进程间通信方式(IPC)

1. 管道（Pipe）：管道可用于具有亲缘关系进程间的通信，允许一个进程和另一个与它有共同祖先的进程之间进行通信。
2. 命名管道（named pipe）：命名管道克服了管道没有名字的限制，因此，除具有管道所具有的功能外，它还允许无亲缘关系进程间的通信。命名管道在文件系统中有对应的文件名。命名管道通过命令mkfifo或系统调用mkfifo来创建。
3. 信号（Signal）：信号是比较复杂的通信方式，用于通知接受进程有某种事件发生，除了用于进程间通信外，进程还可以发送信号给进程本身；linux除了支持Unix早期信号语义函数sigal外，还支持语义符合Posix.1标准的信号函数sigaction（实际上，该函数是基于BSD的，BSD为了实现可靠信号机制，又能够统一对外接口，用sigaction函数重新实现了signal函数）。
4. 消息（Message）队列：消息队列是消息的链接表，包括Posix消息队列system V消息队列。有足够权限的进程可以向队列中添加消息，被赋予读权限的进程则可以读走队列中的消息。消息队列克服了信号承载信息量少，管道只能承载无格式字节流以及缓冲区大小受限等缺
5. 共享内存：使得多个进程可以访问同一块内存空间，是最快的可用IPC形式。是针对其他通信机制运行效率较低而设计的。往往与其它通信机制，如信号量结合使用，来达到进程间的同步及互斥。
6. 内存映射（mapped memory）：内存映射允许任何多个进程间通信，每一个使用该机制的进程通过把一个共享的文件映射到自己的进程地址空间来实现它。
7. 信号量（semaphore）：主要作为进程间以及同一进程不同线程之间的同步手段。
8. 套接口（Socket）：更为一般的进程间通信机制，可用于不同机器之间的进程间通信。起初是由Unix系统的BSD分支开发出来的，但现在一般可以移植到其它类Unix系统上：Linux和System V的变种都支持套接字。

### 11.进程总结

进程：程序运行在操作系统上的一个实例，就称之为进程。进程需要相应的系统资源：内存、时间片、pid。

创建进程：

首先要导入 multiprocessing 中的 Process，然后创建一个 Process 对象，创建 Process 对象时，可以传递参数：

```python
p = Process(target=XXX, args=(tuple,), kwargs={key: value})
# target = XXX 指定的任务函数，不用加(),
# args=(tuple,)kwargs={key:value}给任务函数传递的参数
```

使用start()启动进程：

```python
import os
import time
from mulitprocessing import Process


def pro_func(name, age, **kwargs):
    for i in range(5):
        print("子进程正在运行中，name=%s,age=%d,pid=%d"%(name,age,os.getpid()))
        print(kwargs)
        time.sleep(0.2)

if __name__ =="__main__":
    #创建Process对象
    p = Process(target=pro_func,args=('小明',18),kwargs={'m':20})
    #启动进程
    p.start()
    time.sleep(1)
    #1秒钟之后，立刻结束子进程
    p.terminate()
    p.join()
```

注意：进程间不共享全局变量。

进程之间的通信-Queue

在初始化Queue()对象时（例如q=Queue(),若在括号中没有指定最大可接受的消息数量，获数量为负值时，那么就代表可接受的消息数量没有上限一直到内存尽头）

Queue.qsize():返回当前队列包含的消息数量

Queue.empty():如果队列为空，返回True，反之False

Queue.full():如果队列满了，返回True,反之False

Queue.get([block[,timeout]]):获取队列中的一条消息，然后将其从队列中移除

block默认值为True。

如果block使用默认值，且没有设置timeout（单位秒)，消息队列如果为空，此时程序将被阻塞（停在读中状态），直到消息队列读到消息为止，如果设置了timeout，则会等待timeout秒，若还没读取到任何消息，则抛出“Queue.Empty"异常：

Queue.get_nowait()相当于Queue.get(False)

Queue.put(item,[block[,timeout]]):将item消息写入队列，block默认值为True;

如果block使用默认值，且没有设置timeout（单位秒），消息队列如果已经没有空间可写入，此时程序将被阻塞（停在写入状态），直到从消息队列腾出空间为止，如果设置了timeout，则会等待timeout秒，若还没空间，则抛出”Queue.Full"异常；如果block值为False，消息队列如果没有空间可写入，则会立刻抛出"Queue.Full"异常;

Queue.put_nowait(item):相当Queue.put(item,False)

进程间通信Demo:

```python
import os
import time
import random
from multiprocessing import Process.Queue

#写数据进程执行的代码：
def write(q):
    for value in ['A','B','C']:
        print("Put %s to queue...",%value)
        q.put(value)
        time.sleep(random.random())

#读数据进程执行的代码
def read(q):
    while True:
        if not q.empty():
            value = q.get(True)
            print("Get %s from queue.",%value)
            time.sleep(random.random())
        else:
            break

if __name__=='__main__':
    #父进程创建Queue，并传给各个子进程
    q = Queue()
    pw = Process(target=write,args=(q,))
    pr = Process(target=read,args=(q,))
    #启动子进程pw ，写入：
    pw.start()
    #等待pw结束
    pw.join()
    #启动子进程pr，读取：
    pr.start()
    pr.join()
    #pr 进程里是死循环，无法等待其结束，只能强行终止:
    print('')
    print('所有数据都写入并且读完')
```

进程池Pool

```python
#coding:utf-8

import os
import time
import random
from multiprocessing import Pool

def worker(msg):
    t_start = time.time()
    print("%s 开始执行，进程号为%d"%(msg,os.getpid()))
    # random.random()随机生成0-1之间的浮点数
    time.sleep(random.random()*2)
    t_stop = time.time()
    print(msg,"执行完毕，耗时%0.2f”%（t_stop-t_start))

po = Pool(3)#定义一个进程池，最大进程数3
for i in range(0,10):
    po.apply_async(worker,(i,))

print("---start----")
po.close()
po.join()
print("----end----")
```

进程池中使用Queue

如果要使用Pool创建进程，就需要使用multiprocessing.Manager()中的Queue()，而不是multiprocessing.Queue()，否则会得到如下的错误信息：

RuntimeError： Queue objects should only be shared between processs through inheritance

```python
import os
import time
import random
from multiprocessing import Manager, Pool

def reader(q):
    print("reader 启动(%s),父进程为（%s)"%(os.getpid(),os.getpid()))
    for i in range(q.qsize()):
        print("reader 从Queue获取到消息:%s"%q.get(True))

def writer(q):
    print("writer 启动（%s),父进程为(%s)"%(os.getpid(),os.getpid()))
    for i ini "itcast":
        q.put(i)
        
if __name__ == "__main__":
    print("(%s)start"%os.getpid())
    q = Manager().Queue()#使用Manager中的Queue
    po = Pool()
    po.apply_async(wrtier,(q,))
    time.sleep(1)
    po.apply_async(reader,(q,))
    po.close()
    po.join()
    print("(%s)End"%os.getpid())
```

### 12.Python异步使用场景有那些？

异步的使用场景:

1、 不涉及共享资源，获对共享资源只读，即非互斥操作

2、 没有时序上的严格关系

3、 不需要原子操作，或可以通过其他方式控制原子性

4、 常用于IO操作等耗时操作，因为比较影响客户体验和使用性能

5、 不影响主线程逻辑

### 13.多线程共同操作同一个数据互斥锁同步？

```python
import time
import threading

class MyThread(threading.Thread):
    def run(self):
        global num
        time.sleep(1)
    
        if mutex.acquire(1):
            num +=1
            msg = self.name + 'set num to ' +str(num)
            print msg
            mutex.release()

num = 0
mutex = threading.Lock()
def test():
    for i in range(5):
        t = MyThread()
        t.start()
        
if __name__=="__main__":
    test()
```
### 14.什么是多线程竞争？

线程是非独立的，同一个进程里线程是数据共享的，当各个线程访问数据资源时会出现竞争状态即：数据几乎同步会被多个线程占用，造成数据混乱，即所谓的线程不安全。

那么怎么解决多线程竞争问题？---锁

锁的好处： 确保了某段关键代码（共享数据资源）只能由一个线程从头到尾完整地执行能解决多线程资源竞争下的原子操作问题。

锁的坏处： 阻止了多线程并发执行，包含锁的某段代码实际上只能以单线程模式执行，效率就大大地下降了。

锁的致命问题: 死锁

### 15.请介绍一下Python的线程同步？

1、setDaemon(False)

当一个进程启动之后，会默认产生一个主线程，因为线程是程序执行的最小单位，当设置多线程时，主线程会创建多个子线程，在Python中，默认情况下就是setDaemon(False)，主线程执行完自己的任务以后，就退出了，此时子线程会继续执行自己的任务，直到自己的任务结束。

```python
import time
import threading 


def thread():
    time.sleep(2)
    print('---子线程结束---')

def main():
    t1 = threading.Thread(target=thread)
    t1.start()
    print('---主线程--结束')

if __name__ =='__main__':
    main()

# output
# ---主线程--结束
# ---子线程结束---
```

2、setDaemon(True)

当我们使用setDaemon(True)时，这是子线程为守护线程，主线程一旦执行结束，则全部子线程被强制终止。

```python
import time
import threading

def thread():
    time.sleep(2)
    print(’---子线程结束---')

def main():
    t1 = threading.Thread(target=thread)
    t1.setDaemon(True)#设置子线程守护主线程
    t1.start()
    print('---主线程结束---')

if __name__ =='__main__':
    main()

# 执行结果
# ---主线程结束--- #只有主线程结束，子线程来不及执行就被强制结束
```

3、join（线程同步)

join 所完成的工作就是线程同步，即主线程任务结束以后，进入堵塞状态，一直等待所有的子线程结束以后，主线程再终止。

当设置守护线程时，含义是主线程对于子线程等待timeout的时间将会杀死该子线程，最后退出程序，所以说，如果有10个子线程，全部的等待时间就是每个timeout的累加和，简单的来说，就是给每个子线程一个timeou的时间，让他去执行，时间一到，不管任务有没有完成，直接杀死。

没有设置守护线程时，主线程将会等待timeout的累加和这样的一段时间，时间一到，主线程结束，但是并没有杀死子线程，子线程依然可以继续执行，直到子线程全部结束，程序退出。

```python
import threading
import time

def thread():
    time.sleep(2)
    print('---子线程结束---')

def main():
    t1 = threading.Thread(target=thread)
    t1.setDaemon(True)
    t1.start()
    t1.join(timeout=1)  #1 线程同步，主线程堵塞1s 然后主线程结束，子线程继续执行
                        #2 如果不设置timeout参数就等子线程结束主线程再结束
                        #3 如果设置了setDaemon=True和timeout=1主线程等待1s后会强制杀死子线程，然后主线程结束
    print('---主线程结束---')

if __name__=='__main___':
    main()
```

### 16.解释以下什么是锁，有哪几种锁？

锁(Lock)是python提供的对线程控制的对象。

有互斥锁，可重入锁，死锁。

### 17.什么是死锁？

若干子线程在系统资源竞争时，都在等待对方对某部分资源解除占用状态，结果是谁也不愿先解锁，互相干等着，程序无法执行下去，这就是死锁。

GIL锁 全局解释器锁

作用： 限制多线程同时执行，保证同一时间只有一个线程执行，所以cython里的多线程其实是伪多线程！

所以python里常常使用协程技术来代替多线程，协程是一种更轻量级的线程。

进程和线程的切换时由系统决定，而协程由我们程序员自己决定，而模块gevent下切换是遇到了耗时操作时才会切换

三者的关系：进程里有线程，线程里有协程。

### 18.多线程交互访问数据，如果访问到了就不访问了？

怎么避免重读？

创建一个已访问数据列表，用于存储已经访问过的数据，并加上互斥锁，在多线程访问数据的时候先查看数据是否在已访问的列表中，若已存在就直接跳过。

### 19.什么是线程安全，什么是互斥锁？

每个对象都对应于一个可称为「互斥锁」的标记，这个标记用来保证在任一时刻，只能有一个线程访问该对象。

同一进程中的多线程之间是共享系统资源的，多个线程同时对一个对象进行操作，一个线程操作尚未结束，另一线程已经对其进行操作，导致最终结果出现错误，此时需要对被操作对象添加互斥锁，保证每个线程对该对象的操作都得到正确的结果。

### 20.说说下面几个概念：同步，异步，阻塞，非阻塞？

同步： 多个任务之间有先后顺序执行，一个执行完下个才能执行。

异步： 多个任务之间没有先后顺序，可以同时执行，有时候一个任务可能要在必要的时候获取另一个同时执行的任务的结果，这个就叫回调！

阻塞： 如果卡住了调用者，调用者不能继续往下执行，就是说调用者阻塞了。

非阻塞： 如果不会卡住，可以继续执行，就是说非阻塞的。

同步异步相对于多任务而言，阻塞非阻塞相对于代码执行而言。

### 21.什么是僵尸进程和孤儿进程？怎么避免僵尸进程？

孤儿进程： 父进程退出，子进程还在运行的这些子进程都是孤儿进程，孤儿进程将被 init 进程（进程号为1）所收养，并由 init 进程对他们完成状态收集工作。

僵尸进程： 进程使用 fork 创建子进程，如果子进程退出，而父进程并没有调用 wait 或 waitpid 获取子进程的状态信息，那么子进程的进程描述符仍然保存在系统中的这些进程是僵尸进程。

避免僵尸进程的方法：

1.fork 两次用孙子进程去完成子进程的任务

2.用 wait() 函数使父进程阻塞

3.使用信号量，在 signal handler 中调用 waitpid，这样父进程不用阻塞。

###  22.python中进程与线程的使用场景？

多进程适合在CPU密集操作（cpu操作指令比较多，如位多的的浮点运算）。

多线程适合在IO密性型操作（读写数据操作比多的的，比如爬虫）

###  23.线程是并发还是并行，进程是并发还是并行？

线程是并发，进程是并行;

进程之间互相独立，是系统分配资源的最小单位，同一个线程中的所有线程共享资源。

### 24.并行（parallel）和并发（concurrency）?

并行： 同一时刻多个任务同时在运行

不会在同一时刻同时运行，存在交替执行的情况。

实现并行的库有： multiprocessing

实现并发的库有:  threading

程序需要执行较多的读写、请求和回复任务的需要大量的IO操作，IO密集型操作使用并发更好。

CPU运算量大的程序，使用并行会更好

### 25.IO密集型和CPU密集型区别？

IO密集型： 系统运行，大部分的状况是CPU在等 I/O（硬盘/内存）的读/写

CPU密集型： 大部分时间用来做计算，逻辑判断等CPU动作的程序称之CPU密集型。

### 26.python asyncio的原理？

asyncio这个库就是使用python的yield这个可以打断保存当前函数的上下文的机制， 封装好了 selector 摆脱掉了复杂的回调关系。

### 27.谈谈你对多进程，多线程，以及协程的理解，项目是否用？

这个问题被问的概念相当之大：

进程：一个运行的程序（代码）就是一个进程，没有运行的代码叫程序，进程是系统资源分配的最小单位，进程拥有自己独立的内存空间，所有进程间数据不共享，开销大。

线程: cpu调度执行的最小单位，也叫执行路径，不能独立存在，依赖进程存在，一个进程至少有一个线程，叫主线程，而多个线程共享内存（数据共享，共享全局变量)，从而极大地提高了程序的运行效率。

协程: 是一种用户态的轻量级线程，协程的调度完全由用户控制。协程拥有自己的寄存器上下文和栈。协程调度时，将寄存器上下文和栈保存到其他地方，在切回来的时候，恢复先前保存的寄存器上下文和栈，直接操中栈则基本没有内核切换的开销，可以不加锁的访问全局变量，所以上下文的切换非常快。


1、操作系统中的阻塞、非阻塞、同步、异步、BIO、NIO、AIO 细节。

2、Linux 常见指令

3、进程间通信几种方式，每种方式的优劣性。

4、HTTP 的发展史理解优缺点，公钥、私钥理解跟加密过程。

5、操作系统内存、进程、IO、文件管理的理解，自己大致说下理解。<!-- TOC -->

- [编程语言](#编程语言)
    - [1、Python实现单例模式](#1Python实现单例模式)

- [数据结构](#数据结构)
    - [2、二维数组中的查找](#2二维数组中的查找)
    - [3、替换空格](#3替换空格)
    - [4、从尾到头打印单链表](#4从尾到头打印单链表)
    - [5、重建二叉树](#5重建二叉树)
    - [6、用两个栈实现队列](#6用两个栈实现队列)
- [算法和数据操作](算法和数据操作)
    - [7、旋转数组的最小数字](#7旋转数组的最小数字)
    - [8、斐波那契数列](#8斐波那契数列)
    - [9、二进制中1的个数](#9二进制中1的个数)


<!-- /TOC -->
## 编程语言
### 1、Python实现单例模式

#### 方法一 使用__new__实现单例模式
> 使用__new__实现单例模式，具体我对__new__的理解可以点[这里](http://www.cnblogs.com/qiaojushuang/p/7805973.html)
```python
# coding=utf-8
"""
使用__new__实现单例模式
"""


class SingleTon(object):
    _instance = {}

    def __new__(cls, *args, **kwargs):
        if cls not in cls._instance:
            cls._instance[cls] = super(SingleTon, cls).__new__(cls, *args, **kwargs)
        # print cls._instance
        return cls._instance[cls]


class MyClass(SingleTon):
    class_val = 22

    def __init__(self, val):
        self.val = val

    def obj_fun(self):
        print self.val, 'obj_fun'

    @staticmethod
    def static_fun():
        print 'staticmethod'

    @classmethod
    def class_fun(cls):
        print cls.class_val, 'classmethod'


if __name__ == '__main__':
    a = MyClass(1)
    b = MyClass(2)
    print a is b   # True
    print id(a), id(b)  # 4367665424 4367665424
    # 类型验证
    print type(a)  # <class '__main__.MyClass'>
    print type(b)  # <class '__main__.MyClass'>
    # 实例方法
    a.obj_fun()  # 2 obj_fun
    b.obj_fun()  # 2 obj_fun
    # 类方法
    MyClass.class_fun()  # 22 classmethod
    a.class_fun()  # 22 classmethod
    b.class_fun()  # 22 classmethod
    # 静态方法
    MyClass.static_fun()  # staticmethod
    a.static_fun()  # staticmethod
    b.static_fun()  # staticmethod
    # 类变量
    a.class_val = 33
    print MyClass.class_val  # 22
    print a.class_val  # 33
    print b.class_val  # 33
    # 实例变量
    print b.val  # 2
    print a.val  # 2
```

#### 方法二 使用装饰器实现单例模式
```python
from functools import wraps


def single_ton(cls):
    _instance = {}

    @wraps(cls)
    def single(*args, **kwargs):
        if cls not in _instance:
            _instance[cls] = cls(*args, **kwargs)
        return _instance[cls]
    return single


@single_ton
class SingleTon(object):
    val = 123

    def __init__(self, a):
        self.a = a

if __name__ == '__main__':
    s = SingleTon(1)
    t = SingleTon(2)
    print s is t
    print s.a, t.a
    print s.val, t.val
```

#### 方法三 使用模块实现单例模式
> 可以使用模块创建单例模式，然后在其他模块中导入该单例，这个需要所有人遵守导入规则，不然就没法实现单例了

```python
# use_module.py
class SingleTon(object):

    def __init__(self, val):
        self.val = val

single = SingleTon(2)

# test_module.py
from use_module import single

a = single
b = single
print a.val, b.val
print a is b
a.val = 233
print a.val, b.val
```

#### 方法四 使用metaclass实现单例模式
> 目前我对元类还了解不深，以后来填坑

## 数据结构

### 2、二维数组中的查找
> 题目：二维数组中，每行从左到右递增，每列从上到下递增，给出一个数，判断它是否在数组中 ([LeetCode](https://leetcode.com/problems/search-a-2d-matrix-ii/))
>
> 思路：从左下角或者右上角开始比较

```python
# coding=utf-8
# 二维数组中，每行从左到右递增，每列从上到下递增，给出一个数，判断它是否在数组中
# 从左下角（或右上角）开始遍历数组


def find_integer(matrix, num):
    """
    :param matrix: [[]]
    :param num: int
    :return: bool
    """
    if not matrix:
        return False
    rows, cols = len(matrix), len(matrix[0])
    row, col = rows - 1, 0
    while row >= 0 and col <= cols - 1:
        if matrix[row][col] == num:
            return True
        elif matrix[row][col] > num:
            row -= 1
        else:
            col += 1
    return False


if __name__ == '__main__':
    matrix = [[1, 2, 3],
              [2, 3, 6],
              [3, 6, 7]]
    num = 6
    print find_integer(matrix, num)
```

### 3、替换空格
> 题目：把字符串中的空格替换成'20%'
>
>方法1：直接使用Python字符串的内置函数

```python
>>> ' a  b  '.replace(' ', '20%')
```
> 方法2: 使用正则表达式

```python
>>> import re
>>> ret = re.compile(' ')
>>> ret.sub('20%', '  a  b  ')
```

### 4、从尾到头打印单链表
> 方法1：使用栈,可以使用列表模拟

```python
# coding=utf-8
"""
从尾到头打印单链表
思路1：使用栈
思路2：递归
"""


class ListNode(object):
    def __init__(self, x):
        self.val = x
        self.next = None


class Link(object):

    @staticmethod
    def link(values):
        head = ListNode(0)
        move = head
        try:
            for val in values:
                tmp = ListNode(val)
                move.next = tmp
                move = move.next
        except Exception as e:
            print e
        return head.next


def print_links(links):
    stack = []
    while links:
        stack.append(links.val)
        links = links.next
    while stack:
        print stack.pop()


def print_link_recursion(links):
    if links:
        print_link_recursion(links.next)
        print links.val

if __name__ == '__main__':
    head = Link.link([1, 2, 3, 4, 5, 6])
    # print_links(head)
    print_link_recursion(head)
```
> 方法2：直接递归

```python
def print_link_recursion(links):
    if links:
        print_link_recursion(links.next)
        print links.val
```

### 5、重建二叉树 [LeetCode](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)
> 要求：用前序和中序遍历结果构建二叉树，遍历结果中不包含重复值
>
> 思路：前序的第一个元素是根结点的值，在中序中找到该值，中序中该值的左边的元素是根结点的左子树，右边是右子树，然后递归的处理左边和右边
>
> **提示**：二叉树结点，以及对二叉树的各种操作，测试代码见six.py
```python
# coding=utf-8

"""
使用先序遍历和中序遍历的结果重建二叉树
"""
from collections import deque


class TreeNode(object):
    """
    二叉树结点定义
    """
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None


class Tree(object):
    """
    二叉树
    """
    def __init__(self):
        self.root = None

    def bfs(self):
        ret = []
        queue = deque([self.root])
        while queue:
            node = queue.popleft()
            if node:
                ret.append(node.val)
                queue.append(node.left)
                queue.append(node.right)
        return ret

    def pre_traversal(self):
        ret = []

        def traversal(head):
            if not head:
                return
            ret.append(head.val)
            traversal(head.left)
            traversal(head.right)

        traversal(self.root)
        return ret

    def in_traversal(self):
        ret = []

        def traversal(head):
            if not head:
                return
            traversal(head.left)
            ret.append(head.val)
            traversal(head.right)

        traversal(self.root)
        return ret

    def post_traversal(self):
        ret = []

        def traversal(head):
            if not head:
                return
            traversal(head.left)
            traversal(head.right)
            ret.append(head.val)

        traversal(self.root)
        return ret


def construct_tree(preorder=None, inorder=None):
    """
    构建二叉树
    """
    if not preorder or not inorder:
        return None
    index = inorder.index(preorder[0])
    left = inorder[0:index]
    right = inorder[index+1:]
    root = TreeNode(preorder[0])
    root.left = construct_tree(preorder[1:1+len(left)], left)
    root.right = construct_tree(preorder[-len(right):], right)
    return root


if __name__ == '__main__':
    t = Tree()
    root = construct_tree([1, 2, 4, 7, 3, 5, 6, 8], [4, 7, 2, 1, 5, 3, 8, 6])
    t.root = root
    print t.bfs()
    print t.pre_traversal()
    print t.in_traversal()
    print t.post_traversal()
```

### 6、用两个栈实现队列
> 要求：用两个栈实现队列，分别实现入队和出队操作
> 思路：一个栈负责入队，另一个负责出队，出栈为空则从入栈中导入到出栈中

```python
class MyQueue(object):
    def __init__(self):
        self.stack = []
        self.stack2 = []

    def push(self, val):
        self.stack.append(val)

    def pop(self):
        if self.stack2:
            return self.stack2.pop()
        while self.stack:
            self.stack2.append(self.stack.pop())
        return self.stack2.pop() if self.stack2 else u'队列为空'
```

## 算法和数据操作

### 7、旋转数组的最小数字
> 要求：把递增数组的前面部分数字移到队尾，求数组中的最小值，例如[3,4,5,6,1,2]
>
> 思路：使用二分法，但要考虑[1, 0, 0, 1]这种数据，只能顺序查找

```python
# coding=utf-8
"""
求旋转数组中的最小值
二分法
需要考虑[1, 0, 0, 1]这种数据，只能从头查找
"""


def find_min(nums):
    if not nums:
        return False
    length = len(nums)
    left, right = 0, length - 1
    while nums[left] >= nums[right]:
        if right - left == 1:
            return nums[right]
        mid = (left + right) / 2
        if nums[left] == nums[mid] == nums[right]:
            return min(nums)
        if nums[left] <= nums[mid]:
            left = mid
        if nums[right] >= nums[mid]:
            right = mid
    return nums[0]

if __name__ == '__main__':
    print find_min([2, 2, 4, 5, 6, 2])
    print find_min([1, 0, 0, 1])
```

### 8、斐波那契数列
> 思路：用生成器

```python
# coding=utf-8
"""
斐波那契数列
直接使用生成器， 节省内存
"""


def fib(num):
    a, b = 0, 1
    for i in xrange(num):
        yield b
        a, b = b, a + b


if __name__ == '__main__':
    print [n for n in fib(10)]
```

### 9、二进制中1的个数
> 要求：求一个整数的二进制表示中，1的个数
>
> 思路：二进制表示中，最后的那个1被减去后，低位都变为0，高位不变，按位与就可以去掉这个1


```python
# coding=utf-8
"""
求一个整数的二进制表示中，1的个数
二进制表示中，最后的那个1被减去后，低位都变为0，高位不变，按位与就可以去掉这个1
"""


def num_of_1(n):
    ret = 0
    while n:
        ret += 1
        n = n & n-1
    return ret

if __name__ == '__main__':
    print bin(100).count('1') == num_of_1(100)
```
<!-- TOC -->

- [代码的完整性](#代码的完整性)
    - [1、数值的整数次方](#1数值的整数次方)
    - [2、打印1到最大的n位数](#2打印1到最大的n位数)
    - [3、O(1)时间删除链表结点](#3O(1)时间删除链表结点)
    - [4、调整数组顺序使寄数位于偶数前面](#4调整数组顺序使寄数位于偶数前面)

- [代码的鲁棒性](#代码的鲁棒性)
    - [5、链表中倒数第k个结点](#5链表中倒数第k个结点)
    - [6、反转链表](#6反转链表)
    - [7、合并两个排序的链表](#7合并两个排序的链表)
    - [8、树的子结构](#8树的子结构)



<!-- /TOC -->
## 代码的完整性
### 1、数值的整数次方

> 要求：求一个数的整数次方
>
> 思路：需要考虑次方是正数、负数和0，基数是0
>
> 浮点数相等不能直接用==

```python
def power(base, exponent):
    if equal_zero(base) and exponent < 0:
        raise ZeroDivisionError
    ret = power_value(base, abs(exponent))
    if exponent < 0:
        return 1.0 / ret
    else:
        return ret


def equal_zero(num):
    if abs(num - 0.0) < 0.0000001:
        return True


def power_value(base, exponent):
    if exponent == 0:
        return 1
    if exponent == 1:
        return base
    ret = power_value(base, exponent >> 1)
    ret *= ret
    if exponent & 1 == 1:
        ret *= base
    return ret
```

### 2、打印1到最大的n位数
> 要求：输入n，打印出从1到最大的n位数
>
> 思路：Python中已经对大整数可以进行自动转换了，所以不需要考虑大整数溢出问题

```python
def print_max_n(n):
    for i in xrange(10 ** n):
        print i
```

### 3、O(1)时间删除链表结点
> 要求：O(1)时间删除链表结点
>
> 思路：如果有后续结点，后续结点的值前移，删除后续结点，如果没有，只能顺序查找了

```python
def delete_node(link, node):
    if node == link:  # 只有一个结点
        del node
    if node.next is None:  # node是尾结点
        while link:
            if link.next == node:
                link.next = None
            link = link.next
    else:
        node.val = node.next.val
        n_node = node.next
        node.next = n_node.next
        del n_node
```
### 4、调整数组顺序使奇数位于偶数前面
> 思路：使用两个指针，前后各一个，为了更好的扩展性，可以把判断奇偶部分抽取出来

```python
def reorder(nums, func):
    left, right = 0, len(nums) - 1
    while left < right:
        while not func(nums[left]):
            left += 1
        while func(nums[right]):
            right -= 1
        if left < right:
            nums[left], nums[right] = nums[right], nums[left]


def is_even(num):
    return (num & 1) == 0
```

## 代码的鲁棒性

### 5、链表中倒数第k个结点
> 要求：求单链表中的倒数第k个结点
>
> 思路：使用快慢指针，快的先走k-1步，需要考虑空链表以及k为0

```python
def last_kth(link, k):
    if not link or k <= 0:
        return None
    move = link
    while move and k-1 >= 0:
        move = move.next
        k -= 1
    while move:
        move = move.next
        link = link.next
    if k == 0:
        return link.val
    return None
```

### 6、反转链表
> 要求：反转链表
>
> 思路：需要考虑空链表，只有一个结点的链表

```python
def reverse_link(head):
    if not head or not head.next:
        return head
    then = head.next
    head.next = None
    last = then.next
    while then:
        then.next = head
        head = then
        then = last
        if then:
            last = then.next
    return head
```

### 7、合并两个排序的链表
> 要求：合并两个排序的链表
>
> 思路：使用递归

```python
def merge_link(head1, head2):
    if not head1:
        return head2
    if not head2:
        return head1
    if head1.val <= head2.val:
        ret = head1
        ret.next = merge_link(head1.next, head2)
    else:
        ret = head2
        ret.next = merge_link(head1, head2.next)
    return ret

```

### 8、树的子结构
> 要求：判断一棵二叉树是不是另一个的子结构
>
> 思路：使用递归

```python
def sub_tree(tree1, tree2):
    if tree1 and tree2:
        if tree1.val == tree2.val:
            return sub_tree(tree1.left, tree2.left) and sub_tree(tree1.right, tree2.right)
        else:
            return sub_tree(tree1.left, tree2) or sub_tree(tree1.right, tree2)
    if not tree1 and tree2:
        return False
    return True
```<!-- TOC -->

- [画图让抽象问题形象化](#画图让抽象问题形象化)
    - [1、二叉树的镜像](#1二叉树的镜像)
    - [2、顺时针打印矩阵](#2顺时针打印矩阵)

- [举例让抽象问题具体化](#举例让抽象问题具体化)
    - [3、包含min函数的栈](#3包含min函数的栈)
    - [4、栈的压入弹出序列](#4栈的压入弹出序列)
    - [5、从上往下打印二叉树](#5从上往下打印二叉树)
    - [6、二叉树的后序遍历序列](#6二叉树的后序遍历序列)
    - [7、二叉树中和为某一值的路径](#7二叉树中和为某一值的路径)

- [分解让复杂问题简单化](#分解让复杂问题简单化)
    - [8、复杂链表的复制](#8复杂链表的复制)
    - [9、二叉搜索树与双向链表](#9二叉搜索树与双向链表)
    - [10、字符串的排列](#10字符串的排列)


<!-- /TOC -->
## 画图让抽象问题形象化
### 1、二叉树的镜像

> 思路一：可以按层次遍历，每一层从右到左
>
> 思路二：使用递归


```python
# coding=utf-8
"""
求二叉树的镜像
思路一：按层次遍历，每一层从右到左
思路二：递归遍历
"""
from collections import deque


class TreeNode(object):
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None


class Tree(object):
    def __init__(self):
        self.root = None

    def construct_tree(self, values=None):
        if not values:
            return None
        self.root = TreeNode(values[0])
        queue = deque([self.root])
        leng = len(values)
        nums = 1
        while nums < leng:
            node = queue.popleft()
            if node:
                node.left = TreeNode(values[nums]) if values[nums] else None
                queue.append(node.left)
                if nums + 1 < leng:
                    node.right = TreeNode(values[nums + 1]) if values[nums + 1] else None
                    queue.append(node.right)
                    nums += 1
                nums += 1

    def bfs(self):
        ret = []
        queue = deque([self.root])
        while queue:
            node = queue.popleft()
            if node:
                ret.append(node.val)
                queue.append(node.left)
                queue.append(node.right)
        return ret


def mirror_bfs(root):
    ret = []
    queue = deque([root])
    while queue:
        node = queue.popleft()
        if node:
            ret.append(node.val)
            queue.append(node.right)
            queue.append(node.left)
    return ret


def mirror_pre(root):
    ret = []

    def traversal(root):
        if root:
            ret.append(root.val)
            traversal(root.right)
            traversal(root.left)
    traversal(root)
    return ret


if __name__ == '__main__':
    t = Tree()
    t.construct_tree([1, 2, 6, 4, 3, 7, 5])
    print t.bfs()
    print mirror_bfs(t.root)
    print mirror_pre(t.root)
```

### 2、顺时针打印矩阵

> 思路：每一圈的开始位置总是坐上角元素[0, 0], [1, 1]...

```python
# coding=utf-8
"""
按从外到里的顺序顺时针打印矩阵
每一圈的开始位置总是坐上角元素[0, 0], [1, 1]...
"""


def print_matrix(matrix):
    """
    :param matrix: [[]]
    """
    rows = len(matrix)
    cols = len(matrix[0]) if matrix else 0
    start = 0
    ret = []
    while start * 2 < rows and start * 2 < cols:
        print_circle(matrix, start, rows, cols, ret)
        start += 1
    print ret


def print_circle(matrix, start, rows, cols, ret):
    row = rows - start - 1  # 最后一行
    col = cols - start - 1
    # left->right
    for c in range(start, col+1):
        ret.append(matrix[start][c])
    # top->bottom
    if start < row:
        for r in range(start+1, row+1):
            ret.append(matrix[r][col])
    # right->left
    if start < row and start < col:
        for c in range(start, col)[::-1]:
            ret.append(matrix[row][c])
    # bottom->top
    if start < row and start < col:
        for r in range(start+1, row)[::-1]:
            ret.append(matrix[r][start])


if __name__ == '__main__':
    """
    mat = [[1, 2, 3],
           [5, 6, 7],
           [9, 10, 11]]
    mat = [[]]
    mat = [[1]]
    mat = [[1, 2, 3, 4]]
    mat = [[1], [2], [3], [4]]
    """
    mat = [[1, 2],
           [5, 6]]
    print_matrix(mat)
```

## 举例让抽象问题具体化

### 3、包含min函数的栈
> 要求：栈的push，pop，min操作的时间复杂度都是O(1)
>
> 思路：使用一个辅助栈保存最小值

```python
# coding=utf-8
"""
包含min函数的栈
栈的push，pop，min操作的时间复杂度都是O(1)
使用一个辅助栈保存最小值
"""


class MyStack(object):

    def __init__(self):
        self.stack = []
        self.min = []

    def push(self, val):
        self.stack.append(val)
        if self.min and self.min[-1] < val:
            self.min.append(self.min[-1])
        else:
            self.min.append(val)

    def pop(self):
        if self.stack:
            self.min.pop()
            return self.stack.pop()
        return None

    def min(self):
        return self.min[-1] if self.min else None

if __name__ == '__main__':
    s = MyStack()
    s.push(2)
    s.push(1)
    s.push(3)
    s.pop()
    s.push(2)
    print s.stack
    print s.min
```

### 4、栈的压入弹出序列
> 要求：判断给定的两个序列中，后者是不是前者的弹出序列，给定栈不包含相同值
>
> 思路：使用一个辅助栈, 如果辅助栈栈顶元素不等于出栈元素，则从入栈中找改值，直到入栈为空
> 如果最后出栈序列为空，则是入栈的弹出序列值

```python
# coding=utf-8
"""
栈的压入弹出序列，判断给定的两个序列中，后者是不是前者的弹出序列
序列中不存在相同值
使用一个辅助栈, 如果辅助栈栈顶元素不等于出栈元素，则从入栈中找改值，直到入栈为空
如果最后出栈序列为空，则是入栈的弹出序列
"""


def pop_order(push_stack, pop_stack):
    if not push_stack or not pop_stack:
        return False
    stack = []
    while pop_stack:
        pop_val = pop_stack[0]
        if stack and stack[-1] == pop_val:
            stack.pop()
            pop_stack.pop(0)
        else:
            while push_stack:
                if push_stack[0] != pop_val:
                    stack.append(push_stack.pop(0))
                else:
                    push_stack.pop(0)
                    pop_stack.pop(0)
                    break
        if not push_stack:
            while stack:
                if stack.pop() != pop_stack.pop(0):
                    return False
    if not pop_stack:
        return True
    return False


if __name__ == '__main__':
    print pop_order([1, 2, 3], [2, 3, 1])
```

### 5、从上往下打印二叉树
> 思路：广度优先搜索，按层次遍历

```python
# coding=utf-8
"""
从上往下打印二叉树
树的广度优先，按层次遍历，使用一个辅助队列就可以
"""


from collections import deque


class TreeNode(object):
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None


class Tree(object):
    def __init__(self):
        self.root = None

    def construct_tree(self, values=None):
        if not values:
            return None
        self.root = TreeNode(values[0])
        queue = deque([self.root])
        leng = len(values)
        nums = 1
        while nums < leng:
            node = queue.popleft()
            if node:
                node.left = TreeNode(values[nums]) if values[nums] else None
                queue.append(node.left)
                if nums + 1 < leng:
                    node.right = TreeNode(values[nums + 1]) if values[nums + 1] else None
                    queue.append(node.right)
                    nums += 1
                nums += 1

    def bfs(self):
        ret = []
        queue = deque([self.root])
        while queue:
            node = queue.popleft()
            if node:
                ret.append(node.val)
                queue.append(node.left)
                queue.append(node.right)
        return ret


def bfs(tree):
    if not tree:
        return None
    stack = [tree]
    ret = []
    while stack:
        node = stack.pop(0)
        ret.append(node.val)
        if node.left:
            stack.append(node.left)
        if node.right:
            stack.append(node.right)
    return ret

if __name__ == '__main__':
    t = Tree()
    t.construct_tree([1, 2, 6, 4, 3, 7, 5])
    print t.bfs()
    print bfs(t.root)
```

### 6、二叉搜索树的后序遍历序列
> 要求：判断给定的整数数组是不是二叉搜索树的后序遍历序列
>
> 整数数组中不包含重复值
>
> 整数序列的最后一个值是根结点，然后比根结点小的值是左子树，剩下的是右子树，递归左右子树

```python
# coding=utf-8
"""
判断给定的整数数组是不是二叉搜索树的后序遍历序列
整数数组中不包含重复值
整数序列的最后一个值是根结点，然后比根结点小的值是左子树，剩下的是右子树，递归左右子树
"""


def is_post_order(order):
    length = len(order)
    if length:
        root = order[-1]
        left = 0
        while order[left] < root:
            left += 1
        right = left
        while right < length - 1:
            if order[right] < root:
                return False
            right += 1
        left_ret = True if left == 0 else is_post_order(order[:left])
        right_ret = True if left == right else is_post_order(order[left:right])
        return left_ret and right_ret
    return False


if __name__ == '__main__':
    print is_post_order([9, 6, 7])
```

### 7、二叉树中和为某一值的路径
> 要求：输入一棵二叉树和一个值，求从根结点到叶结点的和等于该值的路径
>
> 深度优先搜索变形

```python
# coding=utf-8
"""
输入一棵二叉树和一个值，求从根结点到叶结点的和等于该值的路径
深度优先搜索变形
"""


from collections import deque


class TreeNode(object):
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None


class Tree(object):
    def __init__(self):
        self.root = None

    def construct_tree(self, values=None):
        if not values:
            return None
        self.root = TreeNode(values[0])
        queue = deque([self.root])
        leng = len(values)
        nums = 1
        while nums < leng:
            node = queue.popleft()
            if node:
                node.left = TreeNode(values[nums]) if values[nums] else None
                queue.append(node.left)
                if nums + 1 < leng:
                    node.right = TreeNode(values[nums + 1]) if values[nums + 1] else None
                    queue.append(node.right)
                    nums += 1
                nums += 1


def find_path(tree, num):
    ret = []
    if not tree:
        return ret
    path = [tree]
    sums = [tree.val]

    def dfs(tree):
        if tree.left:
            path.append(tree.left)
            sums.append(sums[-1]+tree.left.val)
            dfs(tree.left)
        if tree.right:
            path.append(tree.right)
            sums.append(sums[-1] + tree.right.val)
            dfs(tree.right)
        if not tree.left and not tree.right:
            if sums[-1] == num:
                ret.append([p.val for p in path])
        path.pop()
        sums.pop()

    dfs(tree)
    return ret


if __name__ == '__main__':
    t = Tree()
    t.construct_tree([1, 3, 6, 4, 3, 1, 1])
    print find_path(t.root, 8)
```

## 分解让复杂问题简单化
### 8、复杂链表的复制

> 要求：链表中除了指向后一个结点的指针之外，还有一个指针指向任意结点
>
> 分为三步完成：
> 
> 一:复制每个结点，并把新结点放在老结点后面，如1->2,复制为1->1->2->2
> 
> 二:为每个新结点设置other指针
> 
> 三:把复制后的结点链表拆开
> 
> 题目设置了复杂链表的实现，测试代码见文件twenth_six.py


```python
# coding=utf-8
"""
复杂链表的复制
链表中除了指向后一个结点的指针之外，还有一个指针指向任意结点
分为三步完成：
一复制每个结点，并把新结点放在老结点后面，如1->2,复制为1->1->2->2
二为每个新结点设置other指针
三把复制后的结点链表拆开
"""
import random


class Node(object):

    def __init__(self, val):
        self.val = val
        self.next = None
        self.other = None


class Solution(object):

    @staticmethod
    def clone_nodes(head):
        # 结点复制
        move = head
        while move:
            tmp = Node(move.val)
            tmp.next = move.next
            move.next = tmp
            move = tmp.next
        return head

    @staticmethod
    def set_nodes(head):
        # other指针设置
        move = head
        while move:
            m_next = move.next
            if move.other:
                m_next.other = move.other.next
            move = m_next.next
        return head

    @staticmethod
    def reconstruct_nodes(head):
        # 结点拆分
        ret = head.next if head else Node
        move = ret
        while head:
            head = move.next
            if head:
                move.next = head.next
                move = move.next
        return ret

    @staticmethod
    def clone_link(head):
        # 结果
        h = Solution.clone_nodes(head)
        h = Solution.set_nodes(h)
        ret = Solution.reconstruct_nodes(h)
        return ret

    @staticmethod
    def print_nodes(head):
        # 打印结点值，结点other的值，用来比较
        ret = []
        while head:
            tmp = [head.val]
            if head.other:
                tmp.append(head.other.val)
            ret.append(tmp)
            head = head.next
        print ret

    @staticmethod
    def construct_nodes(vals):
        """
        构造一个简单的复杂链表
        :param vals: list
        :return: Nodes
        """
        if not vals:
            return Node
        move = head = Node(vals.pop(0))
        nodes = [None, head]
        for v in vals:
            tmp = Node(v)
            move.next = tmp
            nodes.append(tmp)
            move = move.next
        # print [node.val for node in nodes if node]
        move = head
        while move:
            # 设置other指针为随机结点
            move.other = random.choice(nodes)
            move = move.next
        return head


if __name__ == '__main__':
    link = Solution.construct_nodes([1, 2, 3, 4, 5])
    Solution.print_nodes(link)
    test = Solution.clone_link(link)  # 复制
    Solution.print_nodes(test)
```

### 9、二叉搜索树与双向链表

> 要求: 将二叉搜索树转化成一个排序的双向链表，只调整树中结点的指向
> 
> 思路: 中序遍历，根结点的left指向左子树的最后一个(最大)值，right指向右子树的(最小)值
>
> 注意: 题目构造了一个普通二叉树用来测试，构造时按照二叉搜索树的顺序输入结点，空结点用None表示，详情见twenty_seven.py


```python
# coding=utf-8
"""
将二叉搜索树转化成一个排序的双向链表，只调整树中结点的指向，不用新结点
中序遍历，根结点的left指向左子树的最后一个(最大)值，right指向右子树的(最小)值
"""

from collections import deque


class TreeNode(object):
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None


class Tree(object):
    """
    非二叉搜索树，建树的时候values中的顺序需要注意
    之后有时间会改成二叉搜索树
    """
    def __init__(self):
        self.root = None

    def construct_tree(self, values=None):
        # 结点值不存在的话，values中用None表示
        if not values:
            return None
        self.root = TreeNode(values[0])
        queue = deque([self.root])
        leng = len(values)
        nums = 1
        while nums < leng:
            node = queue.popleft()
            if node:
                node.left = TreeNode(values[nums]) if values[nums] else None
                queue.append(node.left)
                if nums + 1 < leng:
                    node.right = TreeNode(values[nums + 1]) if values[nums + 1] else None
                    queue.append(node.right)
                    nums += 1
                nums += 1

    def bfs(self):
        ret = []
        queue = deque([self.root])
        while queue:
            node = queue.popleft()
            if node:
                ret.append(node.val)
                queue.append(node.left)
                queue.append(node.right)
        return ret


class Solution(object):

    @staticmethod
    def convert(tree):
        """结点转换"""
        if not tree:
            return None
        p_last = Solution.convert_nodes(tree, None)
        while p_last and p_last.left:  # 获取链表头结点
            p_last = p_last.left
        return p_last

    @staticmethod
    def convert_nodes(tree, last):
        if not tree:
            return None
        if tree.left:
            last = Solution.convert_nodes(tree.left, last)
        if last:
            last.right = tree
        tree.left = last
        last = tree
        if tree.right:
            last = Solution.convert_nodes(tree.right, last)
        return last

    @staticmethod
    def print_nodes(tree):
        # 正序链表打印
        ret = []
        while tree:
            tmp = []
            tmp.append(tree.left.val if tree.left else None)
            tmp.append(tree.val)
            tmp.append(tree.right.val if tree.right else None)
            ret.append(tmp)
            tree = tree.right
        print ret

if __name__ == '__main__':
    r = Tree()
    # r.construct_tree([2, 1])
    # r.construct_tree([2, None, 3])
    # r.construct_tree([2, 1, 3])
    # r.construct_tree([])
    r.construct_tree([5, 3, 6, 2, 4, None, 7, 1])
    t = Solution.convert(r.root)
    Solution.print_nodes(t)
```

### 10、字符串的排列

> 要求：求输入字符串的全排列
>
> 思路：递归完成，也可以直接使用库函数


```python
# coding=utf-8
"""
求输入字符串的全排列
递归完成，也可以直接使用库函数
"""

from itertools import permutations


def my_permutation(s):
    str_set = []
    ret = []  # 最后的结果

    def permutation(string):
        for i in string:
            str_tem = string.replace(i, '')
            str_set.append(i)
            if len(str_tem) > 0:
                permutation(str_tem)
            else:
                ret.append(''.join(str_set))
            str_set.pop()

    permutation(s)
    return ret


if __name__ == '__main__':
    s = 'abc'
    print my_permutation(s)
    print [''.join(p) for p in permutations(s)]
```
<!-- TOC -->

- [时间效率](#时间效率)
    - [1、数组中出现次数超过一半的数字](#1数组中出现次数超过一半的数字)
    - [2、最小的k个数](#2最小的k个数)
    - [3、连续子数组的最大和](#3连续子数组的最大和)
    - [4、从1到n整数中1出现的次数](#4从1到n整数中1出现的次数)
    - [5、把数组排成最小的数](#5把数组排成最小的数)

- [时间效率与空间效率的平衡](#时间效率与空间效率的平衡)
    - [6、丑数](#6丑数)
    - [7、第一个只出现一次的字符](#7第一个只出现一次的字符)
    - [8、数组中的逆序对](#8数组中的逆序对)
    - [9、两个链表的第一个公共结点](#5两个链表的第一个公共结点)



<!-- /TOC -->
## 时间效率
### 1、数组中出现次数超过一半的数字

> 思路: 使用hash，key是数字，value是出现的次数
>
> 注意: 列表的len方法的时间复杂度是O(1)
>

```python
# coding=utf-8
"""
数组中出现次数超过一半的数字
思路: 使用hash，key是数字，value是出现的次数
注意: 列表的len方法的时间复杂度是O(1)
"""


def get_more_half_num(nums):
    hashes = dict()
    length = len(nums)
    for n in nums:
        hashes[n] = hashes[n] + 1 if hashes.get(n) else 1
        if hashes[n] > length / 2:
            return n

if __name__ == '__main__':
    test = [1, 2, 1, 2, 3, 1, 1]
    print get_more_half_num(test)
```

### 2、最小的k个数
> 要求：求数组中出现次数超过一半的数字
>
> 思路: 使用heapq，该模块是一个最小堆，需要转化成最大堆，只要在入堆的时候把值取反就可以转化成最大堆(仅适用于数字)
>
> 思路二: 数组比较小的时候可以直接使用heapq的nsmallest方法

```python
# coding=utf-8
"""
求数组中最小的k个数
思路：使用heapq，该模块是一个最小堆，需要转化成最大堆，
只要在入堆的时候把值取反就可以转化成最大堆，仅适用于数字
"""
import random
import heapq


def get_least_k_nums(nums, k):
    # 数组比较小的时候可以直接使用
    return heapq.nsmallest(k, nums)


class MaxHeap(object):
    def __init__(self, k):
        self.k = k
        self.data = []

    def push(self, elem):
        elem = -elem  # 入堆的时候取反，堆顶就是最大值的相反数了
        if len(self.data) < self.k:
            heapq.heappush(self.data, elem)
        else:
            least = self.data[0]
            if elem > least:
                heapq.heapreplace(self.data, elem)

    def get_least_k_nums(self):
        return sorted([-x for x in self.data])

if __name__ == '__main__':
    test = random.sample(xrange(100000), 100)
    print get_least_k_nums(test, 4)
    h = MaxHeap(4)
    for t in test:
        h.push(t)
    print h.get_least_k_nums()
```

### 3、连续子数组的最大和
> 思路: 动态规划问题

```python
# coding=utf-8
"""
连续子数组的最大和
动态规划问题
"""


def max_sum(nums):
    ret = float("-inf")  # 负无穷
    if not nums:
        return ret
    current = 0
    for i in nums:
        if current <= 0:
            current = i
        else:
            current += i
        ret = max(ret, current)
    return ret

if __name__ == '__main__':
    test = [1, 2, -2, 3, 6, 0, -2]
    print max_sum(test)
```
### 4、从1到n整数中1出现的次数
> 要求：求从1到n整数的十进制表示中，1出现的次数
>
> 思路: 获取每个位数区间上所有数中包含1的个数，然后分别对高位分析，然后递归的处理低位数
>
> 此题中，作者的描述我没有理解，按照自己的理解写了一下，具体内容请点击[这里](http://www.cnblogs.com/qiaojushuang/p/7780445.html)

```python
# coding=utf-8
"""
求从1到n整数的十进制表示中，1出现的次数
思路：获取每个位数区间上所有数中包含1的个数，然后分别对高位分析，然后递归的处理低位数
"""


def get_digits(n):
    # 求整数n的位数
    ret = 0
    while n:
        ret += 1
        n /= 10
    return ret


def get_1_digits(n):
    """
    获取每个位数之间1的总数
    :param n: 位数
    """
    if n <= 0:
        return 0
    if n == 1:
        return 1
    current = 9 * get_1_digits(n-1) + 10 ** (n-1)
    return get_1_digits(n-1) + current


def get_1_nums(n):
    if n < 10:
        return 1 if n >= 1 else 0
    digit = get_digits(n)  # 位数
    low_nums = get_1_digits(digit-1)  # 最高位之前的1的个数
    high = int(str(n)[0])  # 最高位
    low = n - high * 10 ** (digit-1)  # 低位

    if high == 1:
        high_nums = low + 1  # 最高位上1的个数
        all_nums = high_nums
    else:
        high_nums = 10 ** (digit - 1)
        all_nums = high_nums + low_nums * (high - 1)  # 最高位大于1的话，统计每个多位数后面包含的1
    return low_nums + all_nums + get_1_nums(low)


def test_n(num):
    # 常规方法用来比较
    ret = 0
    for n in range(1, num+1):
        for s in str(n):
            if s == '1':
                ret += 1
    return ret

if __name__ == '__main__':
    test = 9923446
    import time
    t = time.clock()
    print test_n(test)
    print time.clock() - t
    t1 = time.clock()
    print get_1_nums(test)
    print time.clock() - t1
```


### 5、把数组排成最小的数
> 要求：把数组中的值拼接，找出能产生的最小的数[321,32,3]最小的数是321323
>
> 思路: Python中不需要考虑大整数，需要自己定义一个数组排序规则，直接调用库函数就可以

```python
# coding=utf-8
"""
把数组中的值拼接，找出能产生的最小的数[321,32,3]最小的数是321323
Python中不需要考虑大整数，需要自己定义一个数组排序规则，直接调用库函数就可以
"""


def cmp(a, b):
    return int(str(a)+str(b)) - int(str(b)+str(a))


def print_mini(nums):
    if not nums:
        return
    print int(''.join([str(num) for num in sorted(nums, cmp=cmp)]))


if __name__ == '__main__':
    test = []
    print_mini(test)
```

## 时间效率与空间效率的平衡

### 6、丑数 [LeetCode](https://leetcode.com/problems/ugly-number-ii/)
> 要求：只含有2、3、5因子的数是丑数，求第1500个丑数
>
> 思路: 按顺序保存已知的丑数，下一个是已知丑数中某三个数乘以2，3，5中的最小值

```python
# coding=utf-8
"""
只含有2、3、5因子的数是丑数，求第1500个丑数
按顺序保存已知的丑数，下一个是已知丑数中某三个数乘以2，3，5中的最小值
"""


class Solution(object):
    def nthUglyNumber(self, n):
        """
        :type n: int
        :rtype: int
        """
        ugly = [1]
        t2 = t3 = t5 = 0
        while len(ugly) < n:
            while ugly[t2] * 2 <= ugly[-1]:
                t2 += 1
            while ugly[t3] * 3 <= ugly[-1]:
                t3 += 1
            while ugly[t5] * 5 <= ugly[-1]:
                t5 += 1
            ugly.append(min(ugly[t2]*2, ugly[t3]*3, ugly[t5]*5))
        return ugly[-1]


if __name__ == '__main__':
    print Solution().nthUglyNumber(1500)
```

### 7、第一个只出现一次的字符
> 要求：求字符串中第一个只出现一次的字符
>
> 思路: 使用两个hash，一个记录每个字符穿线的次数，另一个记录每个字符第一次出现的位置

```python
# coding=utf-8
"""
求字符串中第一个只出现一次的字符
使用两个hash，一个记录每个字符穿线的次数，另一个记录每个字符第一次出现的位置
"""


def first_not_repeating_char(string):
    if not string:
        return -1
    count = {}
    loc = {}
    for k, s in enumerate(string):
        count[s] = count[s] + 1 if count.get(s) else 1
        loc[s] = loc[s] if loc.get(s) else k
    ret = float('inf')
    for k in loc.keys():
        if count.get(k) == 1 and loc[k] < ret:
            ret = loc[k]
    return ret

if __name__ == '__main__':
    test = 'abaccbdse'
    print first_not_repeating_char(test)
```

### 8、数组中的逆序对
> 要求：在一个数组中，前面的数字比后面的大，就是一个逆序对，求总数
>
> 思路: 归并排序,先把数组依次拆开，然后合并的时候统计逆序对数目，并排序

```python
# coding=utf-8
"""
在一个数组中，前面的数字比后面的大，就是一个逆序对，求总数
归并排序,先把数组依次拆开，然后合并的时候统计逆序对数目，并排序
"""
import copy


def get_inverse_pairs(nums):
    if not nums:
        return 0
    start, end = 0, len(nums) - 1
    tmp = copy.deepcopy(nums)
    return inverse_pairs(tmp, start, end)


def inverse_pairs(tmp, start, end):
    if start == end:  # 递归结束条件
        return 0
    mid = (end - start) / 2  # 分别对左右两边递归求值
    left = inverse_pairs(tmp, start, start+mid)
    right = inverse_pairs(tmp, start+mid+1, end)

    count = 0  # 本次逆序对数目
    l_right, r_right = start + mid, end
    t = []
    while l_right >= start and r_right >= start + mid + 1:
        if tmp[l_right] > tmp[r_right]:
            t.append(tmp[l_right])
            count += (r_right - mid - start)
            l_right -= 1
        else:
            t.append(tmp[r_right])
            r_right -= 1
    while l_right >= start:
        t.append(tmp[l_right])
        l_right -= 1
    while r_right >= start+mid+1:
        t.append(tmp[r_right])
        r_right -= 1
    tmp[start:end+1] = t[::-1]
    return count + left + right

if __name__ == '__main__':
    test = [7, 5, 6, 4, 8, 1, 2, 9]
    print get_inverse_pairs(test)
```

### 9、两个链表的第一个公共结点
> 思路: 先获取到两个链表的长度，然后长的链表先走多的几步，之后一起遍历
>
> 文件thirty_seven.py中包含了设置链表公共结点的代码，可以用来测试

```python
# coding=utf-8
"""
求两个链表的第一个公共结点
先获取到两个链表的长度，然后长的链表先走多的几步，之后一起遍历
"""


def get_first_common_node(link1, link2):
    if not link1 or not link2:
        return None
    length1 = length2 = 0
    move1, move2 = link1, link2
    while move1:  # 获取链表长度
        length1 += 1
        move1 = move1.next
    while move2:
        length2 += 1
        move2 = move2.next
    while length1 > length2:  # 长链表先走多的长度
        length1 -= 1
        link1 = link1.next
    while length2 > length1:
        length2 -= 1
        link2 = link2.next
    while link1:  # 链表一起走
        if link1 == link2:
            return link1
        link1, link2 = link1.next, link2.next
    return None


class ListNode(object):
    def __init__(self, x):
        self.val = x
        self.next = None


class Nodes(object):
    def __init__(self, values=None):
        self.nodes = self._set_link(values) if values else None

    def get_link(self):
        return self.nodes

    def get_tail(self):
        # 获取尾指针
        tail = self.nodes
        while tail.next:
            tail = tail.next
        return tail

    def print_self(self):
        Nodes.print_link(self.nodes)

    @staticmethod
    def print_link(link=None):
        count = 1
        while link:
            if count == 1:
                print link.val,
            elif count % 5 == 0:
                print '->', link.val
            else:
                print '->', link.val,
            count += 1
            link = link.next
        print

    def _set_link(self, values):
        head = ListNode(0)
        move = head
        try:
            for val in values:
                tmp = ListNode(val)
                move.next = tmp
                move = move.next
        except Exception as e:
            print e
        return head.next

if __name__ == '__main__':
    t1 = [1, 2, 3, 4]
    t2 = [5, 6, 7, 8, 12]
    t3 = [9, 10, 11]
    node1, node2, common = Nodes(t1), Nodes(t2), Nodes(t3)
    h1 = node1.get_link()
    h2 = node2.get_link()
    h3 = common.get_link()
    tail1 = node1.get_tail()
    tail2 = node2.get_tail()
    tail2.next = h3  # 设置公共链表
    tail1.next = h3
    print get_first_common_node(h1, h2)
    print h3
```
<!-- TOC -->

- [知识迁移能力](#知识迁移能力)
    - [1、数字在排序数组中出现的次数](#1数字在排序数组中出现的次数)
    - [2、二叉树的深度](#2二叉树的深度)
    - [3、数组中只出现一次的数字](#3数组中只出现一次的数字)
    - [4、和为s的两个数字VS和为s的连续正数序列](#4和为s的两个数字VS和为s的连续正数序列)
    - [5、翻转单词顺序与左旋转字符串](#5翻转单词顺序与左旋转字符串)

- [抽象建模能力](#抽象建模能力)
    - [6、n个骰子的点数](#6n个骰子的点数)
    - [7、扑克牌的顺子](#7扑克牌的顺子)
    - [8、圆圈中最后剩下的数字](#8圆圈中最后剩下的数字)

- [发散思维能力](#发散思维能力)
    - [9、求1+2...+n](#9求1+2...+n)
    - [10、不用加减乘除做加法](#10不用加减乘除做加法)
    - [11、不能被继承的类](#8不能被继承的类)

- [案例](#案例)
    - [12、把字符串转化成整数](#12把字符串转化成整数)
    - [13、树中两个结点的最低公共祖先]](#13树中两个结点的最低公共祖先])


<!-- /TOC -->
## 知识迁移能力
### 1、数字在排序数组中出现的次数

> 思路: 使用二分法分别找到数组中第一个和最后一个出现的值的坐标，然后相减

```python
# coding=utf-8
"""
统计一个数字在排序数组中出现的次数
使用二分法分别找到数组中第一个和最后一个出现的值的坐标，然后相减
"""


def get_k_counts(nums, k):
    first = get_first_k(nums, k)
    last = get_last_k(nums, k)
    if first < 0 and last < 0:
        return 0
    if first < 0 or last < 0:
        return 1
    return last - first + 1


def get_first_k(nums, k):
    left, right = 0, len(nums) - 1
    while left <= right:
        mid = (left + right) / 2
        if nums[mid] < k:
            if mid + 1 < len(nums) and nums[mid+1] == k:
                return mid + 1
            left = mid + 1
        elif nums[mid] == k:
            if mid - 1 < 0 or (mid - 1 >= 0 and nums[mid-1] < k):
                return mid
            right = mid - 1
        else:
            right = mid - 1
    return -1


def get_last_k(nums, k):
    left, right = 0, len(nums) - 1
    while left <= right:
        mid = (left + right) / 2
        if nums[mid] < k:
            left = mid + 1
        elif nums[mid] == k:
            if mid + 1 == len(nums) or (mid + 1 < len(nums) and nums[mid+1] > k):
                return mid
            left = mid + 1
        else:
            if mid - 1 >= 0 and nums[mid-1] == k:
                return mid - 1
            right = mid - 1
    return -1

if __name__ == '__main__':
    test = [1, 2, 2, 3, 3, 3, 4]
    print get_k_counts(test, 5)
```

### 2、二叉树的深度

> 思路: 分别递归的求左右子树的深度

```python
# coding=utf-8
"""
求二叉树的深度
分别递归的求左右子树的深度
"""
from collections import deque


def get_depth(tree):
    if not tree:
        return 0
    if not tree.left and not tree.right:
        return 1
    return 1 + max(get_depth(tree.left), get_depth(tree.right))


class TreeNode(object):
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None


class Tree(object):
    def __init__(self):
        self.root = None

    def construct_tree(self, values=None):
        if not values:
            return None
        self.root = TreeNode(values[0])
        queue = deque([self.root])
        leng = len(values)
        nums = 1
        while nums < leng:
            node = queue.popleft()
            if node:
                node.left = TreeNode(values[nums]) if values[nums] else None
                queue.append(node.left)
                if nums + 1 < leng:
                    node.right = TreeNode(values[nums + 1]) if values[nums + 1] else None
                    queue.append(node.right)
                    nums += 1
                nums += 1

    def bfs(self):
        ret = []
        queue = deque([self.root])
        while queue:
            node = queue.popleft()
            if node:
                ret.append(node.val)
                queue.append(node.left)
                queue.append(node.right)
        return ret


def bfs(tree):
    if not tree:
        return None
    stack = [tree]
    ret = []
    while stack:
        node = stack.pop(0)
        ret.append(node.val)
        if node.left:
            stack.append(node.left)
        if node.right:
            stack.append(node.right)
    return ret

if __name__ == '__main__':
    t = Tree()
    t.construct_tree([1, 2, 3])
    print t.bfs()
    print get_depth(t.root)
```

### 3、数组中只出现一次的数字
> 要求：数组中除了两个只出现一次的数字外，其他数字都出现了两遍
>
> 思路: 按位异或，在得到的值中找到二进制最后一个1，然后把数组按照该位是0还是1分为两组

```python
# coding=utf-8
"""
数组中除了两个只出现一次的数字外，其他数字都出现了两遍
按位异或，在得到的值中找到二进制最后一个1，然后把数组按照该位是0还是1分为两组
"""


def get_only_one_number(nums):
    if not nums:
        return None
    tmp_ret = 0
    for n in nums:  # 获取两个值的异或结果
        tmp_ret ^= n
    last_one = get_bin(tmp_ret)
    a_ret, b_ret = 0, 0
    for n in nums:
        if is_one(n, last_one):
            a_ret ^= n
        else:
            b_ret ^= n
    return [a_ret, b_ret]


def get_bin(num):  # 得到第一个1
    ret = 0
    while num & 1 == 0 and ret < 32:
        num = num >> 1
        ret += 1
    return ret


def is_one(num, t):  # 验证t位是不是1
    num = num >> t
    return num & 0x01


if __name__ == '__main__':
    test = [1, 2, 3, 4, 3, 1, -1, -1]
    print get_only_one_number(test)
```

### 4、和为s的两个数字VS和为s的连续正数序列
#### 和为s的两个数字
> 要求：输入一个递增排序的数组和一个数字s，在数组中查找两个数，使其和为s
>
> 思路: 设置头尾两个指针，和大于s，尾指针减小，否砸头指针增加

```python
# coding=utf-8
"""
输入一个递增排序的数组和一个数字s，在数组中查找两个数，使其和为s
设置头尾两个指针，和大于s，尾指针减小，否砸头指针增加
"""


def sum_to_s(nums, s):
    head, end = 0, len(nums) - 1
    while head < end:
        if nums[head] + nums[end] == s:
            return [nums[head], nums[end]]
        elif nums[head] + nums[end] > s:
            end -= 1
        else:
            head += 1
    return None

if __name__ == '__main__':
    test = [-4, 0, 1, 2, 4, 6, 8, 10, 12, 15, 18]
    s = 12
    print sum_to_s(test, s)
```

#### 和为s的连续整数序列
> 要求：输入一个正数s， 打印出所有和为s的正整数序列(至少两个数)
>
> 思路: 使用两个指针，和比s小，大指针后移，比s大，小指针后移

```python
# coding=utf-8
"""
输入一个正数s， 打印出所有和为s的正整数序列(至少两个数)
使用两个指针，和比s小，大指针后移，比s大，小指针后移
"""


def sum_to_s(s):
    a, b = 1, 2
    ret = []
    while a < s / 2 + 1:
        if sum(range(a, b+1)) == s:
            ret.append(range(a, b+1))
            a += 1
        elif sum(range(a, b+1)) < s:
            b += 1
        else:
            a += 1
    return ret

if __name__ == '__main__':
    test = 199
    print sum_to_s(test)
```

### 5、翻转单词顺序与左旋转字符串
#### 翻转单词顺序
> 要求：翻转一个英文句子中的单词顺序，标点和普通字符一样处理
>
> 思路: Python中字符串是不可变对象，不能用书中的方法，可以直接转化成列表然后转回去

```python
# coding=utf-8
"""
翻转一个英文句子中的单词顺序，标点和普通字符一样处理
Python中字符串是不可变对象，不能用书中的方法，可以直接转化成列表然后转回去
"""


def reverse_words(sentence):
    tmp = sentence.split()
    return ' '.join(tmp[::-1])  # 使用join效率更好，+每次都会创建新的字符串

if __name__ == '__main__':
    test = 'I am a engineer.'
    print reverse_words(test)

```
#### 左旋转字符串
> 思路: 把字符串的前面的若干位移到字符串的后面

```python
# coding=utf-8
"""
把字符串的前面的若干位移到字符串的后面
"""


def rotate_string(s, n):
    if not s:
        return ''
    n %= len(s)
    return s[n:] + s[:n]

if __name__ == '__main__':
    test = 'abcdefg'
    print rotate_string(test, 1)

```

## 抽象建模能力



### 6、n个骰子的点数
> 要求：求出n个骰子朝上一面之和s所有可能值出现的概率
>
> 思路：n出现的可能是前面n-1到n-6出现可能的和，设置两个数组，分别保存每一轮

```python
# coding=utf-8
"""
求出n个骰子朝上一面之和s所有可能值出现的概率
n出现的可能是前面n-1到n-6出现可能的和，设置两个数组，分别保存每一轮
"""


def get_probability(n):
    if n < 1:
        return []
    data1 = [0] + [1] * 6 + [0] * 6 * (n - 1)
    data2 = [0] + [0] * 6 * n   # 开头多一个0，方便按照习惯从1计数
    flag = 0
    for v in range(2, n+1):  # 控制次数
        if flag:
            for k in range(v, 6*v+1):
                data1[k] = sum([data2[k-j] for j in range(1, 7) if k > j])
            flag = 0
        else:
            for k in range(v, 6*v+1):
                data2[k] = sum([data1[k-j] for j in range(1, 7) if k > j])
            flag = 1
    ret = []
    total = 6 ** n
    data = data2[n:] if flag else data1[n:]
    for v in data:
        ret.append(v*1.0/total)
    print data
    return ret

if __name__ == '__main__':
    print get_probability(3)
```

### 7、扑克牌的顺子
> 要求：从扑克牌中随机抽取5张牌，判断是不是顺子，大小王可以当任意值
>
> 思路: 使用排序

```python
# coding=utf-8
"""
从扑克牌中随机抽取5张牌，判断是不是顺子，大小王可以当任意值
使用排序
"""
import random


def is_continus(nums, k):
    data = [random.choice(nums) for _ in range(k)]
    data.sort()
    print data
    zero = data.count(0)
    small, big = zero, zero + 1
    while big < k:
        if data[small] == data[big]:
            return False
        tmp = data[big] - data[small]
        if tmp > 1:
            if tmp - 1 > zero:
                return False
            else:
                zero -= tmp - 1
                small += 1
                big += 1
        else:
            small += 1
            big += 1
    return True

if __name__ == '__main__':
    test = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13,
            1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13,
            1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13,
            1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13,
            0, 0]
    t = 5
    print is_continus(test, t)
```

### 8、圆圈中最后剩下的数字
> 要求：0到n-1排成一圈，从0开始每次数m个数删除，求最后剩余的数
>
> 思路：当 n > 1 时： f(n,m) = [f(n-1, m)+m]%n,当 n = 1 时： f(n,m)=0，*关键是推导出关系表达式*


```python
# coding=utf-8
"""
0到n-1排成一圈，从0开始每次数m个数删除，求最后剩余的数
当 n > 1 时： f(n,m) = [f(n-1, m)+m]%n,当 n = 1 时： f(n,m)=0
"""


def last_num(n, m):
    ret = 0
    if n == 1:
        return 0
    for i in range(2, n+1):
        ret = (m + ret) % i
    return ret

if __name__ == '__main__':
    print last_num(3, 4)

```

## 发散思维能力


### 9、求1+2...+n
> 要求：不能使用乘除、for、while、if、else等
>
>方法一：使用range和sum
>
>方法二：使用reduce


```python
# coding=utf-8
"""
不能使用乘除、for、while、if、else等
方法一：使用range和sum
方法二：使用reduce
"""


def get_sum1(n):
    return sum(range(1, n+1))


def get_sum2(n):
    return reduce(lambda x, y: x+y, range(1, n+1))


if __name__ == '__main__':
    print get_sum1(4)
    print get_sum2(40)
```

### 10、不用加减乘除做加法
> 要求：不用加减乘除做加法
>
>方法一：使用位运算，Python中大整数会自动处理，因此对carry需要加个判断
>
>方法二：使用sum


```python
# coding=utf-8
"""
不用加减乘除做加法
方法一：使用位运算，Python中大整数会自动处理，因此对carry需要加个判断
方法二：使用sum
"""


def bit_add(n1, n2):
    carry = 1
    while carry:
        s = n1 ^ n2
        carry = 0xFFFFFFFF & ((n1 & n2) << 1)
        carry = -(~(carry - 1) & 0xFFFFFFFF) if carry > 0x7FFFFFFF else carry
        n1 = s
        n2 = carry
    return n1


def add(n1, n2):
    return sum([n1, n2])

if __name__ == '__main__':
    a = 222
    b = -199
    print bit_add(a, b)
    print add(a, b)
```

### 11、不能被继承的类
>Python中不知道怎么实现不能被继承的类。以后补充代码或者原因。

## 案例


### 12、把字符串转化成整数
> 要求：把字符串转化成整数
>
> 测试用例：正负数和0，空字符，包含其他字符
>
> 备注：使用raise抛出异常作为非法提示


```python
# coding=utf-8
"""
把字符串转化成整数
测试用例：正负数和0，空字符，包含其他字符
备注：使用raise抛出异常作为非法提示
"""


def str_to_int(string):
    if not string:  # 空字符返回异常
        raise Exception('string cannot be None', string)
    flag = 0  # 用来表示第一个字符是否为+、-
    ret = 0  # 结果
    for k, s in enumerate(string):
        if s.isdigit():  # 数字直接运算
            val = ord(s) - ord('0')
            ret = ret * 10 + val
        else:
            if not flag:
                if s == '+' and k == 0:  # 避免中间出现+、-
                    flag = 1
                elif s == '-' and k == 0:
                    flag = -1
                else:
                    raise Exception('digit is need', string)
            else:
                raise Exception('digit is need', string)
    if flag and len(string) == 1:  # 判断是不是只有+、-
        raise Exception('digit is need', string)
    return ret if flag >= 0 else -ret


if __name__ == '__main__':
    test = '12399+'
    print str_to_int(test)
```

### 13、树中两个结点的最低公共祖先
> 要求：求普通二叉树中两个结点的最低公共祖先
>
>方法一：先求出两个结点到根结点的路径，然后从路径中找出最后一个公共结点
>
>备注：文件fifty.py中包含该代码的具体测试数据


```python
# coding=utf-8
"""
求普通二叉树中两个结点的最低公共祖先
先求出两个结点到根结点的路径，然后从路径中找出最后一个公共结点
"""
from collections import deque


class TreeNode(object):
    """树结点"""
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None


class Tree(object):
    def __init__(self):
        self.root = None
        self.nodes = []  # 树中的结点

    def construct_tree(self, values=None):
        """使用列表构造二叉树"""
        if not values:
            return None
        self.root = TreeNode(values[0])
        self.nodes.append(self.root)
        queue = deque([self.root])
        leng = len(values)
        nums = 1
        while nums < leng:
            node = queue.popleft()
            if node:
                node.left = TreeNode(values[nums]) if values[nums] else None
                queue.append(node.left)
                self.nodes.append(node.left)
                if nums + 1 < leng:
                    node.right = TreeNode(values[nums + 1]) if values[nums + 1] else None
                    queue.append(node.right)
                    self.nodes.append(node.right)
                    nums += 1
                nums += 1

    def get_node(self, index):
        """根据索引返回树中的某个结点"""
        if index >= len(self.nodes):
            return None
        return self.nodes[index]


class Solution(object):

    def __init__(self, root, node1, node2):
        self.root = root  # 树的根结点
        self.node1 = node1
        self.node2 = node2  # 需要求的两个结点

    @staticmethod
    def get_path(root, node, ret):
        """获取结点的路径"""
        if not root or not node:
            return False
        ret.append(root)
        if root == node:
            return True
        left = Solution.get_path(root.left, node, ret)
        right = Solution.get_path(root.right, node, ret)
        if left or right:
            return True
        ret.pop()

    def get_last_common_node(self):
        """获取公共结点"""
        route1 = []
        route2 = []  # 保存结点路径
        ret1 = Solution.get_path(self.root, self.node1, route1)
        ret2 = Solution.get_path(self.root, self.node2, route2)
        ret = None
        if ret1 and ret2:  # 路径比较
            length = len(route1) if len(route1) <= len(route2) else len(route2)
            index = 0
            while index < length:
                if route1[index] == route2[index]:
                    ret = route1[index]
                index += 1
        return ret

if __name__ == '__main__':
    vals = [0, 1, 2, 3, 4, 5, 6, 7]
    tree = Tree()
    tree.construct_tree(vals)
    r = tree.root  # 树的根结点
    n1 = tree.get_node(7)  # 结点1
    n2 = tree.get_node(4)  # 结点2
    s = Solution(r, n1, n2)
    parent = s.get_last_common_node()  # 公共结点
    print parent, parent.val if parent else None
```## 1 台阶问题/斐波那契

一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法。

```python
fib = lambda n: n if n <= 2 else fib(n - 1) + fib(n - 2)
```

第二种记忆方法

```python
def memo(func):
    cache = {}
    def wrap(*args):
        if args not in cache:
            cache[args] = func(*args)
        return cache[args]
    return wrap


@memo
def fib(i):
    if i < 2:
        return 1
    return fib(i-1) + fib(i-2)
```

第三种方法

```python
def fib(n):
    a, b = 0, 1
    for _ in xrange(n):
        a, b = b, a + b
    return b
```

## 2 变态台阶问题

一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。

```python
fib = lambda n: n if n < 2 else 2 * fib(n - 1)
```

## 3 矩形覆盖

我们可以用`2*1`的小矩形横着或者竖着去覆盖更大的矩形。请问用n个`2*1`的小矩形无重叠地覆盖一个`2*n`的大矩形，总共有多少种方法？

> 第`2*n`个矩形的覆盖方法等于第`2*(n-1)`加上第`2*(n-2)`的方法。

```python
f = lambda n: 1 if n < 2 else f(n - 1) + f(n - 2)
```

## 4 杨氏矩阵查找

在一个m行n列二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

使用Step-wise线性搜索。

```python
def get_value(l, r, c):
    return l[r][c]

def find(l, x):
    m = len(l) - 1
    n = len(l[0]) - 1
    r = 0
    c = n
    while c >= 0 and r <= m:
        value = get_value(l, r, c)
        if value == x:
            return True
        elif value > x:
            c = c - 1
        elif value < x:
            r = r + 1
    return False
```

## 5 去除列表中的重复元素

用集合

```python
list(set(l))
```

用字典

```python
l1 = ['b','c','d','b','c','a','a']
l2 = {}.fromkeys(l1).keys()
print l2
```

用字典并保持顺序

```python
l1 = ['b','c','d','b','c','a','a']
l2 = list(set(l1))
l2.sort(key=l1.index)
print l2
```

列表推导式

```python
l1 = ['b','c','d','b','c','a','a']
l2 = []
[l2.append(i) for i in l1 if not i in l2]
```

sorted排序并且用列表推导式.

l = ['b','c','d','b','c','a','a']
[single.append(i) for i in sorted(l) if i not in single]
print single

## 6 链表成对调换

`1->2->3->4`转换成`2->1->4->3`.

```python
class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None

class Solution:
    # @param a ListNode
    # @return a ListNode
    def swapPairs(self, head):
        if head != None and head.next != None:
            next = head.next
            head.next = self.swapPairs(next.next)
            next.next = head
            return next
        return head
```

## 7 创建字典的方法

### 1 直接创建

```python
dict = {'name':'earth', 'port':'80'}
```

### 2 工厂方法

```python
items=[('name','earth'),('port','80')]
dict2=dict(items)
dict1=dict((['name','earth'],['port','80']))
```

### 3 fromkeys()方法

```python
dict1={}.fromkeys(('x','y'),-1)
dict={'x':-1,'y':-1}
dict2={}.fromkeys(('x','y'))
dict2={'x':None, 'y':None}
```

## 8 合并两个有序列表

知乎远程面试要求编程

> 尾递归

```python
def _recursion_merge_sort2(l1, l2, tmp):
    if len(l1) == 0 or len(l2) == 0:
        tmp.extend(l1)
        tmp.extend(l2)
        return tmp
    else:
        if l1[0] < l2[0]:
            tmp.append(l1[0])
            del l1[0]
        else:
            tmp.append(l2[0])
            del l2[0]
        return _recursion_merge_sort2(l1, l2, tmp)

def recursion_merge_sort2(l1, l2):
    return _recursion_merge_sort2(l1, l2, [])
```

> 循环算法

思路：

定义一个新的空列表

比较两个列表的首个元素

小的就插入到新列表里

把已经插入新列表的元素从旧列表删除

直到两个旧列表有一个为空

再把旧列表加到新列表后面

```pyhton
def loop_merge_sort(l1, l2):
    tmp = []
    while len(l1) > 0 and len(l2) > 0:
        if l1[0] < l2[0]:
            tmp.append(l1[0])
            del l1[0]
        else:
            tmp.append(l2[0])
            del l2[0]
    tmp.extend(l1)
    tmp.extend(l2)
    return tmp
```

> pop弹出

```Python
a = [1,2,3,7]
b = [3,4,5]

def merge_sortedlist(a,b):
    c = []
    while a and b:
        if a[0] >= b[0]:
            c.append(b.pop(0))
        else:
            c.append(a.pop(0))
    while a:
        c.append(a.pop(0))
    while b:
        c.append(b.pop(0))
    return c
print merge_sortedlist(a,b)
    
```

## 9 交叉链表求交点

> 其实思想可以按照从尾开始比较两个链表，如果相交，则从尾开始必然一致，只要从尾开始比较，直至不一致的地方即为交叉点，如图所示

![](http://hi.csdn.net/attachment/201106/28/0_1309244136MWLP.gif)

```python
# 使用a,b两个list来模拟链表，可以看出交叉点是 7这个节点
a = [1,2,3,7,9,1,5]
b = [4,5,7,9,1,5]

for i in range(1,min(len(a),len(b))):
    if i==1 and (a[-1] != b[-1]):
        print "No"
        break
    else:
        if a[-i] != b[-i]:
            print "交叉节点：",a[-i+1]
            break
        else:
            pass
```

> 另外一种比较正规的方法，构造链表类

```python
class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None
def node(l1, l2):
    length1, lenth2 = 0, 0
    # 求两个链表长度
    while l1.next:
        l1 = l1.next
        length1 += 1
    while l2.next:
        l2 = l2.next
        length2 += 1
    # 长的链表先走
    if length1 > lenth2:
        for _ in range(length1 - length2):
            l1 = l1.next
    else:
        for _ in range(length2 - length1):
            l2 = l2.next
    while l1 and l2:
        if l1.next == l2.next:
            return l1.next
        else:
            l1 = l1.next
            l2 = l2.next
```

修改了一下:

```python
#coding:utf-8
class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None

def node(l1, l2):
    length1, length2 = 0, 0
    # 求两个链表长度
    while l1.next:
        l1 = l1.next#尾节点
        length1 += 1
    while l2.next:
        l2 = l2.next#尾节点
        length2 += 1

    #如果相交
    if l1.next == l2.next:
        # 长的链表先走
        if length1 > length2:
            for _ in range(length1 - length2):
                l1 = l1.next
            return l1#返回交点
        else:
            for _ in range(length2 - length1):
                l2 = l2.next
            return l2#返回交点
    # 如果不相交
    else:
        return
```

思路: http://humaoli.blog.163.com/blog/static/13346651820141125102125995/

## 10 二分查找

```python
#coding:utf-8
def binary_search(list,item):
    low = 0
    high = len(list)-1
    while low<=high:
        mid = (low+high)/2
        guess = list[mid]
        if guess>item:
            high = mid-1
        elif guess<item:
            low = mid+1
        else:
            return mid
    return None
mylist = [1,3,5,7,9]
print binary_search(mylist,3)

```

参考: http://blog.csdn.net/u013205877/article/details/76411718

## 11 快排

```python
#coding:utf-8
def quicksort(list):
    if len(list)<2:
        return list
    else:
        midpivot = list[0]
        lessbeforemidpivot = [i for i in list[1:] if i<=midpivot]
        biggerafterpivot = [i for i in list[1:] if i > midpivot]
        finallylist = quicksort(lessbeforemidpivot)+[midpivot]+quicksort(biggerafterpivot)
        return finallylist

print quicksort([2,4,6,7,1,2,5])
```

> 更多排序问题可见：[数据结构与算法-排序篇-Python描述](http://blog.csdn.net/mrlevo520/article/details/77829204)

## 12 找零问题

```python
#coding:utf-8
#values是硬币的面值values = [ 25, 21, 10, 5, 1]
#valuesCounts   钱币对应的种类数
#money  找出来的总钱数
#coinsUsed   对应于目前钱币总数i所使用的硬币数目

def coinChange(values,valuesCounts,money,coinsUsed):
    #遍历出从1到money所有的钱数可能
    for cents in range(1,money+1):
        minCoins = cents
        #把所有的硬币面值遍历出来和钱数做对比
        for kind in range(0,valuesCounts):
            if (values[kind] <= cents):
                temp = coinsUsed[cents - values[kind]] +1
                if (temp < minCoins):
                    minCoins = temp
        coinsUsed[cents] = minCoins
        print ('面值:{0}的最少硬币使用数为:{1}'.format(cents, coinsUsed[cents]))

```

思路: http://blog.csdn.net/wdxin1322/article/details/9501163

方法: http://www.cnblogs.com/ChenxofHit/archive/2011/03/18/1988431.html

## 13 广度遍历和深度遍历二叉树

给定一个数组，构建二叉树，并且按层次打印这个二叉树

## 14 二叉树节点

```python
class Node(object):
    def __init__(self, data, left=None, right=None):
        self.data = data
        self.left = left
        self.right = right

tree = Node(1, Node(3, Node(7, Node(0)), Node(6)), Node(2, Node(5), Node(4)))

```

## 15 层次遍历

```python
def lookup(root):
    row = [root]
    while row:
        print(row)
        row = [kid for item in row for kid in (item.left, item.right) if kid]

```

## 16 深度遍历

```python
def deep(root):
    if not root:
        return
    print root.data
    deep(root.left)
    deep(root.right)

if __name__ == '__main__':
    lookup(tree)
    deep(tree)
```

## 17 前中后序遍历

深度遍历改变顺序就OK了

```python
#coding:utf-8
#二叉树的遍历
#简单的二叉树节点类
class Node(object):
    def __init__(self,value,left,right):
        self.value = value
        self.left = left
        self.right = right

#中序遍历:遍历左子树,访问当前节点,遍历右子树

def mid_travelsal(root):
    if root.left is not None:
        mid_travelsal(root.left)
    #访问当前节点
    print(root.value)
    if root.right is not None:
        mid_travelsal(root.right)

#前序遍历:访问当前节点,遍历左子树,遍历右子树

def pre_travelsal(root):
    print (root.value)
    if root.left is not None:
        pre_travelsal(root.left)
    if root.right is not None:
        pre_travelsal(root.right)

#后续遍历:遍历左子树,遍历右子树,访问当前节点

def post_trvelsal(root):
    if root.left is not None:
        post_trvelsal(root.left)
    if root.right is not None:
        post_trvelsal(root.right)
    print (root.value)

```

## 18 求最大树深

```python
def maxDepth(root):
        if not root:
            return 0
        return max(maxDepth(root.left), maxDepth(root.right)) + 1
```

## 19 求两棵树是否相同

```python
def isSameTree(p, q):
    if p == None and q == None:
        return True
    elif p and q :
        return p.val == q.val and isSameTree(p.left,q.left) and isSameTree(p.right,q.right)
    else :
        return False
```

## 20 前序中序求后序

推荐: http://blog.csdn.net/hinyunsin/article/details/6315502

```python
def rebuild(pre, center):
    if not pre:
        return
    cur = Node(pre[0])
    index = center.index(pre[0])
    cur.left = rebuild(pre[1:index + 1], center[:index])
    cur.right = rebuild(pre[index + 1:], center[index + 1:])
    return cur

def deep(root):
    if not root:
        return
    deep(root.left)
    deep(root.right)
    print root.data
```

## 21 单链表逆置

```python
class Node(object):
    def __init__(self, data=None, next=None):
        self.data = data
        self.next = next

link = Node(1, Node(2, Node(3, Node(4, Node(5, Node(6, Node(7, Node(8, Node(9)))))))))

def rev(link):
    pre = link
    cur = link.next
    pre.next = None
    while cur:
        tmp = cur.next
        cur.next = pre
        pre = cur
        cur = tmp
    return pre

root = rev(link)
while root:
    print root.data
    root = root.next
```

思路: http://blog.csdn.net/feliciafay/article/details/6841115

方法: http://www.xuebuyuan.com/2066385.html?mobile=1

## 22 两个字符串是否是变位词

```python
class Anagram:
    """
    @:param s1: The first string
    @:param s2: The second string
    @:return true or false
    """
    def Solution1(s1,s2):
        alist = list(s2)

        pos1 = 0
        stillOK = True

        while pos1 < len(s1) and stillOK:
            pos2 = 0
            found = False
            while pos2 < len(alist) and not found:
                if s1[pos1] == alist[pos2]:
                    found = True
                else:
                    pos2 = pos2 + 1

            if found:
                alist[pos2] = None
            else:
                stillOK = False

            pos1 = pos1 + 1

        return stillOK

    print(Solution1('abcd','dcba'))

    def Solution2(s1,s2):
        alist1 = list(s1)
        alist2 = list(s2)

        alist1.sort()
        alist2.sort()


        pos = 0
        matches = True

        while pos < len(s1) and matches:
            if alist1[pos] == alist2[pos]:
                pos = pos + 1
            else:
                matches = False

        return matches

    print(Solution2('abcde','edcbg'))

    def Solution3(s1,s2):
        c1 = [0]*26
        c2 = [0]*26

        for i in range(len(s1)):
            pos = ord(s1[i])-ord('a')
            c1[pos] = c1[pos] + 1

        for i in range(len(s2)):
            pos = ord(s2[i])-ord('a')
            c2[pos] = c2[pos] + 1

        j = 0
        stillOK = True
        while j<26 and stillOK:
            if c1[j] == c2[j]:
                j = j + 1
            else:
                stillOK = False

        return stillOK

    print(Solution3('apple','pleap'))
```



## 23 动态规划问题

> 可参考：[动态规划(DP)的整理-Python描述](http://blog.csdn.net/mrlevo520/article/details/75676160)

<!-- TOC -->

- [网络编程](#%e7%bd%91%e7%bb%9c%e7%bc%96%e7%a8%8b)
  - [1.三次握手和四次挥手](#1%e4%b8%89%e6%ac%a1%e6%8f%a1%e6%89%8b%e5%92%8c%e5%9b%9b%e6%ac%a1%e6%8c%a5%e6%89%8b)
  - [2.ARP协议](#2arp%e5%8d%8f%e8%ae%ae)
  - [3.urllib和urllib2的区别](#3urllib%e5%92%8curllib2%e7%9a%84%e5%8c%ba%e5%88%ab)
  - [4.Post和Get](#4post%e5%92%8cget)
  - [5.Cookie和Session](#5cookie%e5%92%8csession)
  - [6.apache和nginx的区别](#6apache%e5%92%8cnginx%e7%9a%84%e5%8c%ba%e5%88%ab)
  - [7.网站用户密码保存](#7%e7%bd%91%e7%ab%99%e7%94%a8%e6%88%b7%e5%af%86%e7%a0%81%e4%bf%9d%e5%ad%98)
  - [8.HTTP和HTTPS](#8http%e5%92%8chttps)
  - [9.CSRF和XSS](#9csrf%e5%92%8cxss)
  - [10.幂等 Idempotence](#10%e5%b9%82%e7%ad%89-idempotence)
  - [11.RESTful架构(SOAP,RPC)](#11restful%e6%9e%b6%e6%9e%84soaprpc)
  - [12.SOAP](#12soap)
  - [13.RPC](#13rpc)
  - [14.CGI和WSGI](#14cgi%e5%92%8cwsgi)
  - [15.中间人攻击](#15%e4%b8%ad%e9%97%b4%e4%ba%ba%e6%94%bb%e5%87%bb)
  - [16.c10k问题](#16c10k%e9%97%ae%e9%a2%98)
  - [17.socket](#17socket)
  - [18.浏览器缓存](#18%e6%b5%8f%e8%a7%88%e5%99%a8%e7%bc%93%e5%ad%98)
  - [19.HTTP1.0和HTTP1.1](#19http10%e5%92%8chttp11)
  - [20.Ajax](#20ajax)
  - [21.怎么实现强行关闭客户端和服务器之间的连接?](#21%e6%80%8e%e4%b9%88%e5%ae%9e%e7%8e%b0%e5%bc%ba%e8%a1%8c%e5%85%b3%e9%97%ad%e5%ae%a2%e6%88%b7%e7%ab%af%e5%92%8c%e6%9c%8d%e5%8a%a1%e5%99%a8%e4%b9%8b%e9%97%b4%e7%9a%84%e8%bf%9e%e6%8e%a5)
  - [22.简述TCP和UDP的区别以及优缺点?](#22%e7%ae%80%e8%bf%b0tcp%e5%92%8cudp%e7%9a%84%e5%8c%ba%e5%88%ab%e4%bb%a5%e5%8f%8a%e4%bc%98%e7%bc%ba%e7%82%b9)
  - [23.简述浏览器通过WSGI请求动态资源的过程?](#23%e7%ae%80%e8%bf%b0%e6%b5%8f%e8%a7%88%e5%99%a8%e9%80%9a%e8%bf%87wsgi%e8%af%b7%e6%b1%82%e5%8a%a8%e6%80%81%e8%b5%84%e6%ba%90%e7%9a%84%e8%bf%87%e7%a8%8b)
  - [24.描述用浏览器访问www.baidu.com的过程](#24%e6%8f%8f%e8%bf%b0%e7%94%a8%e6%b5%8f%e8%a7%88%e5%99%a8%e8%ae%bf%e9%97%aewwwbaiducom%e7%9a%84%e8%bf%87%e7%a8%8b)
  - [25.列出你知道的HTTP协议的状态码，说出表示什么意思？](#25%e5%88%97%e5%87%ba%e4%bd%a0%e7%9f%a5%e9%81%93%e7%9a%84http%e5%8d%8f%e8%ae%ae%e7%9a%84%e7%8a%b6%e6%80%81%e7%a0%81%e8%af%b4%e5%87%ba%e8%a1%a8%e7%a4%ba%e4%bb%80%e4%b9%88%e6%84%8f%e6%80%9d)
  - [26.说一下什么是tcp的2MSL？](#26%e8%af%b4%e4%b8%80%e4%b8%8b%e4%bb%80%e4%b9%88%e6%98%aftcp%e7%9a%842msl)
  - [27.为什么客户端在TIME-WAIT状态必须等待2MSL的时间？](#27%e4%b8%ba%e4%bb%80%e4%b9%88%e5%ae%a2%e6%88%b7%e7%ab%af%e5%9c%a8time-wait%e7%8a%b6%e6%80%81%e5%bf%85%e9%a1%bb%e7%ad%89%e5%be%852msl%e7%9a%84%e6%97%b6%e9%97%b4)
  - [28.谈一下HTTP协议以及协议头部中表示数据类型的字段？](#28%e8%b0%88%e4%b8%80%e4%b8%8bhttp%e5%8d%8f%e8%ae%ae%e4%bb%a5%e5%8f%8a%e5%8d%8f%e8%ae%ae%e5%a4%b4%e9%83%a8%e4%b8%ad%e8%a1%a8%e7%a4%ba%e6%95%b0%e6%8d%ae%e7%b1%bb%e5%9e%8b%e7%9a%84%e5%ad%97%e6%ae%b5)
  - [29.使用Socket套接字需要传入哪些参数 ？](#29%e4%bd%bf%e7%94%a8socket%e5%a5%97%e6%8e%a5%e5%ad%97%e9%9c%80%e8%a6%81%e4%bc%a0%e5%85%a5%e5%93%aa%e4%ba%9b%e5%8f%82%e6%95%b0)
  - [30.HTTP常见请求头？](#30http%e5%b8%b8%e8%a7%81%e8%af%b7%e6%b1%82%e5%a4%b4)
  - [31.七层模型？](#31%e4%b8%83%e5%b1%82%e6%a8%a1%e5%9e%8b)
  - [32.url的形式？](#32url%e7%9a%84%e5%bd%a2%e5%bc%8f)

<!-- /TOC -->

## 网络编程
### 1.三次握手和四次挥手

三次握手：

1. 客户端通过向服务器端发送一个SYN来创建一个主动打开，作为三次握手的一部分。客户端把这段连接的序号设定为随机数 A。
2. 服务器端应当为一个合法的SYN回送一个SYN/ACK。ACK 的确认码应为 A+1，SYN/ACK 包本身又有一个随机序号 B。
3. 最后，客户端再发送一个ACK。当服务端受到这个ACK的时候，就完成了三路握手，并进入了连接创建状态。此时包序号被设定为收到的确认号 A+1，而响应则为 B+1。

四次挥手：

注意: 中断连接端可以是客户端，也可以是服务器端。下面仅以客户端断开连接举例，反之亦然：

1. 客户端发送一个数据分段, 其中的 FIN 标记设置为1. 客户端进入 FIN-WAIT 状态. 该状态下客户端只接收数据, 不再发送数据.
2. 服务器接收到带有 FIN = 1 的数据分段, 发送带有 ACK = 1 的剩余数据分段, 确认收到客户端发来的 FIN 信息.
3. 服务器等到所有数据传输结束, 向客户端发送一个带有 FIN = 1 的数据分段, 并进入 CLOSE-WAIT 状态, 等待客户端发来带有 ACK = 1 的确认报文.
4. 客户端收到服务器发来带有 FIN = 1 的报文, 返回 ACK = 1 的报文确认, 为了防止服务器端未收到需要重发, 进入 TIME-WAIT 状态. 服务器接收到报文后关闭连接. 客户端等待 2MSL 后未收到回复, 则认为服务器成功关闭, 客户端关闭连接.

图解: http://blog.csdn.net/whuslei/article/details/6667471

### 2.ARP协议

地址解析协议(Address Resolution Protocol)，其基本功能为透过目标设备的IP地址，查询目标的MAC地址，以保证通信的顺利进行。它是IPv4网络层必不可少的协议，不过在IPv6中已不再适用，并被邻居发现协议（NDP）所替代。

### 3.urllib和urllib2的区别

这个面试官确实问过，当时答的urllib2可以Post而urllib不可以。

1. urllib提供urlencode方法用来GET查询字符串的产生，而urllib2没有。这是为何urllib常和urllib2一起使用的原因。
2. urllib2可以接受一个Request类的实例来设置URL请求的headers，urllib仅可以接受URL。这意味着，你不可以伪装你的User Agent字符串等。

### 4.Post和Get

[GET和POST有什么区别？及为什么网上的多数答案都是错的](http://www.cnblogs.com/nankezhishi/archive/2012/06/09/getandpost.html)

[知乎回答](https://www.zhihu.com/question/31640769?rf=37401322)

get: [RFC 2616 - Hypertext Transfer Protocol -- HTTP/1.1](http://tools.ietf.org/html/rfc2616#section-9.3)
post: [RFC 2616 - Hypertext Transfer Protocol -- HTTP/1.1](http://tools.ietf.org/html/rfc2616#section-9.5)

### 5.Cookie和Session

|          | Cookie                                               | Session  |
| :------- | :--------------------------------------------------- | :------- |
| 储存位置 | 客户端                                               | 服务器端 |
| 目的     | 跟踪会话，也可以保存用户偏好设置或者保存用户名密码等 | 跟踪会话 |
| 安全性   | 不安全                                               | 安全     |

session技术是要使用到cookie的，之所以出现session技术，主要是为了安全。

### 6.apache和nginx的区别

nginx 相对 apache 的优点：

- 轻量级，同样起 web 服务，比 apache 占用更少的内存及资源
- 抗并发，nginx 处理请求是异步非阻塞的，支持更多的并发连接，而 apache 则是阻塞型的，在高并发下 nginx 能保持低资源低消耗高性能
- 配置简洁
- 高度模块化的设计，编写模块相对简单
- 社区活跃

apache 相对 nginx 的优点：

- rewrite ，比 nginx 的 rewrite 强大
- 模块超多，基本想到的都可以找到
- 少 bug ，nginx 的 bug 相对较多
- 超稳定

### 7.网站用户密码保存

1. 明文保存
2. 明文hash后保存,如md5
3. MD5+Salt方式,这个salt可以随机
4. 知乎使用了Bcrypy(好像)加密

### 8.HTTP和HTTPS

| 状态码         | 定义                            |
| :------------- | :------------------------------ |
| 1xx 报告       | 接收到请求，继续进程            |
| 2xx 成功       | 步骤成功接收，被理解，并被接受  |
| 3xx 重定向     | 为了完成请求,必须采取进一步措施 |
| 4xx 客户端出错 | 请求包括错的顺序或不能完成      |
| 5xx 服务器出错 | 服务器无法完成显然有效的请求    |

403: Forbidden
404: Not Found

HTTPS握手,对称加密,非对称加密,TLS/SSL,RSA

### 9.CSRF和XSS

- CSRF(Cross-site request forgery)跨站请求伪造
- XSS(Cross Site Scripting)跨站脚本攻击

CSRF 重点在请求，XSS 重点在脚本。

### 10.幂等 Idempotence

HTTP方法的幂等性是指一次和多次请求某一个资源应该具有同样的**副作用**。(注意是副作用)

`GET http://www.bank.com/account/123456`，不会改变资源的状态，不论调用一次还是N次都没有副作用。请注意，这里强调的是一次和N次具有相同的副作用，而不是每次GET的结果相同。`GET http://www.news.com/latest-news`这个HTTP请求可能会每次得到不同的结果，但它本身并没有产生任何副作用，因而是满足幂等性的。

DELETE方法用于删除资源，有副作用，但它应该满足幂等性。比如：`DELETE http://www.forum.com/article/4231`，调用一次和N次对系统产生的副作用是相同的，即删掉id为4231的帖子；因此，调用者可以多次调用或刷新页面而不必担心引起错误。

POST所对应的URI并非创建的资源本身，而是资源的接收者。比如：`POST http://www.forum.com/articles`的语义是在`http://www.forum.com/articles`下创建一篇帖子，HTTP响应中应包含帖子的创建状态以及帖子的URI。两次相同的POST请求会在服务器端创建两份资源，它们具有不同的URI；所以，POST方法不具备幂等性。

PUT所对应的URI是要创建或更新的资源本身。比如：`PUT http://www.forum/articles/4231`的语义是创建或更新ID为4231的帖子。对同一URI进行多次PUT的副作用和一次PUT是相同的；因此，PUT方法具有幂等性。

### 11.RESTful架构(SOAP,RPC)

推荐: http://www.ruanyifeng.com/blog/2011/09/restful.html

### 12.SOAP

SOAP（原为Simple Object Access Protocol的首字母缩写，即简单对象访问协议）是交换数据的一种协议规范，使用在计算机网络Web服务（web service）中，交换带结构信息。SOAP为了简化网页服务器（Web Server）从XML数据库中提取数据时，节省去格式化页面时间，以及不同应用程序之间按照HTTP通信协议，遵从XML格式执行资料互换，使其抽象于语言实现、平台和硬件。

### 13.RPC

RPC（Remote Procedure Call Protocol）——远程过程调用协议，它是一种通过网络从远程计算机程序上请求服务，而不需要了解底层网络技术的协议。RPC协议假定某些传输协议的存在，如TCP或UDP，为通信程序之间携带信息数据。在OSI网络通信模型中，RPC跨越了传输层和应用层。RPC使得开发包括网络分布式多程序在内的应用程序更加容易。

总结:服务提供的两大流派.传统意义以方法调用为导向通称RPC。为了企业SOA,若干厂商联合推出webservice,制定了wsdl接口定义,传输soap.当互联网时代,臃肿SOA被简化为http+xml/json.但是简化出现各种混乱。以资源为导向,任何操作无非是对资源的增删改查，于是统一的REST出现了.

进化的顺序: RPC -> SOAP -> RESTful

### 14.CGI和WSGI

CGI是通用网关接口，是连接web服务器和应用程序的接口，用户通过CGI来获取动态数据或文件等。
CGI程序是一个独立的程序，它可以用几乎所有语言来写，包括perl，c，lua，python等等。

WSGI, Web Server Gateway Interface，是Python应用程序或框架和Web服务器之间的一种接口，WSGI的其中一个目的就是让用户可以用统一的语言(Python)编写前后端。

官方说明：[PEP-3333](https://www.python.org/dev/peps/pep-3333/)

### 15.中间人攻击

中间人攻击（Man-in-the-middle attack，通常缩写为MITM）是指攻击者与通讯的两端分别创建独立的联系，并交换其所收到的数据，使通讯的两端认为他们正在通过一个私密的连接与对方直接对话，但事实上整个会话都被攻击者完全控制。

### 16.c10k问题

所谓c10k问题，指的是服务器同时支持成千上万个客户端的问题，也就是concurrent 10 000 connection（这也是c10k这个名字的由来）。

推荐: https://my.oschina.net/xianggao/blog/664275

### 17.socket

推荐: http://www.360doc.com/content/11/0609/15/5482098_122692444.shtml

Socket=Ip address+ TCP/UDP + port

### 18.浏览器缓存

推荐: http://www.cnblogs.com/skynet/archive/2012/11/28/2792503.html

### 19.HTTP1.0和HTTP1.1

推荐: http://blog.csdn.net/elifefly/article/details/3964766

1. 请求头Host字段,一个服务器多个网站
2. 长链接
3. 文件断点续传
4. 身份认证,状态管理,Cache缓存

HTTP请求8种方法介绍 

HTTP/1.1协议中共定义了8种HTTP请求方法，HTTP请求方法也被叫做“请求动作”，不同的方法规定了不同的操作指定的资源方式。服务端也会根据不同的请求方法做不同的响应。

GET

GET请求会显示请求指定的资源。一般来说GET方法应该只用于数据的读取，而不应当用于会产生副作用的非幂等的操作中。

GET会方法请求指定的页面信息，并返回响应主体，GET被认为是不安全的方法，因为GET方法会被网络蜘蛛等任意的访问。

HEAD

HEAD方法与GET方法一样，都是向服务器发出指定资源的请求。但是，服务器在响应HEAD请求时不会回传资源的内容部分，即：响应主体。这样，我们可以不传输全部内容的情况下，就可以获取服务器的响应头信息。HEAD方法常被用于客户端查看服务器的性能。

POST

POST请求会 向指定资源提交数据，请求服务器进行处理，如：表单数据提交、文件上传等，请求数据会被包含在请求体中。POST方法是非幂等的方法，因为这个请求可能会创建新的资源或/和修改现有资源。

PUT

PUT请求会身向指定资源位置上传其最新内容，PUT方法是幂等的方法。通过该方法客户端可以将指定资源的最新数据传送给服务器取代指定的资源的内容。

DELETE

DELETE请求用于请求服务器删除所请求URI（统一资源标识符，Uniform Resource Identifier）所标识的资源。DELETE请求后指定资源会被删除，DELETE方法也是幂等的。

CONNECT

CONNECT方法是HTTP/1.1协议预留的，能够将连接改为管道方式的代理服务器。通常用于SSL加密服务器的链接与非加密的HTTP代理服务器的通信。

OPTIONS

OPTIONS请求与HEAD类似，一般也是用于客户端查看服务器的性能。 这个方法会请求服务器返回该资源所支持的所有HTTP请求方法，该方法会用’*’来代替资源名称，向服务器发送OPTIONS请求，可以测试服务器功能是否正常。JavaScript的XMLHttpRequest对象进行CORS跨域资源共享时，就是使用OPTIONS方法发送嗅探请求，以判断是否有对指定资源的访问权限。 允许

TRACE

TRACE请求服务器回显其收到的请求信息，该方法主要用于HTTP请求的测试或诊断。

HTTP/1.1之后增加的方法

在HTTP/1.1标准制定之后，又陆续扩展了一些方法。其中使用中较多的是 PATCH 方法：

PATCH

PATCH方法出现的较晚，它在2010年的RFC 5789标准中被定义。PATCH请求与PUT请求类似，同样用于资源的更新。二者有以下两点不同：

1、PATCH一般用于资源的部分更新，而PUT一般用于资源的整体更新。 

2、当资源不存在时，PATCH会创建一个新的资源，而PUT只会对已在资源进行更新。

### 20.Ajax

AJAX,Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）, 是与在不重新加载整个页面的情况下，与服务器交换数据并更新部分网页的技术。

### 21.怎么实现强行关闭客户端和服务器之间的连接?
### 22.简述TCP和UDP的区别以及优缺点?

推荐：[简述TCP和UDP的区别以及优缺点](https://github.com/yongxinz/tech-blog/blob/master/TCP%E5%92%8CUDP%E7%9A%84%E4%B8%80%E4%BA%9B%E4%BC%98%E7%BC%BA%E7%82%B9%E5%92%8C%E5%8C%BA%E5%88%AB.md)

### 23.简述浏览器通过WSGI请求动态资源的过程?

浏览器发送的请求被Nginx监听到，Nginx根据请求的URL的PATH或者后缀把请求静态资源的分发到静态资源的目录，别的请求根据配置好的转发到相应端口。

实现了WSGI的程序会监听某个端口，监听到Nginx转发过来的请求接收后(一般用socket的recv来接收HTTP的报文)以后把请求的报文封装成`environ`的字典对象，然后再提供一个`start_response`的方法。把这两个对象当成参数传入某个方法比如`wsgi_app(environ, start_response)`或者实现了`__call__(self, environ, start_response)`方法的某个实例。这个实例再调用`start_response`返回给实现了WSGI的中间件，再由中间件返回给Nginx。

### 24.描述用浏览器访问www.baidu.com的过程

推荐：https://www.zhihu.com/question/34873227/answer/518086565

1、客户端浏览器通过DNS解析到www.baidu.com的IP地址202.108.22.5，通过这个IP地址找到客户端到服务器的路径。客户端浏览器发起一个HTTP会话到202.108.22.5，然后通过TCP进行封装数据包，输入到网络层。

2、在客户端的传输层，把HTTP会话请求分成报文段，添加源和目的端口，如服务器使用80端口监听客户端的请求，客户端由系统随机选择一个端口如5000，与服务器进行交换，服务器把相应的请求返回给客户端的5000端口。然后使用IP层的IP地址查找目的端。

3、客户端的网络层不用关心应用层或者传输层的东西，主要做的是通过查找路由表确定如何到达服务器，期间可能经过多个路由器，这些都是由路由器来完成的工作，我不作过多的描述，无非就是通过查找路由表决定通过那个路径到达服务器。

4、客户端的链路层，包通过链路层发送到路由器，通过邻居协议查找给定IP地址的MAC地址，然后发送ARP请求查找目的地址，如果得到回应后就可以使用ARP的请求应答交换的IP数据包现在就可以传输了，然后发送IP数据包到达服务器的地址。

**事件顺序：**

1. 浏览器获取输入的域名 www.baidu.com
1. 浏览器向DNS请求解析www.baidu.com的IP地址
1. 域名系统DNS解析出百度服务器的IP地址
1. 浏览器与该服务器建立TCP连接(默认端口号80)
1. 浏览器发出HTTP请求，请求百度首页
1. 服务器通过HTTP响应把首页文件发送给浏览器
1. TCP连接释放
1. 浏览器将首页文件进行解析，并将Web页显示给用户。

**涉及到的协议：**

1、应用层：HTTP(www访问协议)，DNS(域名解析服务)DNS解析域名为目的IP，通过IP找到服务器路径，客户端向服务器发起HTTP会话，然后通过运输层TCP协议封装数据包，在TCP协议基础上进行传输。

2、传输层：TCP(为HTTP提供可靠的数据传输)，UDP(DNS使用UDP传输)HTTP会话会被分成报文段，添加源、目的端口；TCP协议进行主要工作。

3、网络层：IP(IP数据数据包传输和路由选择)，ICMP(提供网络传输过程中的差错检测)，ARP(将本机的默认网关IP地址映射成物理MAC地址)为数据包选择路由，IP协议进行主要工作，相邻结点的可靠传输，ARP协议将IP地址转成MAC地址。

### 25.列出你知道的HTTP协议的状态码，说出表示什么意思？

推荐：https://www.cnblogs.com/starof/p/5035119.html

### 26.说一下什么是tcp的2MSL？
### 27.为什么客户端在TIME-WAIT状态必须等待2MSL的时间？
### 28.谈一下HTTP协议以及协议头部中表示数据类型的字段？
### 29.使用Socket套接字需要传入哪些参数 ？
### 30.HTTP常见请求头？

推荐：https://juejin.im/post/5c17d3cd5188250d9e604628

### 31.七层模型？

推荐：https://juejin.im/post/59eb06b1f265da430f313c7f

### 32.url的形式？

1、TCP 阻塞如何解决。1、项目细节讲解，流程图，瓶颈在哪儿

项目整体架构
共包含哪些服务
服务之间信息流是如何流转的
在项目中，有没有遇到什么难点
有没有排查过项目的线上问题
你觉得你现在的设计有什么问题么？
如果你负责的服务从100TPS变成1万TPS会有什么问题？怎么处理？
如果你负责的这个功能之后需要频繁变更，你怎么设计？
等等等

2、QPS，如何压测，性能测试，性能指标，性能优化。

3、秒杀场景设计

4、高并发

5、分布式锁实现方案。

6、1G 内存，1T 文件，想找到出现次数第二大的字符串。

7、项目架构图，自我感觉项目难点，现在再让你重新做这个项目你会有什么修改跟调整

8、常用的负载均衡算法，自己选择个负载均衡算法来实现并进行自测。

9、常用限流方法，自己咋实现。

10、分布式链路追踪的实现跟理解。
## 翻转字符串

**问题描述**

请实现一个算法，在不使用【额外数据结构和储存空间】的情况下，翻转一个给定的字符串(可以使用单个过程变量)。

给定一个string，请返回一个string，为翻转后的字符串。保证字符串的长度小于等于5000。

**解题思路**

翻转字符串其实是将一个字符串以中间字符为轴，前后翻转，即将str[len]赋值给str[0],将str[0] 赋值 str[len]。

**源码参考**

```
func reverString(s string) (string, bool) {
    str := []rune(s)
    l := len(str)
    if l > 5000 {
        return s, false
    }
    for i := 0; i < l/2; i++ {
        str[i], str[l-1-i] = str[l-1-i], str[i]
    }
    return string(str), true
}
```

**源码解析**

以字符串长度的1/2为轴，前后赋值
## 判断两个给定的字符串排序后是否一致

**问题描述**

给定两个字符串，请编写程序，确定其中一个字符串的字符重新排列后，能否变成另一个字符串。
这里规定【大小写为不同字符】，且考虑字符串重点空格。给定一个string s1和一个string s2，请返回一个bool，代表两串是否重新排列后可相同。
保证两串的长度都小于等于5000。

**解题思路**

首先要保证字符串长度小于5000。之后只需要一次循环遍历s1中的字符在s2是否都存在即可。

**源码参考**

```
func isRegroup(s1,s2 string) bool {
	sl1 := len([]rune(s1))
	sl2 := len([]rune(s2))

	if sl1 > 5000 || sl2 > 5000 || sl1 != sl2{
		return false
	}

	for _,v := range s1 {
		if strings.Count(s1,string(v)) != strings.Count(s2,string(v)) {
			return false
		}
	}
	return true
}
```

**源码解析**

这里还是使用golang内置方法 `strings.Count` 来判断字符是否一致。
## 字符串替换问题

**问题描述**

请编写一个方法，将字符串中的空格全部替换为“%20”。
假定该字符串有足够的空间存放新增的字符，并且知道字符串的真实长度(小于等于1000)，同时保证字符串由【大小写的英文字母组成】。
给定一个string为原始的串，返回替换后的string。

**解题思路**

两个问题，第一个是只能是英文字母，第二个是替换空格。

**源码参考**

```
func replaceBlank(s string) (string, bool) {
	if len([]rune(s)) > 1000 {
		return s, false
	}
	for _, v := range s {
		if string(v) != " " && unicode.IsLetter(v) == false {
			return s, false
		}
	}
	return strings.Replace(s, " ", "%20", -1), true
}
```

**源码解析**

这里使用了golang内置方法`unicode.IsLetter`判断字符是否是字母，之后使用`strings.Replace`来替换空格。## 机器人坐标问题

**问题描述**

有一个机器人，给一串指令，L左转 R右转，F前进一步，B后退一步，问最后机器人的坐标，最开始，机器人位于 0 0，方向为正Y。
可以输入重复指令n ： 比如 R2(LF) 这个等于指令 RLFLF。
问最后机器人的坐标是多少？

**解题思路**

这里的一个难点是解析重复指令。主要指令解析成功，计算坐标就简单了。

**源码参考**

```
package main

import (
	"unicode"
)

const (
	Left = iota
	Top
	Right
	Bottom
)

func main() {
	println(move("R2(LF)", 0, 0, Top))
}

func move(cmd string, x0 int, y0 int, z0 int) (x, y, z int) {
	x, y, z = x0, y0, z0
	repeat := 0
	repeatCmd := ""
	for _, s := range cmd {
		switch {
		case unicode.IsNumber(s):
			repeat = repeat*10 + (int(s) - '0')
		case s == ')':
			for i := 0; i < repeat; i++ {
				x, y, z = move(repeatCmd, x, y, z)
			}
			repeat = 0
			repeatCmd = ""
		case repeat > 0 && s != '(' && s != ')':
			repeatCmd = repeatCmd + string(s)
		case s == 'L':
			z = (z + 1) % 4
		case s == 'R':
			z = (z - 1 + 4) % 4
		case s == 'F':
			switch {
			case z == Left || z == Right:
				x = x - z + 1
			case z == Top || z == Bottom:
				y = y - z + 2
			}
		case s == 'B':
			switch {
			case z == Left || z == Right:
				x = x + z - 1
			case z == Top || z == Bottom:
				y = y + z - 2
			}
		}
	}
	return
}

```

**源码解析**

这里使用三个值表示机器人当前的状况，分别是：x表示x坐标，y表示y坐标，z表示当前方向。
L、R 命令会改变值z，F、B命令会改变值x、y。
值x、y的改变还受当前的z值影响。

如果是重复指令，那么将重复次数和重复的指令存起来递归调用即可。
## 常见语法题目 一

### 1、下面代码能运行吗？为什么。

```go
type Param map[string]interface{}

type Show struct {
	Param
}

func main1() {
	s := new(Show)
	s.Param["RMB"] = 10000
}
```

**解析**

共发现两个问题：

1. `main` 函数不能加数字。
2. `new` 关键字无法初始化 `Show` 结构体中的 `Param` 属性，所以直接对 `s.Param` 操作会出错。

### 2、请说出下面代码存在什么问题。

```go
type student struct {
	Name string
}

func zhoujielun(v interface{}) {
	switch msg := v.(type) {
	case *student, student:
		msg.Name
	}
}
```

**解析：**

golang中有规定，`switch type`的`case T1`，类型列表只有一个，那么`v := m.(type)`中的`v`的类型就是T1类型。

如果是`case T1, T2`，类型列表中有多个，那`v`的类型还是多对应接口的类型，也就是`m`的类型。

所以这里`msg`的类型还是`interface{}`，所以他没有`Name`这个字段，编译阶段就会报错。具体解释见： <https://golang.org/ref/spec#Type_switches>

### 3、写出打印的结果。

```go
type People struct {
	name string `json:"name"`
}

func main() {
	js := `{
		"name":"11"
	}`
	var p People
	err := json.Unmarshal([]byte(js), &p)
	if err != nil {
		fmt.Println("err: ", err)
		return
	}
	fmt.Println("people: ", p)
}
```

**解析：**

按照 golang 的语法，小写开头的方法、属性或 `struct` 是私有的，同样，在`json` 解码或转码的时候也无法上线私有属性的转换。

题目中是无法正常得到`People`的`name`值的。而且，私有属性`name`也不应该加`json`的标签。


### 4、下面的代码是有问题的，请说明原因。

```go
type People struct {
	Name string
}

func (p *People) String() string {
	return fmt.Sprintf("print: %v", p)
}

func main() {
 	p := &People{}
	p.String()
}
```

**解析：**

在golang中`String() string` 方法实际上是实现了`String`的接口的，该接口定义在`fmt/print.go`  中：

```go
type Stringer interface {
	String() string
}
```

在使用 `fmt` 包中的打印方法时，如果类型实现了这个接口，会直接调用。而题目中打印 `p` 的时候会直接调用 `p` 实现的 `String()` 方法，然后就产生了循环调用。


### 5、请找出下面代码的问题所在。

```go
func main() {
	ch := make(chan int, 1000)
	go func() {
		for i := 0; i < 10; i++ {
			ch <- i
		}
	}()
	go func() {
		for {
			a, ok := <-ch
			if !ok {
				fmt.Println("close")
				return
			}
			fmt.Println("a: ", a)
		}
	}()
	close(ch)
	fmt.Println("ok")
	time.Sleep(time.Second * 100)
}
```


**解析：**

在 golang 中 `goroutine` 的调度时间是不确定的，在题目中，第一个写 `channel` 的 `goroutine` 可能还未调用，或已调用但没有写完时直接 `close` 管道，可能导致写失败，既然出现 `panic` 错误。



### 6、请说明下面代码书写是否正确。

```go
var value int32

func SetValue(delta int32) {
	for {
		v := value
		if atomic.CompareAndSwapInt32(&value, v, (v+delta)) {
			break
		}
	}
}
```


    
**解析：**

`atomic.CompareAndSwapInt32` 函数不需要循环调用。


### 7、下面的程序运行后为什么会爆异常。

```go
type Project struct{}

func (p *Project) deferError() {
	if err := recover(); err != nil {
		fmt.Println("recover: ", err)
	}
}

func (p *Project) exec(msgchan chan interface{}) {
	for msg := range msgchan {
		m := msg.(int)
		fmt.Println("msg: ", m)
	}
}

func (p *Project) run(msgchan chan interface{}) {
	for {
		defer p.deferError()
		go p.exec(msgchan)
		time.Sleep(time.Second * 2)
	}
}

func (p *Project) Main() {
	a := make(chan interface{}, 100)
	go p.run(a)
	go func() {
		for {
			a <- "1"
			time.Sleep(time.Second)
		}
	}()
	time.Sleep(time.Second * 100000000000000)
}

func main() {
	p := new(Project)
	p.Main()
}
```

**解析：**

有一下几个问题：

1. `time.Sleep` 的参数数值太大，超过了 `1<<63 - 1` 的限制。
2. `defer p.deferError()` 需要在协程开始出调用，否则无法捕获 `panic`。


### 8、请说出下面代码哪里写错了

```go
func main() {
	abc := make(chan int, 1000)
	for i := 0; i < 10; i++ {
		abc <- i
	}
	go func() {
		for  a := range abc  {
			fmt.Println("a: ", a)
		}
	}()
	close(abc)
	fmt.Println("close")
	time.Sleep(time.Second * 100)
}
```

**解析：**

协程可能还未启动，管道就关闭了。


### 9、请说出下面代码，执行时为什么会报错

```go
type Student struct {
	name string
}

func main() {
	m := map[string]Student{"people": {"zhoujielun"}}
	m["people"].name = "wuyanzu"
}

```

**解析：**

map的value本身是不可寻址的，因为map中的值会在内存中移动，并且旧的指针地址在map改变时会变得无效。故如果需要修改map值，可以将`map`中的非指针类型`value`，修改为指针类型，比如使用`map[string]*Student`.


### 10、请说出下面的代码存在什么问题？

```go
type query func(string) string

func exec(name string, vs ...query) string {
	ch := make(chan string)
	fn := func(i int) {
		ch <- vs[i](name)
	}
	for i, _ := range vs {
		go fn(i)
	}
	return <-ch
}

func main() {
	ret := exec("111", func(n string) string {
		return n + "func1"
	}, func(n string) string {
		return n + "func2"
	}, func(n string) string {
		return n + "func3"
	}, func(n string) string {
		return n + "func4"
	})
	fmt.Println(ret)
}
```

**解析：** 

依据4个goroutine的启动后执行效率，很可能打印111func4，但其他的111func*也可能先执行，exec只会返回一条信息。


### 11、下面这段代码为什么会卡死？

```go
package main

import (
    "fmt"
    "runtime"
)

func main() {
    var i byte
    go func() {
        for i = 0; i <= 255; i++ {
        }
    }()
    fmt.Println("Dropping mic")
    // Yield execution to force executing other goroutines
    runtime.Gosched()
    runtime.GC()
    fmt.Println("Done")
}
```

**解析：**

Golang 中，byte 其实被 alias 到 uint8 上了。所以上面的 for 循环会始终成立，因为 i++ 到 i=255 的时候会溢出，i <= 255 一定成立。

也即是， for 循环永远无法退出，所以上面的代码其实可以等价于这样：
```go
go func() {
    for {}
}
```

正在被执行的 goroutine 发生以下情况时让出当前 goroutine 的执行权，并调度后面的 goroutine 执行：

- IO 操作
- Channel 阻塞
- system call
- 运行较长时间

如果一个 goroutine 执行时间太长，scheduler 会在其 G 对象上打上一个标志（ preempt），当这个 goroutine 内部发生函数调用的时候，会先主动检查这个标志，如果为 true 则会让出执行权。

main 函数里启动的 goroutine 其实是一个没有 IO 阻塞、没有 Channel 阻塞、没有 system call、没有函数调用的死循环。

也就是，它无法主动让出自己的执行权，即使已经执行很长时间，scheduler 已经标志了 preempt。

而 golang 的 GC 动作是需要所有正在运行 `goroutine` 都停止后进行的。因此，程序会卡在 `runtime.GC()` 等待所有协程退出。
