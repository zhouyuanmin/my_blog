---
title: python高阶用法
categories: 后端
tags: python
date: 2020-02-22 17:17:20
updated: 2020-02-22 17:17:20
---

## python高阶用法

### lambda匿名函数

```python
func=lambda x, y: x+y
func(3,4)  # 7
```

### 函数式编程

map 会根据提供的函数对指定序列做映射

```
# map(func, iterable) --> map object  # 返回的是迭代器对象
```

reduce 函数会对参数序列中元素进行累积

```
# reduce(function, sequence, initial=None)
from functools import reduce
reduce(lambda x, y: x+y, [1, 2, 3, 4, 5])  # ((((1+2)+3)+4)+5)
```

filter内置函数用于过滤序列，过滤掉不符合条件的元素，返回由符合条件元素组成的新列表。

```
# filter(function, iterable)
list(filter(lambda x: x % 2 == 0, [1,2,3,4,5,6,7,8,9,10]))  # [2, 4, 6, 8, 10]
```

sorted函数对所有可迭代的对象进行排序操作。

```
# sorted(iterable, key=None, reverse=False)
sorted([[3,4],[2,1],[5,3],[7,4],[9,0]], key=lambda x:x[0])
```

### 闭包

闭包的两个作用：“读取函数内部的变量”和“让函数内部的局部变量始终保持在内存中”

```
def add(x, y):  # x,y,m,n 就是被保存在函数内部的变量
    m, n = 1, 2

    def f(z):
        return x + y + z + m + n

    return f


d = add(5, 6)
d(9)  # 5+6+9+1+2=23
d(1)  # 5+6+1+1+2=15

```

### 解析式

```python
# 列表解析式
[i**2 for i in range(10) if i % 2 == 0]  # [0, 4, 16, 36, 64]
# 字典解析式
{i: i**2 for i in range(10) if i % 2 == 0}  # {0: 0, 2: 4, 4: 16, 6: 36, 8: 64}
# 集合解析式
{i for i in [2,2,3,3,4,4]}  # {2, 3, 4}
```

### enumerate

```python
# enumerate(iterable, start=0)
for i, num in enumerate(['1', '2', '3'], start=1):
    print(i, num)
```

### 迭代器与生成器

迭代器指的是可以使用next()方法来回调的对象，可以对可迭代对象使用iter()方法，将其转换为迭代器。

next()只能往后进行访问。

生成器是一个返回迭代器的函数，只能用于迭代操作，更简单点理解生成器就是一个迭代器。

使用了yield的函数被称为生成器。

```
import sys


def fibonacci(n):  # 生成器函数 - 斐波那契
    a, b, counter = 0, 1, 0
    while True:
        if counter > n:
            return
        yield a
        a, b = b, a + b
        counter += 1


f = fibonacci(10)  # f 是一个迭代器，由生成器返回生成

while True:
    try: 
        print(next(f), end=" ")
    except StopIteration:
        sys.exit()

```

### 装饰器

装饰器的作用：抽离大量和函数功能本身无关的代码进行重用

```python
def logging(level):
    def wrapper(func):
        def inner_wrapper(*args, **kwargs):
            print(
                "[{level}]: enter function {func}()".format(
                    level=level, func=func.__name__
                )
            )
            return func(*args, **kwargs)

        return inner_wrapper

    return wrapper


@logging(level="INFO")  # 如果没有使用@语法，等同于  say = logging(level='INFO')(say)
def say(something):
    print("say {}!".format(something))


@logging(level="DEBUG")
def do(something):
    print("do {}...".format(something))


if __name__ == "__main__":
    say("hello")
    do("my work")
```

