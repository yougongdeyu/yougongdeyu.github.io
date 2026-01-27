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



limit 
mapper 里面可以有同名参数不同的借口么？

事务是由spring aop 动态代理生成的。生成逻辑是：如果一个线程会绑定一个 dbconnection（放在threadlocal里面），
事务方法前后会绑定或者重用。hibernate也是这样。


Spring 事务 无法跨线程传播问题
因为不是一个connection。




spring事务的传播行为
有七种，常用的有require，有就用，没有就新建。

事务失效情况：
this调用，没走代理对象
异常被吃掉。
非public方法，代理不走。



有效的，快速的，
高速 高效   英语单词

effictive and high quality



explain
type
key
key_len
extra

ref equal ref

using index

    索引下推：
        就是先用索引，再用回表找数据  WHERE a = 1 AND b = 2;
    下推过滤：
        就是先索引，再索引过滤数据。WHERE a = 1 AND c > 2
        命中索引”，看它有没有参与缩小索引扫描范围，下推过滤，并没有缩小范围，只是执行的时候优化了下。
        如果是WHERE a = 1 AND b >2 那就是命中索引了，因为b在索引范围的时候就参与了。

适合建立索引的字段
查询
条件
分组排序字段

索引失效：
    函数
    模糊匹配： %在左边

explain分三层：
    走不走索引（type，key，key_len）：
        type：扫描策略，走索引还是全表扫描。
        key： 索引名字
        key_len: 用到索引多少字节。
    扫多少（raws，filtered）：
        raws 要扫描多少行数据
        filtered： sql 拿到的数据比例
    执行层extra：
        using index, a = 1 AND b = 2 并且查询数据就是a/b 
        using index condition， a=1 and b>2, 查询范围，再回表。
        using where  ， a = 1 AND c >2 回表，再过滤，哪怕是a = 1 AND b = 2，只要select 的是c，d。
        Using temporary， GROUP BY 临时表，子查询等。
        Using filesort，order by 索引顺序不可用，单独排序
其实先缩小范围再回表就是索引下推
先回表在过滤就是下推过滤

zgc

Bit	含义
M0	对象被标记为存活，尚未搬迁（待搬迁）
M1	对象正在搬迁（GC 已拷贝对象，但用户线程未 remap）
REMAP	对象已经搬迁，引用已经修正（forward pointer 已 CAS）


jvm 内存模型

jvm 参数
    -Xms
    -Xmx


spring 设计模式

    bean 单例
    apo 代理
    工厂模式

工作中的设计模式：
    策略工厂模式：
    责任链模式：
        有多个handler，chain.process 每次index加1，然后继续执行,
        chain：
            public void proceed(Request request) 
                Handler handler = handlers.get(index++);
                handler.handle(request, this);
        handler：
            public void handle(Request request, HandlerChain chain）
        如果某个 Handler 不调用 chain.proceed()，责任链直接中断

    
策略工厂模式：
    可以用自动装配，就是会按照工厂初始化方法的参数类型，来构造初始化方法。如果是list那就list，如果是map，那就map，也可以在要注册的bean上面加注解，然后在初始化方法里面写代码控制。

spring：
    @Qualifier 多个bean 选名字的 和autowired同时用
    @Autowired、按类型（Type）自动注入 Bean
    @Inject、等同于@Autowired + @Qualifier
    @Resource 先名字再类型
    @Configuration bean 工厂
    @Component @Component 表示“这是一个要被容器管理的业务对象”； 作用在类上，构造spring构造，但@COnfiguration自己就灵活的多了。
spring boot：
    1. 自动装配：
        springboot有spring-boot-autoconfigure 负责检测classpath jar包，做自动装配
        至于装配后如何被识别调用，spring 提供了一些interface，比如PlatformTransactionManager，hibernate实现。然后通过PlatformTransactionManager找到bean ，aop拦截后去执行。当然也可以注册其他的PlatformTransactionManager用到的bean了比如EntityManagerFactoryBean。
        <感觉就是一些口子，开放出来>

        @ConditionalOnProperty

        举例：
            classpath 有spring-boot-starter-data-jpa 
            spring-boot-autoconfigure 里面的
                对应的会有一些spring-boot-starter-data-jpa 相关的@Configuration类会被执行
                执行的结果就是注册了bean，至于bean如何被识别，被调用，那是spring开了接口，只要响应实现
                被注册了，那就会被调用。
            @ConditionalOnClass
            @ConditionalOnBean
            @ConditionalOnProperty 
        其实就是springboot做了jar包的自动装配工作，不用开发自己再装配了。开箱即用


    
    2. Spring Boot 提供一系列 starter POM
        一些依赖包，里面包含了好多实现某个领域的所有jar
            spring-boot-starter-web 
                Spring MVC 确实使用了代理/拦截机制，但不是每个注解都直接用代理实现

            spring-boot-starter-data-jpa 
            spring-boot-starter-security  
          
    3. 多种配置方式：
        yaml
        环境变量
        健康状态这些
    4. 命令行启动

    servlet到底是啥？
        其实是跟runable callable有点像，
        它不负责线程创建，不负责请求接受，只负责如何处理数据。
    
    只有一个DispatcherServlet，但是被多个线程同时调用，但因为不存状态，所以多线程没关系。       
    给我个支点，我可以撬动地球，
     ApplicationContext（类似那个进程，所有东西都存里面）
     1.beanDefinition
     2.Bean Initialization
    3.tomcat.addServlet("dispatcherServlet", dispatcherServletInstance);



    5.springboot加载流程
        两部分：
            容器构建对象 infra
            应用运行对象 app
            BeanFactoryPostProcessor是分水岭
        启动
            ├─ ApplicationContextInitializer
            ├─ BeanDefinitionRegistryPostProcessor
            │   └─ ConfigurationClassPostProcessor
            │       ├─ @Configuration
            │       ├─ @Import
            │       │   └─ AutoConfigurationImportSelector
            │       ├─ @ComponentScan
            │       └─ @Bean
            ├─ BeanFactoryPostProcessor 占位符，还是bean元信息，可以拦一手。
            ├─ BeanPostProcessor
            │   ├─ @Autowired
            │   ├─ AOP
            │   └─ 生命周期
            └─ ApplicationReady
        
        ConfigurationClassPostProcessor 这个是正经new出来的对象，处理spring 框架接口的bean的逻辑

        bean创建：
            【容器启动阶段】
            1. 读取配置
            2. 解析注解 / XML
            3. 注册 BeanDefinition
            4. BeanFactoryPostProcessor

            ------------------------------------------------
            5. 实例化 Bean（new）
            6. 属性填充（依赖注入） 类字段 autoware
            7. Aware 接口回调  容器资源注入，大多数对象为容器对象而不是bean，所以要织入方式
                - BeanNameAware
                - BeanFactoryAware
                - ApplicationContextAware
            8. BeanPostProcessor#postProcessBeforeInitialization
            9. 初始化。     
                - @PostConstruct
                - InitializingBean.afterPropertiesSet()
                - init-method
            10. BeanPostProcessor#postProcessAfterInitialization
                - AOP 代理在这里产生

            summary： 其实就4类资源，1.new的时候初始化的。2.autoware 属性。3.容器资源对象。4.bean 初始化。5 代理。 
            BFPP 之前就是一些Infra的东西，配置文件，还有自动配置的实现而已， BeanDefinition。
            BeanFactoryPostProcessor 其实就是对BeanDefinition。拦了一手。方式就是springboot会提前实例化这些类型的bean
            ConfigurationClassPostProcessor 其实也是提前注入的bean，

            所以spring boot只是提供接口，然后在固定的生命周期去发现和调用，至于做什么，怎么做，完全由实现类去决定执行

    java类加载


   

    8.tomcat， 责任链，dispatch servlet 工作流程
        客户端 HTTP 请求
        ↓
        Tomcat 接收连接
        ↓
        Tomcat 工作线程
        ↓
        FilterChain（责任链）
        ├─ CORS Filter
        ├─ Logging Filter
        ├─ Token / JWT Filter
        ├─ Spring Security Filter
        ↓（全部放行）
        DispatcherServlet  （它本身也是一个 Servlet）
        ↓
        HandlerMapping
        ↓
        Controller 方法
        ↓
        返回 Response

启动慢怎么排查

三级缓存 循环依赖问题


spring cloud：
    服务注册与发现：
        Eureka vs Nacos
    配置中心：

    负载均衡：
        Spring Cloud LoadBalancer
        Feign
    熔断、限流、降级：

    网关：
docker：


k8s：
    ingress：
        有ingress定义
        还有ingress controller， 比如 nginx

    service：
        clusterIp： Service 的虚拟 IP 地址
        service只是一个规则，真正执行流量转发的，是 kube-proxy
        kube-proxy是啥：是linux 内部内核层根据规则网络转发的一个东东。
    endpoint：
        Endpoint Controller 监听pod，service selector，生成endpoints对象。
        kube-proxy消费 endpoints，比如：
            根据一个servic selector找到两个pod，那这两个pod的ip pod，就是endpoint
    pod：
        里面可以有多个container，或者说进程。
        资源都是共享的。多个进程共同组成一个application。
        资源是共享的，类似一个小主机。
    configmap：
        deployment消费为env
        应用里面也有改的

    deployment：
        管理pod用的，ReplicaSet Controller会监控pod数量够不够。
        containers： 定义了image name，也可以加上command ：[ sleep]覆盖dockerimage里面的cmd 然后我就可以进去做事情了。
        [一个docker 不是只能有一个进程么，但是其实是一个主进程，可以有多个字进程]

        kubectl apply -f 是修改deployment，而delte + create是销毁deployment再创建（服务中断风险）。


    pv/pvc：
        pvc是存储的请求声明
        pv是实际的存储服务
            其实就是生产和消费，pv定义资源， pvc 消费资源

    如何把多个service弄成一个service
        ingress

    如果一个service的一个selector 对应的pod有多个并且不同端口呢？
        那对不起，serivce的target port必须被pod保持一致，才行。


    限流：
        Ingress限流：
            annotations:
                nginx.ingress.kubernetes.io/limit-connections: "10"     # 并发连接数
                nginx.ingress.kubernetes.io/limit-rps: "5"              # 每秒请求数
        Linked/Istio：
            在k8s层在加个proxy，实现流量监控和管理。

        app层：
            令牌桶：redis 的ratelimiter 或者guava 单个jvm本地的，类似本地缓存
        
    降级：
        应该是app层去做，根据业务情况做

    Liveness → “死了就重启”
    Readiness → “还没准备好就别给流量”

            

helm：
    chart： 安装包
    release： 安装一次就是一个release
        version：每次安装都有一个版本。可以回滚
    values： 改deployment/image/ingress/service参数。

    helm sleep 怎么操作：
        改values.yaml 或者命令行install的时候--set 覆盖
        helm upgread（历史可保留）
        helm uninstall + install 历史没来

    chart例子：
    mychart/
    ├── templates/      # Kubernetes 资源模板（带变量）
    ├── values.yaml     # 参数（你真正常改的地方）
    └── Chart.yaml      # 元信息（名字、版本）


    修改image 步骤：
        helm show values your-chart

        helm upgrade --install my-app ./your-chart \
        --set image.repository=myrepo/myapp \
        --set image.tag=v2.0.1



分库分表：
    ShardingSphere-Proxy：

        SQL 路由

        分片规则

        结果合并

        部分跨库查询

aws


java

python







mysql 的锁 哈希表 进化历史。concurrenthashmap关系




auth service
    authenticator 做authticate （process map 根据type拿token processor 处理 authenticate）
        tokenProcessor（pre post authenticate） auth cache
    preAuthProcessors  List processors ，一个processor有所有的process 的map， authtication前依次调用 preprocessor
    postAuthProcessors。List processors ，一个所有的process 的map， authtication前依次调用 postprocessor



    process是都跑的，但是authentic 是根据   tokentype 一个来跑的


authicateapi。 有 abstract + api + smax


组合模式：执行者从执行的维度分为类，业务层从 功能的角度去组合从分类，第一步pre，第auth，第三部post



线程池：


SQL：
    join：
        select A.xx B.xx from table_A A inner join table_B B on A.xx=B.xx
        left join 
        right join
        full outer join
    
    GROUP BY + HAVING
        SELECT user_id, COUNT(*) cnt
        FROM order
        GROUP BY user_id
        HAVING COUNT(*) > 5;
    DISTINCT：去重
        SELECT COUNT(DISTINCT user_id) FROM order;
    LIMIT / OFFSET：
        SELECT * FROM user LIMIT 10 OFFSET 10;

    

    
