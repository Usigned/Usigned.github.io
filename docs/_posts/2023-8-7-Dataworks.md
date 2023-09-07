# Dataworks

## Overview

>  参考：[MaxCompute/DataWorks 数据集成与开发实践-阿里云开发者社区 (aliyun.com)](https://developer.aliyun.com/article/280236)
>
> 资料较老主要想看看dataworks原始设计和发展，了解数据开发中一些概念

![v2-03f8fcd8cc4cf1d6266974132289f109_r](https://pic2.zhimg.com/v2-03f8fcd8cc4cf1d6266974132289f109_r.jpg)

### 数据集成

将多方面数据聚集，主要难点在于需要兼容多个、多类型数据源和数据中心的多种不同同步方式，同步过程中需要监控等。

![v2-6ab4035dcd09040ab99ce3ea67f100ee_1440w](https://pic3.zhimg.com/80/v2-6ab4035dcd09040ab99ce3ea67f100ee_1440w.webp)

#### 数据集成架构

![v2-d1967a7935def290d0d04064d728528c_1440w](https://pic1.zhimg.com/80/v2-d1967a7935def290d0d04064d728528c_1440w.webp)

### 数据开发

#### 数仓规范

>  数据开发阶段规范化
>
>  类似软件开发生命周期，数据开发生命周期？

![v2-d48dd6387a59d95d42b07718150ed071_1440w](https://pic2.zhimg.com/80/v2-d48dd6387a59d95d42b07718150ed071_1440w.webp)

**工艺流程**

![v2-71786b81862f7667a9d690d7ccfaf13a_1440w](https://pic3.zhimg.com/80/v2-71786b81862f7667a9d690d7ccfaf13a_1440w.webp)

**实现**

![v2-31f1f002ca88e8dc2079833c7122976e_1440w](https://pic3.zhimg.com/80/v2-31f1f002ca88e8dc2079833c7122976e_1440w.webp)

#### 数据模型层次

> ODS, Operation Data Store 数据引入

![v2-67777098d9a24d440773a355faede7b6_1440w](https://pic3.zhimg.com/80/v2-67777098d9a24d440773a355faede7b6_1440w.webp)

### 监控运维

> 运维中心

- 任务管理
- 周期控制