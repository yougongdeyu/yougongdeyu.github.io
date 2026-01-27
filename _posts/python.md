4. 魔术方法new和init方法的区别
5. 迭代器和生成器的区别。    *******
所有生成器都是迭代器，但不是所有迭代器都是生成器。


6. 你们flask项目里怎么做单元测试的，如何mock外部依赖的？
pytest + unittest.mock


7. 你们的flask项目是怎么分层的？















8. python装饰器的原理。 ****。 python有闭包？
9. WSGI你了解吗？你们是怎么起这个服务的？
10. 有用到异步的特性吗？async
11. 用过asyncio这个包吗？
12. 简单说一下异步的底层实现？
13. 协程和多线程有什么区别？协程比多线程性能好体现在哪里？
14. python里面实现真正的并行有什么方式？
15. 多线程怎么实现并行？


8. python基本容器类型？
9. 列表、集合、字典的准入要求？
10. 可变类型和不可变类型的区别是什么？
11. 函数的两种入参方式和场景的区别？
12. 函数默认值的定义方式？
13. 装饰器？接受传参和不接受传参的区别？
14. 类的定义，会用到innit方法？双下划线开头的魔术方法有用过什么？



魔术方法




yield：
        def subgen():
            x = yield 1
            x = x+1
        def parent():
            result = yield from subgen()  # ⬅️ 这里的 yield from 处理 return
            print("subgen returned", result)
            return result
        def delegator():
            sub = subgen()
            try:
                value = next(sub)
                while True:
                    received = yield value # 让父生成器 yield 子生成器的值
                    value = sub.send(received) # send 或 next 返回的值传给子生成器
            except StopIteration as e:
                return e.value  # 捕获 return 值

        yield其实就是 return+保存函数状态 并且yield 不只是 return，它还能接收下一次 send() 传来的值
        sub.send(xx) 再把值吐回去继续执行。

        如果调用parent的方法想接收return，那parent要return，如果return，那就拿不到（并且异常路子拿）
        yieldfrom就是把 return+双向通道（把执行交给子类） 都给做了。

        所以return走异常，yield走next。
         next() == send(None)


yield await 原理：

    代码：
        import asyncio
        async def complex_coroutine():
            await asyncio.sleep(1)  # 第一次挂起
            await asyncio.sleep(1)  # 第二次挂起
            return "最终结果"
        # 调用者视角：
        async def caller():
            result = await complex_coroutine() 
            print(f"调用者：收到结果 = {result}")
        asyncio.run(caller())
    上面的yield的情况是双向的，但是await的情况下是单向的，或者断掉一部分的双向

    sleep做的事情是，创建个future，注册到时间监听器里面，时间到了会执行future里面的setvalue。并且yield future给调用方，也就是run（）；run（）会给他注册
    run的作用：
        1.创建事件循环
        2.创建 Task 并运行主协程。（Task 内部负责处理协程的 resume（send）和 return（StopIteration））
        3.运行事件循环直到完成，包括（Task.result 给 asyncio.run()）
        4.results = await asyncio.gather(*tasks)其实是对 run对封装。
    这里是sleep，如果是多种事，那没问题，只是Future注册的地方不一样，但是是run做的事情是一样的。
    *******一句话，负责run负责打乒乓球，打完了给你。*********



    run做的事情就是个单向的类似send绑定，等于是 yield future后绑定 callback为 coro.send()也就是，future会被时间循环set值，比如时间到了，那就set，set到时候就会调用前面绑定的callback继续执行。

    1. asyncio.run() 启动事件循环
    2. 协程执行到 await sleep() 时，内部会 yield future
    3. 事件循环收到这个 future，绑定回调：future.set_result() 时调用 coro.send()
    4. 定时器到时，future.set_result()
    5. 触发回调 → coro.send() → 协程恢复

    所以闭包在这里其实就是future的callback所绑定的coro.send()。

    事件循环到话就是：
        检测一批，然后执行完，再检测一批，从future到视角就是这么简单。

        如果时间太短也没关系，超时就超时了，因为时间检测是在协程执行之后
        执行的，甚至还有别的协程。
        但是没关系，时间事件错，但是别的事件对事件要求没那么高，会处理就好。

    使用携程：（results = await asyncio.gather(*tasks)，事件循环负责捕获 StopIteration，并把值传递给外层 await）
        import asyncio
        import aiohttp
        # 异步函数：发请求
        async def fetch(url):
            async with aiohttp.ClientSession() as session:
                async with session.get(url) as resp:
                    text = await resp.text()  # await 挂起，直到响应完成
                    return f"URL: {url}, length: {len(text)}"
        # 主协程：并发发多个请求
        async def main():
            urls = [
                "https://www.example.com",
                "https://www.python.org",
                "https://www.asyncio.org"
            ]
        results = await asyncio.gather(*tasks)


魔术方法
    Protocol-based polymorphism（基于协议的多态） 对象协议
    可迭代， __iter__, __next__
    可下标 __getitem__
    CPython 对象模型中“类型槽位（slot）”的公开接口。

    鸭子类型。业务协议，不是语言协议。




数据结构：
    数据类型

    集合类
语法特性：
    魔术方法原理：
        魔术方法：
        原理：
            解释器在特定语义场景下，自动调用的一组“约定方法”
            面向对象协议
    装饰器：

并发问题： 
    java：
        锁与同步 lock
        内存语义 
        协调配合 Latch / Barrier / Phaser / Semaphore / Exchanger
        并发容器
        阻塞队列
    python：
        Java 提供的是完整的底层并发原语体系（JUC）；
        Python 提供的是更高层的并发抽象（线程池、进程池、协程调度），刻意弱化底层同步器设计。
        
        因为有GIL：
            不需要大量 lock-free 结构
            Atomic 类几乎没意义
            CAS 原语也未暴露  

            GIL 从根本上改变了并发内存模型。没有内存可见性问题


            但仍然存在“竞态条件”和“逻辑并发问题”。

            GIL使用


        在 asyncio 中：没有 await 的代码段是“天然原子的” 跨 await 的代码段必须当作临界区处理

        asyncio 是协作式调度模型，协程只会在 await 时让出执行权，因此没有 await 的代码段天然具备原子性。
        一旦临界区跨越 await，就会出现逻辑竞态，需要通过 asyncio.Lock 等同步原语保护。


        async with lock:
        global：

        协程锁问题：
            多个协程处理问题：
            await之后会退出临界区


            lock

            Semaphore
            asyncio.BoundedSemaphore
            asyncio.Event
            asyncio.Condition
            asyncio.Queue
        递归锁问题：


GC：
    所有对象统一以 PyObject 存在于堆上，通过引用计数管理生命周期，小对象由 pymalloc 管理，循环引用由分代 GC 兜底清理。
    小对象由 pymalloc 管理， 只是优化，结果还是对象。比如 int 空list这样的小的。相较于c的malloc更轻量级点



内存结构：


数据类型：
    list，tuple，
    都可以放任意类型，但是不建议混用，或者说随意用。推荐users: list[User]，coords: tuple[int, int]
    这是 Python 的动态类型系统 + 对象模型决定的：，本身只存 对象引用（PyObject*)，每个元素在运行时携带自己的类型信息

    typing.Tuple[int, str, float]

    返回多值 todo
    ， 
    self很多 
    实例方法不过是“绑定了实例的函数”


中间件：




chunk：
    chunking的代码
    chunk的方案来源，社区


PyObject / PyTypeObject 结构体细节

FrameObject 执行模型

函数调用栈是如何创建的

闭包变量如何存储（cell object）

GIL 为什么必须存在

gc 模块如何检测循环引用

pymalloc 的 arena/pool/block 实际结构

为什么 Python 多线程 CPU 密集型性能差


生成器和迭代器，迭代器是实现两个方法的类所创建的对象 状态机对象
生成器不是你写的普通类，而是 Python 解释器为你自动生成的一种“状态机对象”
生成器是迭代器的子类

字典中取值，有可能不存在，，会报错，怎么在不产生错误的情况下取数
    value = my_dict.get("key", default_value)

函数式 vs 推导式
    推导式：声明式、直观
    map/filter：函数式、组合式

推导式：
    # 1. 列表推导式（有专用语法）
    lst = [x**2 for x in range(3)]      # [0, 1, 4]
    # 2. 集合推导式（有专用语法）
    st = {x**2 for x in range(3)}       # {0, 1, 4}
    # 3. 字典推导式（有专用语法）
    d = {x: x**2 for x in range(3)}     # {0: 0, 1: 1, 2: 4}
    # 4. "元组推导式"（不存在，但可以模拟）
    t = tuple(x**2 for x in range(3))   # (0, 1, 4)



# 5. 生成器表达式（惰性，用圆括号）
gen = (x**2 for x in range(3))      # <generator object>

list 取所有奇数
    odds = list(filter(lambda x: x % 2 != 0, numbers))

    sorted_words = sorted(words, key=lambda x: len(x))
    sorted(students, key=lambda x: x['score'], reverse=True)

    list(map(lambda x: x ** 2, numbers))

    product = reduce(lambda x, y: x * y, numbers)
    sentence = reduce(lambda x, y: x + y, words)

    sum_all = lambda *args: sum(args)
    print(sum_all(1, 2, 3, 4, 5))  # 15

    grade = lambda score: 'A' if score >= 90 else 'B' if score >= 80 else 'C'
    print(grade(85))  # 'B'

    lambda是简单的函数
    但是可以用def定义复杂函数，然后给到map filter这些高阶函数

    列表推导式：
        # 现代Python方式（推荐）
        squares = [x**2 for x in numbers]                    # 替代 map
        evens = [x for x in numbers if x%2==0]               # 替代 filter
        squares_of_evens = [x**2 for x in numbers if x%2==0] # map + filter

    map： 函数可以通过，策略模式传递给他。

python的应用场景是啥，有什么usecase
    闭包：
        有状态的内部函数
        nonlocal 声明使用外层变量
    装饰器：前后添加方法 
    数据封装：像类一样，暴露方法，封装数据
    回调： 像await 原理一样，把有状态的函数当参数一样传递。
    