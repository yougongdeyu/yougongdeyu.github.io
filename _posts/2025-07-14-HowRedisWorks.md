## redis 如何做到主从节点的数据一致性的？
1.OS提供的内存copy功能，fork个子进程，然后copy内存
2.记录写操作到某个队列里面
3.如何保证写操作和copy内存之间没有数据gap，通过互斥锁，谁拿到锁谁就只执行，拿不到就等待，完美解决。

## redis的sentinel是什么形式，每个redis实例都需要启动一个么？

Redis Sentinel 是一个 独立的进程（程序），可以和 Redis Server 分开部署，也可以部署在相同机器上。它不是 Redis 的一部分，而是 Redis 附带的一个独立运行的可执行文件（redis-sentinel 或用 redis-server sentinel.conf --sentinel 启动）。
![safsdf.png](/image/redis/sentinel.png) 



## 如果是redis作为分布式锁的情况下，主从切换丢掉数据怎么办？



## 怎么丢失的？


## 业务层：幂等 + 补偿机制我没太理解，你是说应用层不仅检查锁在不在，还检查锁的内容？那怎么补偿呢？锁不在了加一个锁？
所谓业务幂等，指的是：即使某个操作被执行多次，业务结果依然是正确的。
业务幂等是前提，补偿机制是兜底，锁只是预防，不是唯一保障。
![safsdf.png](/image/redis/Idempotent.png) 
![safsdf.png](/image/redis/Idempotent2.png) 
## redission 锁丢失怎么处理的。
SET myLock <UUID:threadId> NX PX 30000

## 你担心 Redis 主节点收到加锁命令但还没同步给从节点就宕机，切主之后锁丢失，导致多个客户端都能加上锁。

## 主从模式如何保证

## sentinel 主从怎么实现的。我不是说redis主从，是S的主从。



## 如果线程在锁到期之前还没有执行完，那么 Redisson 会自动给锁续期。
这个自动续期有点像数组，和arraylist，空间无限大，但是屏蔽实现细节，自己实现。

##  redis 并发有问题么？
redis 本身没有，但是如果用jedis那是有的，多个线程操作jedis发送，会有问题的。
但是jedis poll没有，因为是分开的。


## 其实一部分是可用性问题，比如主从，redlock，一部分是锁问题，



## 锁续期问题

## 看门狗

## redission


## redission 做了哪些事情，分别怎么做的。

## sentinel 自己的高可用怎么做到。

## 

## 其实除了主从，还有redlock

## 还有wait 

## 为啥lua 就安全了
Redis 命令默认是单条命令原子，但多条就不是

## 产品里面redis场景有哪些。
1.last accesstime
2.liuhao 
3. redis scan. 优化扫描
4. 认证token 续期问题。

## redis分布式系统的看门狗跟redission看门狗


## LRU




## 最终一致性

db删,red查到，

## redisson 锁

## redis 什么时候，spring cache.




*** 应用层，看来只是个调度之类的，写了一些协调的东西，得有个应用层的思想    *** 


哨兵 -> raft->

## 高safe协议 Gossip 协议
Gossip and 哨兵 混淆

联邦灾备。异地多活

持久化


## 对java内部是透明的，除了初始化的时候。


## redission redlock方案，12 被加锁成功，3 加锁后来也失败了，然后1锁挂掉，这个时候是有锁还是没锁
![safsdf.png](/image/redis/RedLock.png) 