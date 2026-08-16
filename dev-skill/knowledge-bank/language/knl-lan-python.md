---
name: knl-lan-python
description: 当用户想要了解 python 语法，这篇文档可能可以提供帮助
disable-model-invocation: true
---

# knl-lan-python

此文主要讲解了 python 语法的一部分容易遗忘的知识内容

## 基本

```python
# 下划线开头通常表示“内部使用”或“私有”
_total = 100

# 双下划线开头，有特殊含义
__private_var

# 字段大小写是敏感的，要注意

# 变量使用前必须赋值

# 允许多个变量赋值
a = b = c = 1
a, b, c = 1, 2, "runoob"
```

注释：

```python
# 单行注释
#

# 多行注释
"""
这是一个多行注释
"""
'''
这是一个多行注释
'''

# 逻辑运算符
# ! → not
# && → and
# || → or
```

## 程序入口和执行

一个模块被另一个程序第一次引入时，其主程序将运行。
如果我们想在模块被引入时，模块中的某一程序块不执行，我们可以用 **name** 属性来使该程序块仅在该模块自身运行时执行

```python
# 每个模块都有一个 __name__ 属性
# 当模块作为主程序运行时：__name__ 的值被设置为 "__main__"。
# 当模块被导入时：__name__ 的值被设置为模块的文件名
if __name__ == '__main__':
    print("这是主程序")
else:
    print('我来自另一模块')
```

## 数据类型

```python
# 6 种标准类型
# Number（数字）int、float、complex
# String（字符串）
# bool（布尔类型）True 和 False
# List（列表）[]
# Tuple（元组） ()
# Set（集合） {}
# Dictionary（字典） {}

# 类型注解，就是有 : 来指定类型，推荐此写法
name: str = "Alice"
# 函数类型注解，即有 ->
def add(a: int, b: int) -> int:
    return a + b
# 其他类型注解
numbers: List[int] = [1, 2, 3, 4, 5]
student_scores: Dict[str, int] = {"Alice": 95, "Bob": 88}

# 列表
my_list = ['abcd', 786, 2.23, 'runoob', 70.2]
my_list[0:4] # index 0 - 4，不含 4
my_list[-1] # 倒数第一个元素
my_list.append('666') # 添加元素

# 元组，与列表类似，不同之处在于元组的元素不能修改
my_tuple = (abcd', 786, 2.23, 'runoob', 70.2)

# 集合，无序的不重复元素序列
my_set = {'Google', 'Taobao', 'Runoob', 'Facebook', 'Zhihu', 'Baidu'}

# 字典，一种映射类型，键(key) : 值(value) 的集合
my_dict = {'name': 'runoob', 'age': 7, 'class': 'first'}
my_dict['name'] = 'runoob.com'

# bytes 不可辨的二进制序列
# 使用 b 前缀创建 bytes 对象，或者使用 bytes() 函数
x = b"hello"
x = bytes("hello", encoding="utf-8")

# 方括号 [] 来截取字符串
var1 = 'Hello World!'
var2 = "Runoob"
print ("var1[0]: ", var1[0]) # H
print ("var2[1:5]: ", var2[1:5]) # noob，左闭右开

# 格式化输出
print ("我叫 %s 今年 %d 岁!" % ('小明', 10))

# 字符串跨多行使用 """
para_str = """这是一个多行字符串的实例
多行字符串可以使用制表符
TAB ( \t )。
也可以使用换行符 [ \n ]。
"""
```

python 中数据类型转化自动发生，无需我们干预，也可显示转化 `y = int(2.8)`

命名空间有：优先 局部 > 全局 > 内置

## 条件 if else

```python
if condition_1:
    statement_block_1
elif condition_2:
    statement_block_2

match subject:
    case <pattern_1>:
        <action_1>
    case <pattern_2>:
        <action_2>
    case _: # 默认
        <action_wildcard>
```

## 循环 for

```python
# 常规 while

# while 配合 else
while <expr>:
    <statement(s)>
else:
    <additional_statement(s)>

# for
sites = ["Baidu", "Google","Runoob","Taobao"]
for site in sites:
    print(site)

# for 整数范围配合 range()
for number in range(1, 6): # 1 - 5，不含 6
    print(number)

# range(0, 10, 3)，0 - 10，步长为 3
for number in range(0, 10, 3):
    print(number)
```

## 函数

```python
# 基本
def function_name(arguments):
    <function_body>
    return <return_value>

# 默认参数
def function_name(arguments, default_argument_value = 1):
    <function_body>
    return <return_value>
```

## lambda 表达式

```python
# lambda 表达式表示的匿名函数
f = lambda: "Hello, world!"
f()

# lambda 表示表示的带 1 个参数的匿名函数
x = lambda a : a + 10
x(3)
```

## 装饰器

在不修改原函数代码的前提下，动态扩展函数或类的功能。
本质上，装饰器是一个函数：它接收一个函数作为参数，并返回一个新的函数（通常是对原函数的增强版本）

Python 还提供了一些内置装饰器，例如 @staticmethod 和 @classmethod

```python
def decorator_function(original_function):
    def wrapper(*args, **kwargs):
        # 调用前
        print("执行前")

        result = original_function(*args, **kwargs)

        # 调用后
        print("执行后")

        return result
    return wrapper

@decorator_function
def target_function():
    print("原函数执行")

# 再一个例子
def my_decorator(func):
    def wrapper():
        print("函数执行前")
        func()
        print("函数执行后")
    return wrapper

@my_decorator
def say_hello():
    print("Hello!")

# 装饰器用于类也可以
def log_class(cls):
    class Wrapper:
        def __init__(self, *args, **kwargs):
            self.wrapped = cls(*args, **kwargs)

        def __getattr__(self, name):
            return getattr(self.wrapped, name)

        def display(self):
            print("调用前")
            self.wrapped.display()
            print("调用后")

    return Wrapper

@log_class
class MyClass:
    def display(self):
        print("原方法")

obj = MyClass()
obj.display()

# 内置装饰器
# @staticmethod：定义静态方法
# @classmethod：定义类方法
# @property：将方法变为属性
```

## 异常
```python
try:
    f = open('myfile.txt')
    s = f.readline()
    i = int(s.strip())
except OSError as err:
    print("OS error: {0}".format(err))
except ValueError:
    print("Could not convert data to an integer.")

# 抛出异常
raise Exception('x 不能大于 5。x 的值为: {}'.format(x))
```

## 类
python 是支持类的多继承的！
```python
class MyClass:
    # 构造函数。self 代表类的实例，而非类
    def __init__(self, param1, param2):
        self.param1 = param1
        self.param2 = param2

# 类的继承
class people:
    def __init__(self, name, age):
        self.name = name
        self.age = age
class student(people):
    def __init__(self, name, age, id):
        super().__init__(name, age)
        self.id = id

# 子类可以重写父类函数

# 类的私有属性和方法，只能在类的内部使用，不可在外部使用
```

## 异步

async 标记的函数返回一个 asyncio 协程对象，而不是直接返回结果。
await 会等待协程对象完成，直到它返回结果。
async 不是标记是否需要异步的函数，而是用来标记一个内部是否有需要异步能力的函数，即内部有 await，所以函数必须加 async

asyncio.gather() 用于并发运行多个协程，等待它们所有完成，最后返回一个列表，列表中的元素是每个协程的结果。
```python
import asyncio
# 定义协程函数
async def task1():
    print("任务1开始")
    await asyncio.sleep(2)  # 模拟耗时操作
    print("任务1完成")
    return "任务1结果"
async def task2():
    print("任务2开始")
    await asyncio.sleep(1)  # 模拟耗时操作
    print("任务2完成")
    return "任务2结果"
# 主协程
async def main():
    # 并发运行多个协程
    results = await asyncio.gather(task1(), task2())
    print("所有任务完成:", results)
# 启动事件循环运行主协程
if __name__ == "__main__":
    asyncio.run(main())
```

asyncio.create_task() 用于创建一个任务对象并立即安排执行，返回一个 Task 对象。调用后协程立即开始执行，await 只是等待阻塞协程返回结果。
```python
import asyncio
async def task1():
    print("任务1开始")
    await asyncio.sleep(2)
    print("任务1完成")
    return "任务1结果"
async def task2():
    print("任务2开始")
    await asyncio.sleep(1)
    print("任务2完成")
    return "任务2结果"
async def main():
    # 创建任务对象，协程立即开始执行
    t1 = asyncio.create_task(task1())
    t2 = asyncio.create_task(task2())
    print("主协程继续执行其他操作")
    # 等待任务完成
    result1, result2 = await asyncio.gather(t1, t2)
    print(f"结果: {result1}, {result2}")
if __name__ == "__main__":
    asyncio.run(main())
```