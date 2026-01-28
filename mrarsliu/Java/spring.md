# spring
## 一句话概括spring
java的一款轻量级框架，快速构建应用，核心思想是ioc、aop，基于ioc为第三方框架提供了良好的适配
## 说说什么是ioc、aop、di
**ioc搭配di使用，ioc是思想，di是实现手段，通过容器管理和属性注入完成对象创建，区别于new，我们只需要直接使用即可**
**aop面向切面编程，将与业务代码无关的逻辑单独封装，实际上是使用了动态代理技术，生成被aop管理的代理类**
## 什么叫与业务无关
增删改查是业务代码，日志监控权限事务这种可以抽离出来复用的则是非业务代码
## aop的术语介绍
- 切面
- 连接点
- 通知
- 代理
- 目标对象
## ioc的原理
反射-bean map-di-工厂模式
- 反射提供运行时查看值，创建对象，动态方法调用，修改值
- map提供了数据容器
- di实现了值注入，基本值和引用值
- 工厂设计思想，封装了创建和初始化
## aop的原理
动态代理技术，在运行时生成代理类——区别于静态代理（在编译阶段确定）
## 动态代理的实现，JDK的CGLIB的区别
- JDK 基于接口实现，创建对象快，但是调用慢，基于反射
- CGLIB 基于子类，创建慢（字节码层面），调用快，适合没借口实现
## bean的生命周期
- 一开始作为配置文件或者注解标志的静态资源
- beanfacotry扫描并且创建beandefinition（元数据）
- beanfactoryprocessor
- 实例化，分配内存空间，创建初始引用放入一级缓存
- 属性注入，@value，@autowired
- Aware=提供保留bean名称，工厂实力、applicationcontex的机会
- 前置处理
- 初始化——我们自己创建的，可以通过实现接口和（bean、配置文件）指定方法
- 后置处理，aop

## 动态代理是什么
运行时用反射创建的代理，可以根据运行情况对多个类代理，主要实现有jdk和cglib
## 为什么不适用静态代理
可以，但是不这么用。  
在编码时，100个类需要事务？类的方法改变了？spring自带的条件筛选？
## 讲讲beanfactory和applicationcontext的区别
- 超集关系，前者提供基础的ioc，后者提供aop、事件监听发布、预加载
- 前者快速，适合资源紧张场景；后者是现在的首选

## 如何解决循环依赖
## 三级缓存的流程
1. 实例化bean，放入三级缓存
2. 初始化bean，设置值，beanA依赖B
3. 创建B，B依赖A
4. 获取A的三级缓存
5. 完成B创建
6. 完成A创建
## 为什么使用三层缓存，不能删掉二或者三
- 二级负责保证只有一个对象，C、B只有一个A
- 三级缓存保证真实和代理只存在一个，&& 避免改变顺序
## 用到了哪些设计模式
## bean是单例的吗
有非单例  
区别：
1. 线程安全
2. 创建过程  
还有请求、会话、web
## bean是否线程安全
## spring中有哪些容器
## spring中的事务如何实现的
本质上是AOP代理  
根据切入点的条件设置AOP代理类，调用方法时本质上调用的是代理类，有一个对象专门判断是否符合切点条件，通过ThreadLocal绑定线程和数据库链接
## spring事务注解的机制
## spring事务失效的场景
- 未启动Bean管理
- 未启动事务管理
- 非public
- 底层引擎
- 捕获异常默认只处理运行时异常和Error
- 传播级别设置不当
## spring的扩展点有哪些，具体使用例子
## 如何设计一个spring
- 底层数据结构
- 生命周期
- 扩展点
- 作用域
- aop的实现
- 事务
## 声明式事务@Transactional和编程式事务的对比
自由度，原理，性能，耦合度。  

# springmvc
## mvc分层
- M,模型
- V，视图
- C，控制器

用户通过V和C交互，C操作M
## springmvc的处理流程
- 用户发送请求
- 前端拦截器处理，dispatcherservelet同意拦截处理
- mapperhandler负责查找合适的处理者
- handleradopter负责执行包括了拦截器和调用方法
- 这里按照类型选择json还是调用视图解析器将值set进模板中

# springboot
## spring、mvc、springboot的区别
## springboot一句话讲讲是什么
约定大于配置————自动装配，默认配置，项目结构
作为spring的子集，提供了更便捷的能力。  
主要表现为：
1. 自动装配：解决的是资源发现的问题，对指定位置的jar包扫描并且注册到容器中，利用注解，避免了配置文件
2. starter：解决的是资源打包的问题，将一系列jar包组合，并且内部解决了版本冲突问题，搭配自动装配使用
3. 内嵌Tomcat，无需额外的配置，直接将应用打包为JAR包
## springboot对比spring多了什么
## 常见的starter说说
1. web
2. security
3. mybatis
4. data-jdbc
5. redis
6. test
## springbootstart如何实现
1. 创建maven项目
2. 引入自动装配依赖
3. 配置Configuration，设置好前缀
4. 创建好resource/META-INF/spring.factories
5. 打包
## 自动装配的原理
- 扫描全部Meta-inf下的配置
- 通过条件过滤、去重、排序
- 最终得到key-value
- 导入放入bean容器
## springboot的注解介绍几个
## 过滤器和拦截器介绍一下
- 过滤器是Servlet中的，全局的doFilter中执行
- 拦截器是MVC中的概念dispatch后，Controller前
# mybatis
## mybatis的原理
- 初始化读取xml获取连接参数、mapper.xml，创建config对象，每一个mapper都会和接口对应，生成对应的语句对象
- 根据这个config对象生成sqlsessionfactory
- 每一个session代表一个会话，每一次执行语句createSql
- 经过java和jdbc对象转换、语句拼接参数设置、执行（获取连接池）、获取结果集
- 完成
## mybatis的优点，对比jdbc
- 复用连接
- 模板化
- 解耦
- 扩展机制
## jdbc连接数据库步骤
- 加载
- 创建
- sql语句
- 执行
- 结果集
## mybatis的流程和原理
## mybatis的插件机制
## mybatis的#和$
- 预编译缓存，先编译后传参，另一个是粗暴的设置值
- sql注入
- 类型检查
## mybatis的设计模式
- 责任链
- 工厂
- 适配器
- 模板方法
- 代理
## mybatis是否有线程池？
- 连接复用支持springboot导入的、mybatis的、tomcat
# springcloud
## 和springboot的区别
springboot专注于单个应用的构建，cloud则是在多个微服务之间构建，提供了一些组件，比如负载均衡、服务发现、服务注册、保护、日志、链路追踪。
## 介绍一下组件
- nacos注册中心、配置、服务发现
- feign和dubbo实现服务通信
- gateway网关
- elk日志或者阿里云支持
- seata分布式事务
- sential保护
- redis、mq
## 有哪些负载均衡算法
- 轮询
- 加权轮询
- 随机
- 加权随机
- 一致性哈希
- 资源连接数、负载
## 如何一直均衡给一个用户
一致性hash
## 如何服务熔断
通过http或者rpc通信，如果一个节点出故障，没有容错措施，大量的请求重试会导致雪崩。  
熔断和降级像保险丝一样，避免全部故障
## 如何服务降级
减少非核心功能的资源或者停止非核心功能。   
可以通过sential设置，每秒失败xx，或者失败率到达，失败数有了xx
# 分布式
## cap是什么
- c
- a
- p
常见的cp、ap
## base
作为ap的延申，ap出现以后如何处理
- BA 基本可用性，即保证核心的功能
- S 软状态
- E 最终一致性
可以通过消息队列，定时任务
## 分布式锁
### 需要怎么保证
- 互斥
- 可中断
- 容错
- 可重入

### 为什么需要
### redis实现
- lua结合redis语句实现
- 上锁时 key 用户+操作 setnx expire
- 解锁时判断用户+解锁
### redlock

### 看门狗
### zk实现
## 分布式事务
- 2PC
- saga
- TCC
- 消息队列
## RPC的实现
1. 引入序列化
2. 引入nio
3. 动态代理
4. 服务发现
5. requestID

## ZK的原理
1. znode——锁、配置中心、发现注册中心
2. ZAB协议——
   1. 察觉到自己失联/新加入，进入选举模式
   2. 和所有节点建立连接，感知当前的节点数
   3. 发送节点id、当前轮次、主节点
   4. 选举leader
   5. 多数派统一变成主节点
## 分布式组件
  
## 限流算法
## 一致性算法
