database

mybatis
hibernate
jpa

1.jpa和hibernate的关系？
jpa定义了一套标准api，hibernate实现了它。
同时hibernate也有自己的实现，非jpa的方式也可以用

2.事务@Transactional是 hibernate的？还是spring的？
注解是spring的，spring会去db执行类似 begin start transactional的sql。
至于后边的entity的save操作。这个纯粹是由hibernate做检查，和执行更新操作的sql。
也就是事务归spring，sql归hibernate。

深层理解下来就是，既然事务注解归spring，那为啥不开个口子有不同的事务逻辑，所以有了事务管理器，可以配置不同的事务逻辑。
比如jpa提供的，比如hibernate提供的，比如原生的。
3.hibernate 执行sql的时候做什么？
entity有好几种状态，这个状态从哪来的。从脏检查来的，内部维护了一个map，包含所有entity，
如果这个entity脏了，并且被事务包围了，那就执行sql更新。如果没有被事务包围，那就不执行sql

3.1:怎么判断脏了
entity有好几种状态，entity会被关联到一个map里面，这个map里维护跟db里面关系，以及entity的状态。
比如如果 User user = userRepository.findById(id)，那map里面的entity状态就是managed。
然后离开transactional的方法后就可以执行sql更新了

**************感觉是另一种ifelse的写法。类似工作流，非侵入性的，给我一个支点，撬动地球。
就好像打个断点，打到那里，然后我啥不能干，想干啥都能干。

4.hiber和jpa，更新用jpasql还是hibersql怎么确定。

5.事务的隔离级别问题？是配置好事务管理器就不变了的？还是说每个entity可以有不同的事务级别
当然是不同的service或者说不同的业务逻辑用不同的级别了。
实现就很简单了事务注解里面加上级别，开启事务的时候指定就好了。


6.无论是那种方式，自动脏检查，还是说，都是改变entity的状态，好让hibernate识别做对应状态的事情，从而做出
正确的行为。




## mybatis

pojo 实体类
xml 定义sql的
mapper： 代理对象的接口。


事务是由spring aop 动态代理生成的。生成逻辑是：如果一个线程会绑定一个 dbconnection（放在threadlocal里面），
事务方法前后会绑定或者重用。hibernate也是这样。


Spring 事务 无法跨线程传播问题
因为不是一个connection。



有效的，快速的，
高速 高效   英语单词

effictive and high quality
