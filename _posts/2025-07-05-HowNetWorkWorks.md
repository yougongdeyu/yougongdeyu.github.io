### socket里面对tcp三次握手是怎么实现的？

其实socket 在操作系统层面是系统调用，三次握手是在内核里面自己做的。

### socket长连接是怎么个情况？

握手后，会发送完，然后接受收到回信。



TCP 四次挥手是什么样子？



### 那其实如果从tcp协议栈的角度看，tcp连接一直都是连接这的，即使四次挥手之后也是可以发送报文的，只是说从socket的角度看，经由tcp协议栈的路被阻断掉了

是的，所谓的断开，是两边都微信删除了，就是说 协议栈会释放连接控制块。server client都释放。


✅ 四、完全断开连接的唯一状态：CLOSED
只有当 TCP 状态变为 TCP_CLOSE：

协议栈会释放连接控制块（struct sock）

不再接受任何报文

不再发送任何东西（包括 ACK）

这才叫真正“物理+逻辑”都断了。




如果你想更深入，可以用下面的方式验证：

使用 tcpdump 抓包，观察 TIME_WAIT 阶段是否还会发 ACK

写一个 shutdown(SHUT_WR) 后持续监听的程序，观察内核行为

看 Linux TCP 协议栈代码中 tcp_rcv_state_process() 如何决定响应动作


![TCPConnection.png](/image/network/TCPConnection.png)
![TCPConnection1.png](/image/network/TCPConnection1.png)

到这里我其实觉得端口也只是对程序的逻辑区分而已，没有什么。

### 那实际上的tcp连接是什么呢？如何共享，如何并发呢？
一条经过网卡 + IP 层 + TCP 层 + socket 控制块，绑定了四元组（src IP, src port, dst IP, dst port）的数据通路。
![WhatTCPConnectionIs.png](/image/network/WhatTCPConnectionIs.png)
![WhatTCPConnectionIs1.png](/image/network/WhatTCPConnectionIs1.png)
![WhatTCPConnectionIs2.png](/image/network/WhatTCPConnectionIs2.png)


 todo 这里没彻底弄明白，需要再深入了解下。

tcp的并发

## tcp往下走是什么东东，如何工作的。 或者说那ip呢，ip在操作系统里面什么形式存在，跟tcp关系？ ip层面的并发问题呢？

### 那ip呢，ip在操作系统里面什么形式存在，跟tcp关系？

### 那其实tcp层跟ip层是很类似的，都是一段程序，只不过工作的层不一样



### 数据链路层呢？？？难道也是跟tcp ip一样？
✅ 是的！你现在已经非常接近网络协议栈的底层统一实现原理了——我们可以很肯定地说：
数据链路层（Data Link Layer）在操作系统中，和 TCP/IP 层一样，也是通过一套“模块化的程序 + 数据结构 + 回调函数”实现的，只不过它面向的是网卡设备和驱动。

“TCP 和 IP 是两个以 C 语言写的内核模块，通过注册机制和回调接口连接起来，实现了分层的协议栈

### 注册机制和回调接口时什么？是不是跟前端，事件驱动类似？那这种思想是不是很相似。为啥要这样，这样有什么好处？扩展性还是什么？ 或者说跟spring bean有点类似？ioc， aop？


### 既然数据链路层把数据发送出去是通过驱动，那并发问题呢？

##  那驱动为什么能控制 CPU 绑定关系？

：驱动负责桥接硬件和内核，既能“告诉硬件怎么干”，也能“告诉内核怎么接”。


### 通常是 5 元组 src/dst ip/port + protocol 是什么东西？

### 如果cpu核心和网卡的队列数不一样怎么办？


## 如果网卡不能接受数据，那这会的数据丢失？还是怎么样子？




### shark什么的软件抓包什么原理，抓包的原理是？莫非它把手伸到内核里面了？



## 经典模型下，网卡是怎么工作的。


🧠 正确的 “指针逻辑” 是：
对于 TX（发送）：
驱动填 descriptor → 更新 next_to_use → 设置 TX_TAIL（通知网卡发送）

网卡发送完，会更新 TX_HEAD（告诉驱动：我处理完多少了）

驱动用 TX_HEAD 来 reclaim buffer，不需要判断 head < tail

对于 RX（接收）：
驱动初始化 descriptor → 设置 RX_TAIL（告诉网卡 buffer 可以用了）

网卡接收到包 → 填写描述符 → 自己更新内部指针

驱动轮询 descriptor，用 next_to_clean 顺序遍历即可

🔔 不存在“RX_HEAD < RX_TAIL”这种判断行为。

### tcp 粘包和拆包？
TCP 是面向流，没有界限的一串数据
给什么发什么，不做区分，给多了拆，给小的合并。
### socket工作在那一层？

socket是传输层的api
### wireshark 怎么工作的，


## 那数据接受后如何保证给到对应线程，比如线程发送请求后是什么状态，然后数据接受后到达tcp层后是怎么给到原来线程接受的？
(2) 接收队列与线程唤醒
阻塞模式：

如果线程正在 recv() 阻塞等待数据，内核会将其唤醒，并从 Socket 接收缓冲区拷贝数据到用户空间。

唤醒机制：通过 Socket 的等待队列（wait_queue_head_t）通知线程。

非阻塞模式：

数据到达后，内核标记 Socket 为“可读”，epoll/select 等 I/O 多路复用机制会通知线程主动读取。





### 数据结构是对这些东西的数字化表示，有了数字化，才能自动化，就比如这里的epoll了，线程了，fd，socket了，表示了才能

## 还有一些就是编程范式，比如事件编程，什么的。就是如何触发这个函数执行。

## epoll三个方法解释下


### 那比如tcp是如何调用epoll的


###
每个 TCP socket 内部有一个等待队列 wait_queue，
用 socket 的 sk_data_ready() 回调，反过来唤醒正在 epoll_wait() 的线程

TCP 层是如何从 skb 提取数据并通知应用层的


### 通过 selector.selectedKeys() 获取就绪事件的集合（Set） 在epoll里面是什么行为


## 所以select.keys里面对应是一个个socket还是一个tcp的包？
## 那如果丢了会怎样，比如一个


## 四次挥手都断开了，为啥还能发ack什么的第四次数据


## 


## 那aio呢？
## 那tcp 丢包怎么保证呢？
