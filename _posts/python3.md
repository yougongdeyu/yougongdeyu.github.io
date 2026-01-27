TEKsystems总结市场常见题目
2.*args和kwargs的区别

3.可迭代对象和迭代器的区别
    iterable 有__inter__ 方法返回迭代器（可以是实现next的self，可以是别的迭代器，可以是生成器）
    iterator 是“带状态的遍历器”。

4.装饰器和生成器项目上会用到吗
    事务 orm 弄下 todo

    生成器：
        with 用法：
            语法级别的 try/finally 封装
            with as xx    xx是enter的返回值
            原理：
                __enter__()
                __exit__()
        yield from batch

5.装饰器大概是什么结构

高阶函数和闭包关系：
    高阶函数：关心“函数能不能被传来传去”
    闭包：关心“变量能不能被记住”

6.元类你了解吗？

new vs init
    new 负责分配内存并返回实例
    init 实例方法，负责初始化实例属性
    每次创建对象的时候，比如 Foo(3),先调用 __new__ 创建对象，再调用 __init__ 初始化对象


7.生成器平时有什么使用场景吗？
8.平时数据库用的是MySQL吗？

9.redis持久化机制或者方案？
10.redis有哪些基本的数据结构

hash set zset string 数组，

11.你们什么场景会用hash这种结构？

12.MySQL的隔离级别?
13.数据库的MVVC了解吗？
14.一般用什么ORM？

15.Python多进程多线程协程有使用场景吗？
16.现在项目里面用到或者学过LLM相关的东西吗？
17.了解Fastapi的依赖注入吗？请求进来有个defined函数，用过吗？

18.Python内存管理机制或者垃圾回收机制

19.ES是自己部署还是？为什么比放在结构化数据库里更好？
20.中间件会自己部署吗？
21.k8s是自己部署还是直接提供现成的平台？会了解k8s里面pod的类型吗？
22.平时团队协作，有很多人同时提交代码的话怎么管理？
23.merge和rebase的区别是什么？
24.你有什么优势和劣势?
26.工作中遇到什么挑战？怎么解决？
27.工作过程中有没有什么非常有成就感的时刻？

4. 魔术方法new和init方法的区别
5. 迭代器和生成器的区别
6. 你们flask项目里怎么做单元测试的，如何mock外部依赖的？
7. 你们的flask项目是怎么分层的？
8. python装饰器的原理
9. WSGI你了解吗？你们是怎么起这个服务的？
10. 有用到异步的特性吗？async
11. 用过asyncio这个包吗？
12. 简单说一下异步的底层实现？
13. 协程和多线程有什么区别？协程比多线程性能好体现在哪里？
14. python里面实现真正的并行有什么方式？
15. 多线程怎么实现并行？
16. redis用过吗？
17. Acid mysql怎么实现的？
18. Bean log主从同步机制有哪些？
19. redis持久化
20. 一般生产环境的redis，两个都打开，还会有数据丢失的情况吗？
21. 一个写入命令之间是怎么工作的？
22. 了解过主从机制吗？
23. mysql有哪些锁？读写、并发场景，锁是怎么设计的？
24. 有没有主动获取锁的场景？说一下具体的过程
25. mysql查询慢需要优化的场景怎么做的？哪个ORM框架？遇到过索引未命中的情况吗？
26. 知道MySQL索引是如何加速查询的吗？
27. 创建索引有什么代价？对写操作的性能有影响吗？
28. innodb的聚簇索引和非聚簇索引有什么区别？
29. 回表这个操作什么时候会出现？
2. 日志监控和分析的具体方法？
3. 机器学习算法的使用？
4. 数据预处理怎么去除标点符号等等的内容？
5. 应用级别日志，状态码如何填写？

6. 会调用大模型吗？
7. 为什么几百万条大模型会崩掉？

8. python基本容器类型？
int float bool str cmplex
list tuple 
dict set


9. 列表、集合、字典的准入要求？
10. 可变类型和不可变类型的区别是什么？
11. 函数的两种入参方式和场景的区别？
12. 函数默认值的定义方式？
13. 装饰器？接受传参和不接受传参的区别？
14. 类的定义，会用到innit方法？双下划线开头的魔术方法有用过什么？
15. restful api的规范是什么？

异常：
    raise todo 

    
orm：
    def list_users(db: Session = Depends(get_db))
    这是一个运行时依赖解析系统，不是语法层装饰器。
    冒号是干啥的todo
        来一个事务
            async def get_db():
                conn = await db_pool.acquire()
                try:
                    yield conn
                finally:
                    await db_pool.release(conn)

            async def get_tx(conn=Depends(get_db)):
                tx = conn.transaction()  # asyncpg 的事务对象
                await tx.start()         # 开始事务
                try:
                    yield conn           # 把同一个连接注入路由
                    await tx.commit()    # 请求结束后提交
                except:
                    await tx.rollback()  # 出错回滚
                    raise
        事务的传播行为完全由用户决定
    
    async with 问题：
        实现两个api，__aenter__() 获取资源，__aexit__() 自动释放资源
        fastapi同时支持 yield+finally ，和async with

fastapi：
    单例模式：
        x
    反射
        把路由函数或依赖函数当作 对象 保存
            func = greet       # 赋值给变量
            print(func("Alice"))  # 动态调用函数
        拿到参数签名
            sig = signature(my_route)
            print(sig.parameters)
            # OrderedDict([('user_id', <Parameter "user_id: int">), ('db', <Parameter "db">)])
        type hint 
            def add(a: int, b: int) -> int:
            python 3 支持 类型注解（type hints），就是在函数参数或返回值上标注类型

    config

    注解

    

    ioc 
        FastAPI 所有依赖本质上都是通过 Depends 声明，然后框架在运行时解析函数签名，
        构建依赖图并递归执行。普通依赖只是简单函数调用，而带 yield 的依赖则通过生成器实现生命周期管理。
        框架还对依赖结果做了 request 级缓存，避免重复执行。整体是一个轻量级 IoC / 依赖调度系统。

        FastAPI 所有 Depends 形式的依赖，底层机制都是同一套：
        基于函数签名解析 → 构建依赖图 → 递归调用 → 生命周期管理。

        async def get_db():
            # 假设 db_pool 是全局初始化的连接池
            conn = await db_pool.acquire()
            try:
                yield conn  # 注入路由
            finally:
                await db_pool.release(conn)


        FastAPI 的依赖本身 无状态、无连接管理
        有状态资源必须外部管理，依赖只负责引用
        结合 单例 / 连接池 / yield / async context manager 可以安全地注入有限资源

        那db_pool 初始化在哪里做的：
            app = FastAPI()
            @app.on_event("startup")
            async def startup():
                global db_pool  = await asyncpg.create_pool()



        yield 和await区别
            yield 会把值注入路由函数，FastAPI 会继续执行 yield 后面的 finally，释放资源
            await 是 挂起协程，等到下次唤醒执行
                async def get_db_yield():
                    conn = await db_pool.acquire()
                    try:
                        yield conn       # 注入路由参数
                    finally:
                        await conn.release()  # 请求结束后自动释放 请求结束后释放资源（异步操作，需要 await）

        global变量
            app 里面定义
            然后初始化，初始化的时候要global
            然后是依赖函数里面使用，据说只读不写不用global 关键字
            
            配合 yield + finally，保证请求结束后释放连接
                这个finally如何工作
                路由执行完毕 -> FastAPI 调用 generator.__anext__() 继续执行
        



    事务管理器 
    协程级别 threadlocal ContextVar

    WSGI VS ASGI
        WSGI 多线程 同步 api
        ASGI Asynchronous Server Gateway Interface 协程，异步
一个下划线和两个下划线区别
    _var 约定外部不能访问
    __var 类定义阶段改名了，所以访问不了
    __var__ 魔术方法


Exception： 
    todo
    首先都是exception ，error只是命名习惯


多继承：


两种参数：
    xx
    *args → 捕获多余的位置参数
    **kwargs → 捕获多余的关键字参数

那filter 呢有没有
    有的，也是往app 里面注册
        @app.middleware("http")
        async def log_middleware(request, call_next):
            print("before request")
            response = await call_next(request)  # 调用下一个中间件或路由
            print("after response")
            return response
    Depends = 依赖提供者，不管是对象还是逻辑都可以。可以校验权限然后抛异常等。
    比如 demo：
        @app.get("/manager")
        async def manager_route(user=Depends(role_required("manager"))):


一点感触
    装饰器，不好好增强函数，而要去注册用
    DI 不仅仅DI，还要做业务逻辑比瑞校验权限

工程上：
    比如权限校验

        会有一个专门的module，权限定义 → auth 模块集中维护，使用的时候import → 从 auth 模块__init__.py统一导出
    __init__.py 决定一个目录是不是“包”，同时可以控制“这个包对外暴露什么接口”。



函数： 
    一等公民
        赋值给变量
        作为参数传递给函数
        作为返回值从函数返回
        存储在数据结构中（列表、字典、集合等）
    FastAPI
        FastAPI 在注册阶段收集这个引用到依赖树
        在请求阶段调用这个函数获得资源

        函数像数据一样可以传递、存储和动态调用。




装饰器：
    app = FastAPI()               # app 对象此时已创建

    @app.exception_handler(Exception)
    async def handler(...):

    上面类似
        1. app = FastAPI()               # app 对象此时已创建
        2. decorator = app.exception_handler(Exception)
        3. async def handler(request, exc):
        4. handler = decorator(handler)
    工作流程：
        Python import 模块
        执行顶层代码（从上到下）
        创建 app = FastAPI()
        遇到 @app.exception_handler(Exception)
        调用 app.exception_handler(Exception)
        得到 decorator
        创建 handler 函数对象
        decorator(handler) → 注册进 app
        模块加载完成
    其实他拿装饰器当注册用


fastapi vs spring
    python：
        一切都是对象（函数也是对象）
        函数是一级公民
        装饰器 + import 执行语义 极其强大

    显式优于隐式
    Explicit is better than implicit.

    就没有

多进程
    todo


db pool：
    不会有并发问题，多个协程不会有问题，因为每个协程是不同的函数，是不同的生成器对象。


垃圾收集器
    todo

大模型 参数 量级
    7b



那对于pool来讲的话


    
英文自我介绍

最后的q&a

大大方方的，不要紧张


为啥想转python



chrunking：
    
    遇到的问题：


todo
    
    召回率

chunk：
    nltk 拆分为
    process_large_sentence
        一句句append，如果append> 512, 放入list，继续新建。句子内部用tokenizer，句子之间也有
    merge_sentence
        把小句子合并为长句子，句子间的tokenizer做拼接，也就是成为chunk
    批量embeding
        调用模型生成 向量，并且拼接向量
    顺序向量余玄相似度小于多少，那就聚合为cluster
    遍历cluster，如果没超过maxtoken，append chunk，如果cluster太大，那就继续调高 threshold +step，重新聚合，然后再append结果。

    cluster怎么表示，其实就是 前面embeding后的元素索引[[0,1][2,3,4]]
    最后拿着cluster去拼 chunk
    
    
    