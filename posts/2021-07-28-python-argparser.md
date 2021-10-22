---
title: python argparse
tags: [python]
---

### **给python文件添加命令行运行参数**

[python官网教程]([Argparse Tutorial — Python 3.9.6 documentation](https://docs.python.org/3/howto/argparse.html#id1))

[官网详细文档](https://docs.python.org/3/library/argparse.html#module-argparse)

### 基本使用

```python
import argparse
parser = argparse.ArgumentParser()
parser.parse_args()
```

- 不给定任何参数时和原本运行效果一样
- 自带`--help`，给定除`--help/-h`以外的参数时会报错

### 位置参数(positional args)

```python
import argparse
parser = argparse.ArgumentParser()
paser.add_argument("echo", help="echo the string you use here")
paser.add_argument('square', help="input a number to be squared", type=int)
args = parser.parse_args()
print(args.echo)
print(args.square ** 2)
```

- 位置参数在运行时必须给定
- `add_argument()`规定了运行时需要接收位置参数，`type`默认为`str`，`help`在`-h`时显示

- `paser.parse_args()`方法的返回值用于提取输入的参数

### 可选参数(optional args)

```python
import argparse
parser = argparse.ArgumentParser()
#verbose: 冗余的
parser.add_argument('-v', '--verbosity', help='increase output verbosity',
                    action='store_true')
args = parser.parse_args()
if args.verbosity:
    print('verbosity turned on')
```

- 可选参数运行时可以指定或不指定
- 使用`--`指定可选参数`action='store_true'`代表在给定时将`args`中的值设为`True`，否则为`False`

- 可用`-`指定简写，简写不会添加到`args`中

### 位置参数与可选参数组合

```python
# ...
parser.add_argument('square', help="input a number to be squared", type=int)
parser.add_argument('-v', '--verbosity', type=int, help='balabala')
args = parser.parse_args()
```

如何使用：

```shell
python3 prog.py 4 -v 1
```

- 4赋值给`args.square`，1赋值给`args.verbosity`
- 不给`-v`选项，`args.verbosity`的值默认为`None`
- 给了`-v`选项后就必须给出其值

**限定参数范围**

```python
parser.add_argument('-v', '--verbosity', type=int, choices=[0, 1, 2],
                    help='balabala')
```

- `-v`后的值必须是`choices`中的一个，否则会报错

**使用`count`**

```python
parser.add_argument('-v', '--verbosity', action='count',help='balabala')
```

- `action='count'`统计某个参数出现的次数

```shell
python prog.py -vv
python prog.py --verbosity --verbosity
```

- 两种都会把`args.verbosity`的值设为2

- 不给`-v`则为`None`，可通过`default`设定默认值

```python
parser.add_argument('-v', '--verbosity', action='count', default=0, 
                    help='balabala')
```

### Advanced

```python
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("x", type=int, help="the base")
parser.add_argument("y", type=int, help="the exponent")
parser.add_argument("-v", "--verbosity", action="count", default=0)
args = parser.parse_args()
answer = args.x**args.y
if args.verbosity >= 2:
    print("Running '{}'".format(__file__))
if args.verbosity >= 1:
    print("{}^{} == ".format(args.x, args.y), end="")
print(answer)
```

#### **Conflicting options**

用于两个参数不能同时给定, `add_mutually_exclusive_group()`

```python
import argparse

parser = argparse.ArgumentParser()

group = parser.add_mutually_exclusive_group()
group.add_argument("-v", "--verbose", action="store_true")
group.add_argument("-q", "--quiet", action="store_true")

parser.add_argument("x", type=int, help="the base")
parser.add_argument("y", type=int, help="the exponent")
args = parser.parse_args()
answer = args.x**args.y

if args.quiet:
    print(answer)
elif args.verbose:
    print("{} to the power {} equals {}".format(args.x, args.y, answer))
else:
    print("{}^{} == {}".format(args.x, args.y, answer))
```

- `-vq`或其他同时使用二者的命令会给出错误提示
- `-h`中显示信息为`[-v | -q]`表示二者不能共存