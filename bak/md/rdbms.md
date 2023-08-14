# RDBMS

> **存储系统**
>
> - 块存储，文件存储，对象存储，k-v存储
>
> **数据库系统**
>
> - **关系型**，非关系型
>
> **分布式架构**
>
> - 数据分布式策略，数据复制协议，分布式事务算法

## 特性

事务

- 一组SQl一个程序执行单元

ACID

- 原子：事务不可分
- 一致性：数据的完整性以及业务逻辑上的一致性
  - 更多偏向业务操作的合法性

- 隔离性：一个事务不影响其他事务执行
- 持久性：事务完成后，更改会持久保存

高并发

- 数据库每秒能处理请求次数

高可靠、高可用

- 数据库不会宕机

## 发展

前DBMS时代

- 人工管理
- 文件系统
  - 数据管理通过文件系统实现
  - 更新、查询低效

DBMS时代

> DBMS数据模型

- 网状模型

  - 每个数据作为节点
  - 父节点和子节点关系为多对多

  > 有点类似知识图谱？

- 层次数据库

  - 树状结构
  - 每个子节点只能有一个父节点

- 关系数据库

  - >  Origin: A Relational Model of Data for Large Shared Data Banks

![image-20220527202209339](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20220527202209339.png)

SQL

- 语法风格接近自然语言
- 高度非过程化
- 面向集合的操作方式

## 关键技术

### SQL执行过程

![image-20220527234818802](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20220527234818802.png)

1. Parser生成AST
2. AST通过Optimizer生成Plan
3. Plan通过Executor执行

### SQL引擎

- Parser：词法分析，语法分析，语义分析
- Optimizer
  - 基于规则优化
  - 基于代价优化
    - 时间、IO、CPU、网络、内存等

- Executor
  - 火山模型
  - 向量化（批处理）
    - 函数调用次数减少
    - 缓存命中更多
    - 充分利用cpu的simd

  - 编译执行
    - 将算子混合在一起，复用
    - LLVM - 动态编译


### 存储引擎

> innoDB为例

![image-20220528000352780](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20220528000352780.png)

**Buffer Pool**

- Buffer pool
- LRU

### 事务引擎

> mysql为例

**原子性**

> 如何实现状态回退？

Undo Log

- 逻辑日志
- 记录增量辩护
- 用于MVCC实现

**隔离性**

- 共享锁（读锁）
- 排他锁（写锁）

![image-20220528002201502](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20220528002201502.png)

MVCC

- 读写互不阻塞
- 降低死锁概率
- 一致性读

**一致性**

Redo Log

- 记录页面变化
- 写入时保证redo log的写入
- 数据写入异常时，根据redo log重做

## 企业实践

挑战

- 流量大
- 流量突增
- 稳定性

**大流量 - sharding**

> 分库分表
>
> 类似负载均衡

- 业务数据水平拆分
- 代理层进行分片路由

**流量突增 - 扩容**

- 扩容DB物理节点
- 使用影子表进行压测

> 扩容：
>
> 1. 数据复制
> 2. 代理配置
> 3. 数据去重

**流量突增 - 代理连接池**

- 业务侧预热连接池
- 代理侧预热连接池
- 代理侧支持连接队列

**稳定性&可靠性**

3AZ部署

- 三机房部署

- 代理：读写分离
- 监控报警

HA管理

- 服务监管，切换节点
- 代理配置热加载
- 代理自动屏蔽宕机读节点

> PMEM：非易失性内存
>
> 查询缓存
>
> - 重复sql查询 -- 缓存结果
>
> 数据缓存 buffer pool
>
> - 缓存page数据
>
> - 将部分page的数据缓存到内存
