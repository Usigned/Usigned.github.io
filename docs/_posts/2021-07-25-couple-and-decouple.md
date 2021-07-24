---
title: 耦合和解耦
---

### 耦合

耦合代表着模块间的关联关系。

大多数耦合产生于业务逻辑的需求，是为我们所需要的。对于这部分耦合应该保留甚至强化：如将隐式的变为显示的，松散的变为内聚。具体做法：有使用类似字典的数据结构将双方绑定、使用全局变量关联数据等。

对于少部分不合理的耦合（模块间不合理的依赖关系），如依赖于外部模块不稳定的行为、模块间双向依赖、修改全局变量而影响其他模块等行为，我们需要对其进行解耦以加强代码的稳定性和可扩展、可以移植性。

### 解耦原则

1. 让模块保持完整且独立

   具体表现为：

   - 对内有完整的逻辑，所依赖的外部资源尽量是不变量
   - 对外提供稳定的特性

   实例：

   一个人要读书：

   ```
   Person person = new Person();
   person.readBook(book);
   ```

   而其中`readBook`操作需要戴眼镜

   ```java
   class Person {
       // ...
       void readBook(Book book) {
           wearGlasses(this.myGlasses);
           read(book);
       }
   }
   ```

   此时如果person没有眼镜(`this.myGlasses`是`null`)，`person.readBook(book)`则会出现异常

   **优化1：属性注入**

   在调用`readBook`前对`person`中的创建眼镜

   ```java
   person.myGlasses = new Glasses();
   person.readBook(book);
   ```

   但这就给`readBook`方法加入了一个隐式要求：调用前先需要对`myGlasses`赋值，给程序员带来了额外的记忆负担。

   **优化2：通过构造函数注入**

   为`Person`构造函数中加一个`glasses`参数

   ```java
   class Person {
   	Person(Glasses glasses) {
           this.myGlasses = glasses;
       }
   }
   ```

   每次创建`Person`对象时都会强制要求传入一个`glasses`，减轻了程序员的记忆要求。但是对于`Person`中的许多方法`glasses`并不是必须的，专为了可能并不会被使用的`readBook`方法就要求`Person`在初始化时需要传入一个`glasses`，十分浪费且不方便。自己方法的特殊需求应该由方法自己解决。

   **优化3：通过普通成员函数注入**

   将构造方法中的参数移动到`readBook`自己的参数列表中。

   ```java
   class Person {
       //...
       void readBook(Book book, Glasses glasses) {
           wearGlasses(glasses);
           read(book);
       }
   }
   ```

   在调用时则使用

   ```java
   person.readBook(book, new Glasses());
   ```

   这样解决了优化2中的诸多不便，但是每次调用都`new`一个眼镜有些浪费，且依赖于外部的眼镜对象可能导致不稳定行为。

   **优化4：封装注入**

   `person`每次可以使用自己持有的眼镜

   ```java
   person.readBook(book, this.myGlasses)
   ```

   但是回到最开始的问题，`this.myGlasses`可能为空。我们可以通过封装一个`get`方法来解决这个问题。

   ```java
   class Person {
       //...
       Glasses getGlasses() {
           if (this.myGlasses == null)
               this.myGlasses = new Glasses();
           return this.myGlasses;
       }
       void readBook(Book book) {
           wearGlasses(getGlasses());
           read(book);
       }
   }
   ```

   这样每次`readBook`都会复用同一副眼镜而且不会影响`person`中其他函数。

2. 保持模块间的连接稳定

   当外部需求方法变化时，连接模块间的接口应该保持不变，让变化发生在模块内部而不是对外的接口上。这需要架构师的提前预判和设计，改变接口的代价要远大于保持变化在模块内部的代价。