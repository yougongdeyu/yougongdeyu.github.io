## 五层模型是啥？
五层模型是函数，五个函数调用
传输层调用网络层调用数据链路层调用物理层
## 网卡驱动在物理层还是哪里
驱动层

## 一个线程可能有多个比如http连接，如何支持并发问题的？

一个线程其实没有并发问题的，都是一个个顺序执行的。
只有多线程才有问题，那其实是问如何支持多线程的。
其实是通过定义数据结构隔离开来，比如维护一个数组，*** 然后线程a的某个引用tcp连接的数据结构，线程b执行的时候使用线程btcp连接的数据结构。*** (1.一样的是剧本，唱戏的人不一样，2.配合思想，3.28思想都有一点)数字化，才能被管理。
也就是逻辑意义，数据不一样，没什么神奇的其实。


## 一个api的json是怎么发出去的？
一层层方法调用，tcp->ip->data laryer -> 最终到物理层的01.
就是一次方法调用，只不过每一层处理的内容和东西不一样。

## socket是什么？
每个 socket 都是内核中的一个对象，表示一条网络连接的句柄
socket是操作tcp udp的一个工具。

socket也可以被包装成fd，file description.
NIX 本地套接字（不走网络）
## 接受 过程是什么样子的


1.驱动维护环形数组，每个元素是buffer的表示，网卡会消费这个数组里面的空元素，然后装满比如200个，中断cpu，网卡里面由两个值表示哪些数组是装满的，然后驱动会读取装满的index,驱动for循环调用数据链路，网络，传输，的方法。for循环一定数量就放一个软中断，由ksoft去处理了。
注意这里的中断不是来一个包就中断，是一批包来了之后中断？
[总结说来就是硬件中断会处理一部分，然后其他更多的会让线程去处理]



然后mtu一层层最后到达顶层怎么找到socket 对应的线程，然后我好唤醒线程

## java代码里面select.keys()拿到的是mtu还是socket/fd?

拿到的是fd 所以会把这个fd读取完。

## 那是不是也会把别的socket也读了？
所以 selector.selectedKeys() 返回的 是所有就绪的 socket（fd）集合

## socket对应的一个mtu拉起线程，那就应用层去处理读完 ，丢包？？？？

## 我怎么知道读完了没
文件我知道读完没有，就是fd知道，那socket包装为fd，我也是可以知道的。

## nio 
ch1.register(selector, SelectionKey.OP_READ);
ch2.register(selector, SelectionKey.OP_READ);
ch3.register(selector, SelectionKey.OP_READ);

chnnel 是什么概念对应到epoll里面。？？？
就是
这个 fd 被注册到 epoll 的 红黑树（epoll instance）中，epoll 内核结构维护了这个 fd 上的 事件、状态、回调队列等信息。？？？
## epoll 
epoll提供了一种机制就是，如果一个

## 我是说epoll的这种，就是java nio的那种，一个socket的mtu到达传输层，然后它是怎么知道通知那个线程的？tcp的ip：port？
是的，内核根据 TCP 包的 5 元组（IP、Port、协议）在 socket 哈希表中查找到对应的 socket（fd），然后通过这个 socket 的 epoll 等待队列找到挂在上面的线程，并唤醒它。
所以channel是fd，register是注册到epoll里面让epoll维护线程和fd也就是socket的关系，一边后续收到数据可以找到拉起那个线程


## 那selector是啥，是epoll？
对的 
chanel是file fd



## epoll 红黑树？
高效查找/添加/删除大量 fd
fd 的值可能很大、稀疏（比如 3、10000、99877）
哈希表虽然查找快，但 删除时性能不稳定、哈希冲突不好控制
红黑树能保证 增删改查都是 O(logN)，而且有序、稳定


## java 的select.keys() 那是不是也会把别的socket也读了？我是说一个mtu触发了线程的拉起，然后线程来处理selector被注册了多个socket/fd， 那是不是会处理
多个socket.?
是的







## nio

## aio
Java 的 AIO 不流行的原因：
Java 7 引入了 AsynchronousSocketChannel（基于 Linux AIO）

但 Linux AIO 实现较差，早期是靠线程池模拟异步，不是真正零拷贝内核异步
直到 io_uring 出现，才有真正高性能的 AIO 
所以 Java 社区还是更常用 NIO + epoll（比如 Netty）
## rpc


## jrpc

## 数据库连接池
就是socket么？只不过这些socket建立了连接，然后没有释放，并且有db自己定制的协议，比如认证了，鉴权之类的。
然后多线程对这些socket的操作进行并发控制。

## 数据库连接池里面的连接，鉴权前后都能发送数据，为啥鉴权之后才能查sql？
其实就是再建立一个对象，然后保存下用户信息，然后这个对象reference到这个连接，后续就能消费到。
![safsdf.png](/image/network/DbConnection.png)
![safsdf.png](/image/network/DbConnection2.png)

## session和token的区别是啥？
Session 和 Token 都是用户登录后的身份凭证机制，区别在于：
Session 是服务端存储身份状态
Token 是客户端持有身份状态
![safsdf.png](/image/network/SessionAndToken.png)
![safsdf.png](/image/network/SessionAndToken2.png)
![safsdf.png](/image/network/SessionAndToken3.png)

### gateway最大连接数。resttemplate是个啥？ resttemplate 设置最大连接上限，啥意思，是发送http请求？然后控制数量？
RestTemplate 的“最大连接上限”，是通过连接池管理来限制 RestTemplate 向目标服务同时发起的 TCP 连接数，

## 所以我猜测resttemplat在多线程访问的时候其实会有阻塞的问题？
对的，就是简单的并发锁控制。

## 所以WebClient代替resttemplate，webclient底层是nio模型?
对，这样cpu就不用被锁阻塞了。


## resttemplate 连接池复用连接怎么实现的
复用 TCP 连接，所以创建连接之前会先检查下对吧？ 对的。

## 复用 TCP 连接 是啥，如果要同样的service才能复用，每次都更换service api就不能复用了
![safsdf.png](/image/network/ReuseTcpConnection.png)

## tcp连接是什么？
所谓的tcp连接是一个数据结构，收发双方都有的一个数据结构，表示你是你，我是我。并且都ready,

三次握手是定义你我，四次挥手是删除你我。

剩下的传输都是是 发送数据 + 确认数据。
确认数据由tcp这层的函数处理。
对于socket 往tcp建立连接，断开连接，其实是tcp数据结构里面的一个标志位，这个标志位在tcp协议方法里面被消费。
也就是个toggle，socket再给我发消息他不发送而已。


## 管理mtu和那个线程关系的东西是什么级别的，线程级别的还是所有线程公用的
内核协议栈中的全局共享结构，

## 那我有个问题啊，协议栈第一个tcp的包会拉起线程来处理这个socket，那这个socket是如何知道内容完整性的，或者说多个tcp包怎么汇总为一个socket的内容的？
应用层回去排序重组，

![safsdf.png](/image/network/NetworkStack.png)

![safsdf.png](/image/network/NetworkStack1.png)
![safsdf.png](/image/network/NetworkStack2.png)
## 所以http工作在应用层，负责排序重组，然后供socket去缓冲区读取数据？别的想redis ，mysql的协议也是一样？
tcp 层负责分包、排序、重组、重传、流控，提供可靠的字节流
排序、重组 是 TCP 做的；消息边界、完整性 是应用协议自己判断的。
![safsdf.png](/image/network/TCPandHttpOrReis.png)

## 现在主流的是什么样的，如果一个请求过来，所有包都会到一个cpu核心处理么？

## 那这样，如果一个api请求，一半的tcp包道理core1,一半的包道理core2, 最终是core1的tcp层建立了socket要读的缓冲区，那core2上的那一半包怎么知道要放在这里呢？


## 五元组


## 但是我想说的是网卡怎么保证一个api的这么多mtu都走到一个rx的ring呢？
 ✅ 靠 RSS（Receive Side Scaling）机制中的 hash 算法来保证：
同一个 TCP flow（即一次 API 请求所属的连接）的多个包都会 hash 到同一个 RX 队列 → 使用同一个 ring buffer。

网卡会计算五元组的hash，然后给到一个rx上。
同时我理解一个rx的ring可以缓存大量的mtu。
 ## cpu亲和性和中断亲和性的关系
 CPU 亲和性控制线程跑在哪个核，中断亲和性控制中断在哪个核触发。两者关系密切——越亲密，性能越好；越分离，延迟越大。

![safsdf.png](/image/network/CPUAndInteraptAffinity.png)

## 是不是亲和性这些只能优化到一个cpu核心？
![safsdf.png](/image/network/CanAfinnityBeAttatchedOnlyToOneCore.png)

## 那cpu亲和性和中断亲和性怎么跟RPS rfs配合使用呢？

## 那为啥很少在java市场上见到这样用的
所以这不是「不会」，而是人少 + 成本高 + 风险大。
 Java 场景多数 IO 忍受型，并不立即性能瓶颈
![safsdf.png](/image/network/WhyAfinityIsNotPopular.png)
## 那redis这样用的多吗？
多，值得 Redis 是一个 高并发、低延迟、事件驱动、单线程模型（或 I/O 多线程） 的系统，特点决定了它：
## redis是把所有的rx tx都亲和到一个cpu么？

不是的，它更骚操作，它通过rps rfs cpu affinity ， 中断affinity，不让某个核心处理协议栈，专门处理redis的计算线程
redis的线程从别的io线程里面消费数据就好了，发送这个核心也不用管。
 
 ## 怎么 保证某个核心不处理协议栈的？
它通过rps rfs cpu affinity ， 中断affinity，不让某个核心处理协议栈。


