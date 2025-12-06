## 协程是啥？
线程是线程级别的，一个线程起码1M内存，
协程是多个方法栈共享线程的一种机制，在纯用户态维护和管理方法栈，(管理就是)自己实现调度器，来调度各个方法栈，类似os 调度器调度线程一样。

![safsdf.png](/image/yield/yield0.png)
*** 其实吧，就是我只想从代码某个地方挂起或者执行，我并不关心怎么实现。线程只是一个手段而已，无非协程比线程消耗资源更少***
*** 那其实跟线程池的思想也一样 复用 跟java集成也一样，子类实现细节，父类负责公共部分***
## lua yield 举例
coroutine.yield()：暂停协程，把执行权交回主线程
coroutine.resume(co, ...)：恢复协程运行，并可传值给协程

![safsdf.png](/image/yield/yield1.png)
![safsdf.png](/image/yield/yield2.png)

![safsdf.png](/image/yield/yield3.png)

##  coroutine.yield 是交出控制权，那什么时候拿到控制权呢？
coroutine.yield()：暂停协程，把执行权交回主线程
coroutine.resume(co, ...)：恢复协程运行，并可传值给协程

## 那我发送了请求，然后暂停，然后我怎么知道什么时候resume合适
这是协程编程的核心挑战之一：你需要设计一种机制，在「外部事件到来时」主动调用 coroutine.resume() 来恢复协程。
比如resttemplate类似的库，它是同步的，对于协程是不友好的，或者根本不能用的。

*** 所以也是底层也是epoll，就是等待epoll的通知，然后resume ***

## 那是不同方法yield了，方法调用栈怎么隔离的呢？
![safsdf.png](/image/yield/luaYieldState.png)

## 那我理解，其实是类似于线程的方法栈一样，只不过是lua自己维护了多个方法栈
Lua 协程就像“线程的简化版”，它自己维护“线程的调用栈”，不依赖系统线程，因此能轻量、灵活地实现挂起与恢复。
![safsdf.png](/image/yield/luaYieldState1.png)
![safsdf.png](/image/yield/luaYieldState2.png)


## 那lua里面肯定也有类似RestTemplate，那配合lua 协程的时候怎么用？

Lua 中如果你要让 HTTP 请求配合协程 yield() 使用，就必须用一个“协程友好”的网络库，它必须支持异步 + resume 的调用模型。



## 所以需要一个http是非阻塞的lib，才能配合lua使用


ChatGPT 说：
✅ 完全正确！你一语道破核心：

Lua 要想使用 coroutine.yield() 来挂起协程，HTTP 请求必须是非阻塞的，或者由某个库封装成“协程友好的”才能配合使用。

# 那这个调度是用户态调度多个线程的，还是一个线程一个调度器
不是的
Lua 协程不像线程那样被操作系统抢占
它只有在执行 coroutine.yield() 时才会主动“交出控制权”
所以 Lua 虚拟机一次只能运行一个协程
所有协程共享一份 Lua 栈空间（但每个协程有自己私有的 call stack）
![safsdf.png](/image/yield/LuaSchedule.png) 

# lua是多线程么？

Lua 本身 不是多线程的，它是一个 完全单线程 的解释型语言。
✅ 如果你真的想用多线程怎么办？
可以通过 宿主语言（C、Java、Python 等）来创建多个 Lua VM 实例，每个绑定在一个线程上：

## 那有些比如python实现yield的方式为啥不是真正的协程？实现方式不一样么？

Python 协程底层基于生成器（generator），使用 await 关键字挂起，通过事件循环（event loop）调度不同协程，实现在单线程中“并发执行”多个任务。
![safsdf.png](/image/yield/pythonyield.png) 
![safsdf.png](/image/yield/pythonyield1.png) 
![safsdf.png](/image/yield/pythonyield2.png) 
![safsdf.png](/image/yield/pythonyield3.png) 
无所谓了，通往成功的路不止这一条

## epoll 之前是一个线程，现在是多个协程，那哪个先执行，哪个后执行。
那lua里面，多个协程，epoll了，那么多协程如何知道调用哪个，


## 就是说没有协程的时候是直接拉起线程，有协程的时候是拉起线程里面的调度器，然后这个调度器会找到对应的协程
对的
Rust 的 async/await 协程模型中，epoll 不会直接“叫醒某个协程”，
而是通过 reactor 模式 + 任务唤醒机制（Waker），由 调度器 来“重新调度”该协程继续运行。

就是说没有协程的时候是直接拉起线程，有协程的时候是拉起线程里面的调度器，然后这个调度器会找到对应的协程


## js epoll 如何实现的


## 其实你看 nio 跟协程就很像，就是小

## 那 mysql radis这种都是线程模型么？一个请求过去，一个线程，处理？还是说类似java 有nio处理

## 线程池是如何实现的。
维护一个队列，当队列发生改变的时候，线程会被通知来消费队列里面的数据据


其实还是那个思想，aop，包一层的思想。
那个协程是大的包一层的思想，这个也是包一层的思想。懒加载的机制。

js有闭包，java的没有闭包，但是如何像js那样利用nio呢？可以用对象啊，闭包只是执行的上下文而已，存储执行上下文又不单单一个闭包。


## 那mysql如何处理大量的请求，毕竟线程数有限
线程模型（如果完全用 NIO + 单线程处理，会非常难管理 事务隔离；方便锁管理和错误处理）
线程池方案。而不是动态分配。
连接池，客户端连接池排队，限制链接的数量

## nio性能比协程更好， 那我怎么感觉这种nio类似于协程？甚至比协程更加的轻量，或者说对于io专用的模型
是的
在极端高并发、I/O 密集的场景下，NIO 比协程更高效、更轻量。
但 ⚠️ 在复杂业务逻辑、多层 await/挂起的场景中，协程的开发效率和调度性能往往更优。

## java虚拟线程为啥要把jvm栈弄出来，而不是用原来的c栈（原来的jvm公用线程栈，无论是c还是java的栈都放在os线程的栈里面）
因为jvm是用户态的c代码，用户态不能操作cpu的寄存器，也就不能在c栈里面切来切去。所以只能自己 构建栈。


