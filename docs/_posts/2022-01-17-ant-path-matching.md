---
title: apache ant路径匹配
tags: others
---

起因是在vscode配置中看到了如下的路径匹配字符串`**/*.jar`。以前只知道`*`在正则表达式、shell命令行中匹配任意数量的字符串，还是头一次见两个`*`连用的情况，于是就去查了一下，发现这种路径模式叫Apache Ant-style路径匹配。

![image-20220117224412012](https://cdn.jsdelivr.net/gh/Usigned/pic-typora@main/images/image-20220117224412012.png)

Apache Ant样式的路径三种通配符

| 路径 | 描述                  |
| ---- | --------------------- |
| ?    | 匹配任何单字符        |
| *    | 匹配0或任意数量字符   |
| **   | 匹配0或任意数量的目录 |

举例

| Path                    | 作用                                                         |
| ----------------------- | ------------------------------------------------------------ |
| `**/CVS/*`              | 目录树中任意位置的CVS目录下的所有文件，匹配CVS/Repository, org/apache/CVS/Entries, org/apache/jakarta/tools/ant/CVS/Entries, 不匹配org/apache/CVS/foo/bar/Entries（foo/bar/ 部分不匹配） |
| `org/apache/jakarta/**` | 匹配org/apache/jakarta下的所有目录树，匹配org/apache/jakarta/tools/ant/docs/index.html, org/apache/jakarta/test.xml，不匹配org/apache/xyz.java（没有Jakarta/部分） |
| `org/apache/**/CVS/*`   | 匹配org/apache目录中的任意CVS目录中的目录树，匹配org/apache/CVS/Entries org/apache/jakarta/tools/ant/CVS/Entries，不匹配org/apache/CVS/foo/bar/Entries（foo/bar/部分不匹配） |
| `**/test/**`            | 匹配只要路径中有test元素的所有文件，包括test做文件名         |

参考资料：

1. [Ant-style的匹配原则_GuXiangFly的博客-CSDN博客_ant-style](https://blog.csdn.net/my__Sun_/article/details/76444435)
2. [AntPathMatcher (Spring Framework 5.3.15 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/util/AntPathMatcher.html)
3. [java - Ant path style patterns - Stack Overflow](https://stackoverflow.com/questions/2952196/ant-path-style-patterns)

4. [Directory-based Tasks (apache.org)](https://ant.apache.org/manual/dirtasks.html#patterns)