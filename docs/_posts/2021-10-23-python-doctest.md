---
title: python doctest
tags: [python]
---

python doctest基本使用方法

[官方文档](https://docs.python.org/3/library/doctest.html)

# 测试用例语法

```python
'''
基本语法： >>> 接命令行测试用例 ...接多行输入 换行接预期结果
>>> 命令行测试输入
... 	多行输入
... 	多行输出
预期结果

预期结果识别范围为上一个>>>/...结束到下一个>>>开始或遇见一个空行
>>> 命令行测试输入
...		多行输入
预期结果
>>> 命令行测试输入
预期结果

若测试用例输出结果为空行需要用<BLANKLINE>替代
>>> 结果为空行的测试用例
<BLANKLINE>

异常测试：预期结果需要以Traceback (most recent call last):
或Traceback (innermost last):起头
具体traceback stack可以写但一般适用...省略
异常类型和报错信息需要提供
>>>	含异常的测试用例
Traceback (most recent call last):
	...
异常类型: 报错信息
'''
```

# Demo

官方demo中文注释版

```python
"""
“example”模块

此事例模块提供一个方法，factorial()，其使用方法如下
>>> factorial(5)
120
"""

def factorial(n):
    """返回n的阶乘，要求n>=0且数值为整数

    >>> [factorial(n) for n in range(6)]
    [1, 1, 2, 6, 24, 120]
    >>> factorial(30)
    265252859812191058636308480000000
    >>> factorial(-1)
    Traceback (most recent call last):
        ...
    ValueError: n must be >= 0

    浮点型输入可以，但数值必须为整数
    >>> factorial(30.1)
    Traceback (most recent call last):
        ...
    ValueError: n must be exact integer
    >>> factorial(30.0)
    265252859812191058636308480000000

    n不能过大
    >>> factorial(1e100)
    Traceback (most recent call last):
        ...
    OverflowError: n too large
    """

    import math
    if not n >= 0:
        raise ValueError("n must be >= 0")
    if math.floor(n) != n:
        raise ValueError("n must be exact integer")
    if n+1 == n:  # catch a value like 1e300
        raise OverflowError("n too large")
    result = 1
    factor = 2
    while factor <= n:
        result *= factor
        factor += 1
    return result


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

# 使用方法

1. 直接在文件中使用文档字符串，然后将

   ```python
   if __name__ == "__main__":
       import doctest
       doctest.testmod()
   ```

   加入模块中，之后执行

   ```shell
   python example.py -v
   ```

   或者直接

   ```shell
   python -m doctest example.py -v
   ```

   > -v参数控制是否输出通过的用例

2. 将测试用例放入文件中，然后单独调用

   ```python
   import doctest
   doctest.testfile("example.txt")
   ```

   或

   ```shell
   python -m doctest example.txt
   ```

   > 此时测试用例和新启动repl一样需要先import模块

   ```
   The ``example`` module
   ======================
   
   Using ``factorial``
   -------------------
   
   This is an example text file in reStructuredText format.  First import
   ``factorial`` from the ``example`` module:
   
       >>> from example import factorial
   
   Now use it:
   
       >>> factorial(6)
       120
   ```

   