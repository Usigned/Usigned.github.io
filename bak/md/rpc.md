## RPC

>  远程过程调用(Remote Procedure Calls)

相比于本地函数调用，RPC要解决的问题

1. 函数映射
   - 使用id定位函数
2. 数据转成字节流
   - 参数传递
3. 网络传输

### 概念模型

![image-20220524162335853](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20220524162335853.png)

组成部分

- User
- user-stub
- RPC Runtime
- Server-stub
- Server

步骤

1. user本地调用user-stub
2. user-stub打包数据发送给runtime
3. runtime传输数据
4. server-stub解包数据
5. server端本地调用
6. 数据返回参考上述

### 实现

![image-20220524162702163](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20220524162702163.png)

组件

- IDL(Interface Description language)
  - 中立方式来描述接口，来统一不同平台和不同语言
- 生成代码
  - 通过工具将IDL转换为语言对应的静态库
- 编解码
  - 内存中的表示和字节序列间的转换
  - aka 序列化与反序列化

- 通信协议
  - 除了必须的请求、响应外，还携带**元数据**
  - 规范网络中数据传输的内容和格式

- 网络传输
  - 通常基于TCP/UDP

### 为什么要使用RPC?

优点

1. 单一职责，利于分工协作和运维开发
   - 服务间可以使用不同的语言、平台
2. 可扩展性强，资源利用率高
   - 可以针对单一服务进行扩容
3. 故障隔离

问题

- 服务宕机处理
- 网络传输

> 问题由rpc框架解决

## RPC框架

### 分层

![image-20220524164022271](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20220524164022271.png)

> apache thrift

### 编解码层

> 生成代码 + 框架的编解码层

1. 数据格式
   - 语言特定：java.io.Serializable
2. 文本格式
   - JSON, XML, CSV
   - 人类可读
3. 二进制编码（常用）
   - 跨语言
   - Thrift Binary Protocal, protobuf

> TLV编码
>
> - Tage：标签-》类型
> - Length：长度
> - Value：值Value也可以是TLV结构

### 协议层

> - 特殊结束符
>   - http
> - 变长协议

协议构造

- 数据包大小
- 协议类型
- seq number：用于多路复用，包重组
- 头部长度
- 编解码方式
- 压缩方式
- 元数据
- 消息体

协议解析

![image-20220524165613220](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20220524165613220.png)

1. 读取MagicNumber -> 获取协议类型
2. PayloadCodec -> 获取编解码/压缩方式
3. payload解码

### 网络通信

- 使用Sockets Api通信

- 使用现有的网络库
  - 易用APi
  - 支持多种协议，异常处理等
  - 高性能

## RPC框架指标

### 稳定性

1. 降级措施

   - 熔断：保护调用方

   - 限流：保护被调用方

   - 超时控制

2. 请求成功率

   - 负载均衡
   - 重试

3. 长尾请求

   - 备份请求：一种重发策略

   - pc99

4. 注册中间件

   - 以此来添加功能

   - aka middle ware / 拦截器

### 易用性

1. 开箱即用
   - 提供默认配置
2. 周边工具
   - 代码生成
   - 脚手架工具???

### 扩展性

![image-20220524171500167](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20220524171500167.png)

### 观测性

1. Log，Metric，Tracing

2. 内置观测服务

### 性能

1. 高吞吐
2. 低延迟

## 企业实践

>  kitex

### 架构

![image-20220524171928789](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20220524171928789.png)

### 网络库

原生库`go/net`缺点

- 无法感知连接状态，连接池中存在失效连接
- goroutine利用率地下

`netpoll`优化

- epoll主动监听
- goroutine复用
- NoCopy Buffe，编码层零拷贝