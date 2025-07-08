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


数据链路层呢？？？







