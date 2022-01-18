---
Title: 使用命令行运行java程序
Tags: java
---

python程序能够很容的从命令行运行，只用`python fileName.py`就可以运行了。而用命令行运行java程序就不是这么容易了。其主要繁琐的地方有两个：

1. 需要在运行的文件前加包名
2. 需要配置classpath

产生这些复杂性的原因主要是java和python管理环境的方式（也有部分java实现是半编译型的原因）。

python将所有环境都装在同一个解释器的环境中，所有使用这个解释器的项目都会共享这个环境。python标准实现是一个解释器，直接运行源代码文件即可。

> 这样做的安装起来很方便，但是当不同的项目需要不同的环境且各个环境依赖间相互冲突时就会很麻烦，所以一般使用python时，大家都会使用pyenv, conda等工具来管理环境。

java中则通过设置classpath来对每个java程序指明所使用的环境。记得之前在安装java时还需要配置一个叫CLASS_PATH的环境变量，每次使用java命令就都会从该目录下读取环境，否则java命令默认是从当前目录(.)读取环境。同时由于java程序运行之前，需要通过javac命令先将.java文件(文本)编译为.class文件(字节码)，使用java命令运行的是编译后的字节码文件没有直接运行源文件那么直观、方便。同时java运行程序还需要加上包名，并确保对应的字节码文件在对应的目录层级下。

## 命令行运行java文件

新建一个目录test，新建一个HelloWorld.java文件

```java
package com.lzq.demo;

public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("HelloWorld");
    }
}
```

此时的目录结构是

```shell
$ tree .
.
└── HelloWorld.java
```

1. 使用javac编译

```shell
$ javac HelloWorld.java
```

会生成HelloWorld.class文件，不会因为目录和包路径对不上报错

```shell
$ tree .
.
├── HelloWorld.class
└── HelloWorld.java

0 directories, 2 files
```

2. 使用java运行

```shell
$ java HelloWorld             
错误: 找不到或无法加载主类 HelloWorld
原因: java.lang.NoClassDefFoundError: com/lzq/demo/HelloWorld (wrong name: HelloWorld)
```

此时会报错找不到子类，因为此时HelloWorld.class文件不在指定的目录结构下。在当前目录下按照包名建立目录结构，之后将class文件移动到此目录下。

```shell
$ mkdir -p com/lzq/demo
$ mv HelloWorld.class com/lzq/demo
```

之后再尝试运行，提示无法找到主类，原因是移动后的class文件找不到了

```shell
$ java HelloWorld                 
错误: 找不到或无法加载主类 HelloWorld
原因: java.lang.ClassNotFoundException: HelloWorld
```

加上包名再次尝试运行，运行成功。

```shell
$ java com.lzq.demo.HelloWorld
HelloWorld
$ java -cp "." com.lzq.demo.HelloWorld
HelloWorld
```

此时如果没有设置CLASS_PATH环境变量，会默认设置为当前目录。

也可以切换到别的目录然后指定classpath运行

```shell
$ cd ~
$ java -cp "/User/lzq/Desktop/test" com.lzq.demo.HelloWorld
```

3. 引用第三方jar包

   在test目录下再新建一个目录`lib`，同时加入一个网上下载的jar包

   ```shell
   $ tree .
   .
   ├── HelloWorld.java
   ├── com
   │   └── lzq
   │       └── demo
   │           └── HelloWorld.class
   └── lib
       └── mysql-connector-java-8.0.27.jar
   ```

   为了在运行时能够使用该jar包，需要在classpath中添加该包路径。

   `-cp`参数使用`:`分割路径

   ```shell
   $ java -cp ".:lib/*.jar" com.lzq.demo.HelloWorld
   ```

## 参考资料

1. [java - What does "Could not find or load main class" mean? - Stack Overflow](https://stackoverflow.com/questions/18093928/what-does-could-not-find-or-load-main-class-mean)
2. [java 命令文档(oracle)](https://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html)
3. [Setting the class path (oracle.com)](https://docs.oracle.com/javase/7/docs/technotes/tools/windows/classpath.html)
4. [PATH and CLASSPATH (The Java™ Tutorials > Essential Java Classes > The Platform Environment) (oracle.com)](https://docs.oracle.com/javase/tutorial/essential/environment/paths.html)

