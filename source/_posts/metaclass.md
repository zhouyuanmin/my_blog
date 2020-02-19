---
title: 元类编程
categories: 后端
tags: python
date: 2020-02-19 12:10:52
updated: 2020-02-19 12:10:52
---

## 元类编程

### 元类

元类（metaclass）是 Python 中一个高级概念，它是一种特殊的类，负责创建和定义其他类。

对象是类的实例，而类是元类的实例。

### type

类是如何产生的？表面上使用继承创建一个类，所有类都直接或间接继承于object，而真正创建类的是type

### 用type创建类

type()需要接受三个参数

- 1.类的名称：若不指定也要传入空字符串
- 2.父类：注意以tuple的形式传入，没有也要传入空tuple：()，默认的是object
- 3.绑定的方法或属性：注意以dict的形式传入

```python
# 准备一个方法
def say(self):
    print("hello")


# 使用type来创建一个类
Person = type("Person", (object,), {"name": "person", "say": say})

p = Person()

p.say()  # hello

```

## 元类的主要用途

控制类创建过程、动态创建类、实现设计模式、代码检查和验证、自动注入代码

### 控制类创建过程

使用元类，可以在类的创建过程中更改类的定义，例如添加、修改或删除属性和方法。其机制主要是通过重写元类的特殊方法`__new__`和`__init__`。这可以实现对类的高度定制。

```
# 示例:创建一个UpperAttrMetaclass元类，它将类的所有属性名和方法名变为大写形式
class UpperAttrMetaclass(type):
    def __new__(cls, name, bases, dct):
        """将属性名转换为大写"""
        uppercase_attrs = {}
        for attr_name, attr_value in dct.items():
            if not attr_name.startswith("__"):
                uppercase_attrs[attr_name.upper()] = attr_value
            else:
                uppercase_attrs[attr_name] = attr_value
        # 使用修改后的属性创建新的类
        return super().__new__(cls, name, bases, uppercase_attrs)


# 使用 UpperAttrMetaclass 元类创建一个新类
class TestClass(metaclass=UpperAttrMetaclass):
    def __init__(self):
        pass

    test_attr = "hello"

    def aaa(self):
        return "aaa"


# 测试 TestClass 中的属性名是否已转换为大写
obj = TestClass()
print(obj.TEST_ATTR)  # 输出: hello
print(obj.AAA())  # 输出: aaa

```

### 动态创建类

元类动态创建类的机制主要是通过在运行时调用元类（通常是`type`或自定义元类）并传入类的名称、基类（继承的类）和类字典（包含属性和方法）来实现的。

这使得我们可以根据程序的需要在运行时生成新的类，例如根据数据库模式自动生成 ORM 类。

```
# 定义一个简单的基类
class BaseClass:
    def greeting(self):
        print("Hello from BaseClass")


# 动态创建一个名为 "DerivedClass" 的类，继承自 BaseClass
DerivedClass = type(
    "DerivedClass", (BaseClass,), {"say_hi": lambda self: print("Hi from DerivedClass")}
)

# 创建 DerivedClass 的实例并调用其方法
obj = DerivedClass()
obj.greeting()  # 输出: Hello from BaseClass
obj.say_hi()  # 输出: Hi from DerivedClass

```

### 实现设计模式

元类实现设计模式的机制主要是通过在类的创建过程中控制类的实例化和行为。

```
# 单例设计模式
class Singleton(type):
    _instances = {}

    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._instances[cls] = super().__call__(*args, **kwargs)
        return cls._instances[cls]


class MyClass(metaclass=Singleton):
    pass


# 测试单例模式
obj1 = MyClass()
obj2 = MyClass()
print(obj1 is obj2)  # 输出: True

```

### 代码检查和验证

代码检查和验证的机制是通过在类的创建过程中对类的属性和方法进行检查，确保它们满足某些约束或编码规范。我们可以在元类中重写`__new__`或`__init__`方法，在创建类时对类的定义进行检查和验证。

```
class LowercaseAttrMetaclass(type):
    def __init__(cls, name, bases, dct):
        for attr_name in dct:
            if not attr_name.startswith('__'):
                if not attr_name.islower():
                    raise ValueError(f"属性名 '{attr_name}' 必须为小写")

        super().__init__(name, bases, dct)


# 使用 LowercaseAttrMetaclass 元类创建一个新类
class MyClass(metaclass=LowercaseAttrMetaclass):
    valid_attr = "This is valid"
    # Invalid_Attr = "This is invalid"  # 取消注释将引发 ValueError


# 测试 MyClass
obj = MyClass()
print(obj.valid_attr)  # 输出: This is valid

```

### 自动注入代码

自动注入代码的机制是在类创建过程中修改类的定义，例如添加新的属性或方法。我们可以在元类中重写`__new__`或`__init__`方法，以在创建类时自动注入代码。

```
class InjectedMethodMetaclass(type):
    def __init__(cls, name, bases, dct):
        # 定义一个公共方法
        def common_method(self):
            print("这是一个公共方法，由元类注入")

        # 将公共方法注入到类中
        cls.common_method = common_method

        super().__init__(name, bases, dct)


# 使用 InjectedMethodMetaclass 元类创建两个新类
class MyClassA(metaclass=InjectedMethodMetaclass):
    pass


class MyClassB(metaclass=InjectedMethodMetaclass):
    pass


# 测试 MyClassA 和 MyClassB 中的公共方法
obj_a = MyClassA()
obj_a.common_method()  # 输出: 这是一个公共方法，由元类注入

obj_b = MyClassB()
obj_b.common_method()  # 输出: 这是一个公共方法，由元类注入

```

