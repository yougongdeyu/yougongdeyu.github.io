
aws

Compute：
    ec2
    Auto Scaling
    Lambda
    ECS / EKS

Storage：
    s3
    EBS
    EFS

Database：
    RDS
    Aurora
    DynamoDB
    ElastiCache

Network：
    VPC
    ELB
        ALB
        NLB
        CLB？？
    Route 53
    API gateway
Security：
    IAM
    KMS
    WAF
    Shield
MQ：
    SQS
    SNS
 Observability & Ops：
    CloudWatch
    CloudTrail
    X-Ray。********* 我们公司的链路跟踪是啥，原理，如何实现的

Devops：
    CloudFormation

    cloud运维是怎么做的

Bigdata & AI

    SageMaker



算力      → EC2 / Lambda / ECS / EKS
存储      → S3 / EBS / EFS
数据库    → RDS / DynamoDB / Redis
网络      → VPC / ELB / Route53
安全      → IAM / KMS / WAF
解耦      → SQS / SNS / EventBridge
运维      → CloudWatch / CloudTrail


云原生是啥

出问题就销毁重建

镜像 + IaC 才是“真机器”
怎么理解


Kubernetes 为什么一定是声明式 + 控制器

为什么云原生系统要极度强调“无状态”

云原生数据库 vs 传统数据库的根本差异

为什么云原生系统 Debug 反而更难



声明式 + 控制器
k8s也是
CloudFormation 的核心理念 用“声明式代码”描述期望的云资源状态，由云平台负责创建、变更、回滚和一致性维护。



vault怎么用
helm

k8s

mychart/
├── templates/      # Kubernetes 资源模板（带变量）
├── values.yaml     # 参数（你真正常改的地方）
└── Chart.yaml      # 元信息（名字、版本）


pv
pvc区别


1️⃣ 多资源的“逻辑应用单元”

一个真实应用至少包含：

Deployment

Service

ConfigMap

HPA

Ingress

RBAC

Kubernetes 眼里，这只是一堆独立资源。

Helm 说的是：

这些资源合起来，叫“一个应用”


熟练使用Zabbix 以及Prometheus、Grafana监控系统







负责基于RBAC模型功能的构建与扩展，优化OnBoarding/OffBoarding流程，提供灵活的权限控制。

并负责其它新功能的设计与开发，比如用于用户调研的Survey，企业级智能交互系统Aviator等，从需求进行讨论与分析，到开发，测试，安全，质量，发布，升级，监控，等阶段的全链路参与。

同时及时处理客户的生产事故，通过patch或者hotfix等方式保证服务的高可用。


大数据风控系统开发：负责整合第三方数据服务商，运用Spark、Flink等大数据技术清洗和加工数据，构建标准化数据接口，为风控模型提供高质量数据源，支撑贷款业务的审批流程。
风控后台系统开发：基于React+Ant Design开发后台管理平台，实现人工审批模块、规则引擎配置界面及数据可视化看板，优化审批人员操作效率。



？？？

api 权限怎么弄的




aviator

grpc：
    python 做service
    java 做client
        proto：
            service HelloService {
            rpc SayHello (HelloRequest) returns (HelloReply);
            }

            message HelloRequest {
            string name = 1;
            }
        service ≈ 接口
        message ≈ DTO
        rpc ≈ 方法签名
        python -m grpc_tools.protoc 生成文件：
            hello_pb2.py → 数据结构   （包装数据）
            ello_pb2_grpc.py → 客户端 stub + 服务端基类（包装server和client用）
        

hybrid search  
semantic search + deterministic search

search api 找到相关article显示在下面

completion

chat


vector db：
    search
        SearchResults
        SearchResultsData
        QueryResults （sql）
        MetricType
    auth：
        insert with auth column
        query with auth expression （withExpr） 

    milvus collection：(强schema)
        id，embedding，content(thunk),......
        withExpr（）查询就好了

    k8s configmap 读itom-aviator-model-configmap
completion
qna
chat：
    model可替换



 @PostConstruct 是如何工作的
 加载mode config




模型切换怎么做的。

chains 从k8s configmap里面拿到的配置配置出来的，配置完了之后设置modelConfig和chains关系，生产端 
如果modelConfig也就是从k8s拿到，那就用对应的engine ,或者从请求里面拿参数，参数指定的是模型的在k8sconfig 里面的key，根据这个key可以拿到model config，从而选择engine，消费端。

model 也是从


model type 是啥

model resource是啥

model stop word

model tokennizer






function call是啥
    训练模型输出调用方法的参数，比如输出方法名，参数。 （模型吐文本，python根据文本类型执行）

langchain：
    while not finished:
    send(state, tools) to LLM
    receive(action)
    execute(action)
    update(state)
langchain：
    也就是不断的跟模型做交互，让模型来参与决策，然后执行后拿到结果再决策执行。
    功能：
        prompt管理
        链式流程
        memory
    tool
    其实就是模拟一个chatgpt交互的流程


langgraph：
    node，edge（普通的和自定义需要llm参与的edge）
    node，edge连接起来构成workflow，类似流程图。
    llm作用，llm作为node 或者edge里面代码内嵌的逻辑，用于语义识别为准确的输出。

mcp：
    是干啥的
    “轻量框架设计理念”或协议规范，自己实现就好。
    有状态 context，有action，有action handler，request，response。
    MCP 更强调 协议化、上下文管理和多轮状态：
    MCP 协议层只是定义：模型 → 请求 → handler → action → 返回
    MCP 的协议层就是 “模型调用能力的规范和约定”，它规定了 action、请求/响应格式和多轮上下文管理，而不是具体执行逻辑。

langchain langgraph 都是框架，mcp是理论指导。


LLM + Embedding + Reranker + Moderation

ci cd？



mcp agent

no sql