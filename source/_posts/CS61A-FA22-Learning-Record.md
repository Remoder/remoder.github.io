---
title: CS61A-FA22 Learning Record
date: 2023-06-27 16:05:18
tags: [CS, Python]
description: 记录 CS61A-FA22 的学习过程。
abbrlink: CS61A-Learning-Record
cover: /img/CS61A-Learning-Record.png
categories:
---

# 前言&资源

CS61A 是 Berkeley 大学针对其大一学生所开设的课程，并在互联网上做出了相当程度的透明分享。

[此处](https://inst.eecs.berkeley.edu/~cs61a/archives.html) 为 CS61A 课程汇总。

非校内学生存在一定局限性，其中无法访问的 Recording 可以在 Y2B 或 Bilibili 中寻找，Solution 可以在 Gtihub 中寻找。

课程中的 Lab 采用了 `ok` 系统进行检测，非其校内学生可以采用 `python3 ok --local -u` 的命令进行本地测试，命令 `python3 ok --local -q [question number] -i` 用于对某个问题进行本地测试。

建议学习顺序为 `Textbook - Lecture - Lab & Disscussion - Homework & Project`。 

为同时学习 Linux 系统，笔者借助 [WSL](https://learn.microsoft.com/zh-cn/windows/wsl/about)，于 Win10 中运行 Linux 进行课程学习。

# 学习记录
## Week 1
### I. Python 中的元素(Elements)
Python 中的元素包括但不限于：

- 表达式(Expressions)  
    符号表达式(Mathematical Expressions)，如 `1 + 2 / 3 * 4 // 5 % 6 ** 7`  
    调用表达式(Call Expressions)，如 `max(min(1, -2), min(pow(3, 5), -4))`

- 函数(Functions)   
    纯函数(Pure Function)，需满足两点：  
    - 函数的结果只与参数有关，即相同参数必然返回相同结果；  
    - 在函数执行过程中，没有其他任何操作，也造不成任何影响。
    
    非纯函数(Non-Pure Function)，会对其他元素造成影响的函数。  

    不难发现，纯函数具有很强的优越性：它与调用表达式工作更加融洽，它输出固定易于调试，它不受外部影响、更利于并行处理。

- 变量名(Names)与环境(the Environment)  
    Python 代码运行于环境之中，而环境包含了许多框架。
    
    在不同框架中，可以存在相同的变量名而互不影响，即**局部变量**、**全局变量**。  

    在调用函数时，便会创立新的框架(Frame)，并优先在新建框架中检索变量，若无法找到，再返回**父框架**(见下文)。可以发现，此处是**栈**的思想。  

    因而，  

    ```python
    def square(square):
        return square * square
    ```

    这样的代码是可行的，具体可见下段内容的分析。
    

### II. 环境图(Environment Diagram)    
利用图示方式，将**变量名与框架**直观展现出来，即称为**环境图**。  

于 [环境图生成网站](https://pythontutor.com/python-debugger.html#mode=edit) 中运行代码  

```python
def square(square):
    return square * square
square(-2)
```

即可感受上文所提的**框架栈**。

### III. 嵌套调用表达式(Nested Call Expressions)的运行
Python 首先识别表达式的操作命令，即 `add mul` 等，而后去访问算子，亦即函数中的参数。若算子仍为表达式，则继续识别操作命令，而后访问算子，直到两个算子都为数字为止。而后返回计算结果，返回计算结果，...，返回计算结果，最终得到答案。    
不难发现，这是一个递归的过程。若将每一步都展开并画图示意，则可构建出一棵树，称为**表达式树**(Expression Tree)。

### 布尔运算符(Boolean Operators)的性质
- not  
    返回表达式的相反结果，`True` 或 `False`。
- and  
    返回第一个为 `False` 的值；  
    若无 `False`，返回最后一个值。
- or  
    返回第一个为 `True` 的值；
    若无 `False`，返回最后一个值。

## Week 2
### I. 抽象(Abstraction)的概念
首次接触这个词是在课程 [Crash Course Computer Science](https://www.bilibili.com/video/BV1EW411u7th) 中。

课程从电子元件将起，将晶体管的不同组合**抽象**成逻辑门，将逻辑门的组合**抽象**成加法器，将加法器和门的组合**抽象**成逻辑运算单元······

而抽象的好处，即设计时不需要再考虑底层，直接将其作为整个“模块”进行使用，从而更好地进行工作开展。

Python 中也蕴含着许多**抽象**。如，`a = 3` 这样的赋值语句将数据抽象为变量名，`def max()` 这样的定义语句将寻找最大值功能抽象为函数。

函数的抽象使我们不必考虑其内部结构，而只需要考虑三个维度，即：定义域(Domain)，值域(Range)，映射(Intent)。其分别代表了参数的范围、返回值的范围、参数与返回值的关系。

正是这样的抽象，使得我们在编写代码时会更加流畅，不需要考虑具体的值，不需要在意函数的具体实现方式 ——— 拿来用就是了！

### II. 再谈环境(Environment)与框架(Frame)
**用户自定义函数(User-defined Function)** 的内部语句不会在定义时被执行，每当其被调用时，就会创造一个 `Local Frame`，框架内将会先进行形参赋值，而后被率先执行函数内部语句。

之前提到过，一个环境中会存在许多框架。而具体来讲，不同的框架组合构成了不同的环境。一个环境要么仅包含一个 `Global Frame`，要么包含数个 `Local Frame` 和一个 `Global Frame`。

举例来说，最初的环境中仅有 `Global Frame` 这一个框架。而调用函数后，生成了新的框架 `New Frame`。此时我们便称，得到了一个包含 `Global Frame` 和 `New Frame` 两个框架的新环境。

这段代码

```python
from operator import mul
def square(x):
    return mul(x, x)
square(square(3))
```

所生成的**环境图**如下，其中便包含了三个环境。

![](CS61A-FA22-Learning-Record/Week2-Environment-Diagram-with-3-Environments)  

相同的变量名可以存在于同一个环境之中，但必须在不同的框架之内。每当寻找变量值时，会首先在最新框架中寻找，若找不到，再返回上一框架。


### III. 内在名称(Intrinsic Name)与绑定名称(Bound Name)
诸如 `f, g = max, min` 这样的语句说明，不同的名称有可能代表相同的函数，这样的名称被称之为 **绑定名称(Bound Name)** 。  

而在函数创建之初，会存在一个唯一的名称，始终保持不变，以指向这个函数，此名称即称为 **内在名称(Intrinsic Name)** 。

### IV. 变量名选取准则
选取好的变量名、函数名与参数名是提高代码可读性的基础，以下是一些建议：  

1. 函数名小写，单词之间用下划线分割，描述性的名字更好。
2. 函数名要尽可能表明功能，阐述其操作(e.g. print)或结果(e.g. square)。
3. 参数名小写，单词之间用下划线分割，并尽可能减少单词数量。
4. 参数名要尽可能表面参数在函数内起到的作用。
5. 尽可能避免单个字母的混淆，如 `I l 1 O 0`。
6. 布尔值变量的命名一般以 `is` 开头，如 `is_finite, is_digit, is_instance`。

### V. 函数设定原则
为便于调用，函数的设定也应有一定的规则，如：  

1. 一个函数应只解决一个问题、只有一种功能。而这样的功能应该尽可能简洁，简洁到可以用一句话描述。
2. 函数应尽可能被多次调用。当自己在复制粘贴自己的代码时，就应该考虑构建函数了。
3. 函数应尽可能解决普遍的问题。即当 `pow()` 函数能够涵盖 `square()` 函数时，前者便应该得到推崇。
4. 函数的参数尽可能设立默认值，以便于调用。

此外，函数应包含**文档描述(Docstring)** 。文档描述位于函数体中，用三引号引起，其中的第一行用于说明函数的功能，其后可以解释原理、解释参数等，最好能够指明各参数的数据类型。

如：
```python
def pressure(v, t, n):
    """Compute the pressure in pascals of an ideal gas
    
    v -- volume of gas, in cubic meters
    t -- absolute temperature in degrees kelvin
    n -- particles of gas
    """

    k = 1.38e-23 # Boltzmann's constant
    return n * k * t / v
```

当使用 `help(pressure)` 的命令时，即可查阅 `pressure()` 函数的 `docstring`。

上示代码中，`# Boltzmann's constant` 即为注释(Comments)，用以读者理解，不会被编译。

### VI. 函数的调试
1. Assertion.  
    使用 `assert fib(8) == 13, 'The 8th Fibonacci number should be 13'` 语句。  
    当 `fib(8) != 13` 时，后面的文本便会显示。
2. Doctests.   
    此处便是上文提到的**文档描述**，其中可以加入调用命令和期望结果，以进行测试。

    如：
    ```python
    def sum_naturals(n):
        """Return the sum of the first n natural numbers.

        >>> sum_naturals(10)
        55
        >>> sum_naturals(100)
        5050
        """

        total, k = 0, 1
        while k <= n:
            total, k = total + k, k + 1
        return total
    ```

    采用以下代码

    ```python
    from doctest import testmod
    testmod()
    ```

    或

    ```python
    from doctest import run_docstring_examples
    run_docstring_examples(sum_naturals, globals(), True)
    ```

    或在命令行中 `python3 -m doctest <python_source_file>` 都可以此进行函数的检测。

    这样对单个函数的检测，即被称为**单元测试(Unit Test)** 。
    
### VII. 高阶函数(Higher-Order Functions)
高阶函数即**将函数作为参数或返回值**的函数。

#### 将函数作为参数 
最基本的形式如下：

```python
def summation(n, term):
    total, k = 0, 1
    while k <= n:
        total, k = total + term(k), k + 1
    return total

def cube(x):
    return x*x*x

result = summation(3, cube)
```

在这个基础上，求得某序列的前 n 项和便很容易了：

```python
def pi_term(x):
    return 8 / ((4*x-3) * (4*x-1))

result = summation(n, pi_term)
```

由此可见，`summation` 将**某序列的前 n 项和**的函数功能进一步抽象，仅仅实现了**前 n 项和**的功能。而后借助其他函数，如上文中的 `cube` `pi_term` 函数，实现**某序列**的功能。

接下来，再来看一个例子，以便更好地理解这样的函数，即**普适函数(Functions as General Methods)** ，是如何独立于具体的特定函数之外，将通用的计算方式表达出来的。 

```python
def improve(update, close, guess=1):
    while not close(guess):
        guess = update(guess)
    return guess
```

函数 `improve` 并没有指明具体的计算方法与目标值，仅仅是表明：若预估值非目标值，就更新预估值，直到两者足够接近为止。

[拉马努金](https://zh.wikipedia.org/wiki/%E6%96%AF%E9%87%8C%E5%B0%BC%E7%93%A6%E7%91%9F%C2%B7%E6%8B%89%E9%A9%AC%E5%8A%AA%E9%87%91)提出的**连分法**用来计算**黄金分割率(Golden Ratio)** 十分合适，即：

$$Golden.Ratio = \frac{\sqrt{5}-1}{2} = \frac{1}{1+\frac{1}{1+\frac{1}{1+...}}}$$

借助函数 `improve`，即可得到目标精度值的黄金分割率。

```python
def golden_update(guess):
    return 1 + 1/guess

def approx_eq(x, y, tolerance=1e-15)
    return abs(x - y) < tolerance

def square_close_to_successor(guess):
    """精度检测"""
    return approx_eq(guess * guess, guess + 1) 

golden_ratio = improve(golden_update, square_close_to_successor)
```

这样的结构体现出计算机科学的两大重要思想：

1. 变量和函数让我们将相当繁重的复杂性抽象出来，从而得以简化；

2. 多个简单函数的相互调用可以实现复杂的过程，实现庞大的功能。

#### 将函数作为返回值
在上面的例子中，我们将函数都定义在了 `Global Frame` 中。这样做不会导致什么运行问题，但会产生一些不便：
1. `Global Frame` 可能会充斥着这些函数而显得臃肿混；
2. 定义好的函数在调用时参数数量已经固定，可能会引起一些重复。

为解决这样问题，**在函数内定义函数**不失为一种解决方法。同样借助 `improve` 函数，这次我们来利用二分逼近法求得平方根：

```python
def sqrt(n):
    def sqrt_update(x):
        return average(x, n/x)
    def sqrt_close(x):
        return approx_eq(x*x, n)
    return improve(sqrt_update, sqrt_close)
```

这样一来，函数 `sqrt_update` `sqrt_close` 便不会出现在 `Global Frame` 中，而是在 `sqrt` 被调用时才会被创建于 `sqrt Frame` 中。

在这里，进一步丰富关于 `Frame` 的概念。

自定义函数有权使用**被定义**而非被调用时所处环境中的变量，这样的性质被称为**词法作用域(Lexical Scope)** 。函数定义时环境中的**最新框架**被称为其的**父框架(Parent Frame)** ，而当函数被调用时，新建框架便会拥有相同的父框架。

前面说过，寻找变量值时，会由最新框架开始检索，若找不到就返回上一框架。此时我们便知道，上一框架便是指的父框架。在这样的检索过程中，框架与框架之间会成**链状结构**，而这样的链状结构便组成了一个一个的**环境**。

要进行这样的**链状检索方式**，对我们的**环境**有两个要求：

1. 每个自定义函数都有一个**父环境**，即函数被定义时的环境。
2. 当自定义函数被调用时，其 `Local Frame` 就会拓展到其**父环境**中。

从中，可以发掘出词法作用域的两大优势：

1. 函数内定义的函数不会与外部函数的名字发生冲突矛盾。
2. 函数内定义的函数有权访问“封闭函数”的环境，即，内层函数可以访问到外层函数的作用域。这样的内层函数就称之为**闭包(closures)** 。

因此，当我们使用函数作为返回值时，就可以避免对外部环境的修改了。

### 柯里化(Currying)
柯里化，就是用**高阶函数(Higher-order Function)** 将多参数函数转化成各有一个参数的函数链。

```python
def curried_pow(x):
    def h(y):
        return pow(x, y)
    return h

curried_pow(2)(3)
```

上面的代码中，就将函数 `pow(x, y)` 柯里化成了函数 `curried_pow(x)(y)`。

当然，我们也可以进行**反柯里化(uncurrying)** ：

```python 
def curry(f):
    def g(x):
        def h(y):
            return f(x, y)
        return h
    return g

def uncurry(g):
    def f(x, y):
        return g(x)(y)
    return f

pow_curried = curry(pow)
uncurry(pow_curried)(2, 3)
```

从中可以发现，`curry(f)(x)(y)` 等价于 `f(x, y)`，而 `uncurry(curry(f))` 等价于 `f`。

### 匿名表达式(Lambda Expressions)
当函数只用来返回表达式结果时，便可以运用匿名表达式来代替。

```python
def compose(f, g):
    return lambda x: f(g(x))
```

我们可以用以下的结构来进行理解：
```python
lambda x: f(g(x))
"""A function that (lambda) / takes x (x) / and returns (:) / f(g(x)) (f(g(x)))"""
```

`lambda`可以视为只含有一个`return`语句的`def`语句，他们之间也存在一些异同：
1. 都具有定义域(Domain)、值域(Range)、映射关系(Behavior)；
2. 都是将函数与函数名绑定，对应的函数都具有父框架；
3. `def` 语句可以为函数创建**内在名称(Intrinsic Name)** ，而 `lambda` 不可以。

注意，Python 中一般更喜欢 `def` 语句，而只在作为参数或返回值时使用 `lambda`。

### 一等函数(First-Class Functions)
在实际编程中，不能仅仅追求更加强有力的抽象而忽视其他因素。成为一流的编程专家要学会如何**权衡抽象的程度**。

通常来说，编程语言会对各种计算元素的操作方法施以限制，而限制最少的元素就被我们称为**一等状态(First-class status)** 。一等的元素具有以下特权：  

1. 他们可以与变量名绑定；
2. 他们可以作为参数传递给函数；
3. 他们可以作为函数的返回值；
4. 他们可以被数据结构包含。

特别的，Python 给予了**函数(Functions)** 一等状态。

### 函数装饰器(Function Decorators)
Python 为高阶函数提供了特殊的语法，使其能够作用于执行 `def` 语句的部分，称为**装饰器(Decorator)** 。

装饰器最常见的应用场景就是**示踪器(trace)** ：

```python
def trace(fn):
    def wrapped(x):
        print('->', fn, '(', x, ')')
        return fn(x)
    return wrapped

@trace
    def triple(x):
        return 3 * x

triple(12)
```

上述代码中，输出结果为 

``` 
-> <function triple at 0x102a39848> ( 12 )
36
```

可以见得，上述代码的第二部分等价于：

```python
def triple(x):
    return 3 * x

triple = trace(triple)
```

进一步解释，`@` 后面也可以是一个调用表达式，此语句会被首先执行，而后 `def` 语句执行，最终，装饰器表达式\装饰器函数的结果会被应用到新定义的函数，最终的结果就会与 `def` 后的函数名绑定。

## Week 3
### 错误与异常处理(Errors and Tracebacks)
#### 错误(Errors)

1. 语法错误(Syntax Errors)   
    如括号不匹配，运算符不规范，变量未定义等；
2. 运行时出错(Runtime Errors)  
    如类型错误、除零错误等，在显示时会具体到不同的错误类型；
3. 逻辑/行为错误(Logical or Behavior Errors)  
    逻辑上的漏洞导致代码可以执行，但结果不符合预期。

#### 异常处理(Tracebacks)
在执行 `.py` 文件时，若产生报错，则会显示 `Tracebacks` 部分，用以说明具体的出错之处及错误原因。

### 期中例题
#### WWPP(What Would Python Print?)

```python
def delay(arg):
    print('delayed')
    def g():
        return arg
    return g
```

Q1:   
`delay(delay)()(6)()`

A1: 
```python
delayed
delayed
6
```

Q2:  
`print(delay(print)()(4))`  

A2:
```python
delayed
4
None
```

分析：

Q1 中，分析的对象是这样变化的：`delay(delay)()(6)()` -> `delay(delay)()(6)` -> `delay(delay)()` -> `delay(delay)` 。其中，每一项都是对后一项函数的调用。`delay(delay)` 会先输出一个 `delayed`，而后便可将 `delay(delay)` 视作 `g`。故 `delay(delay)()` 成为了对 `g` 的调用 `g()`，返回 `arg`。本例中，`arg` 实际为 `delay(delay)` 时传入的 `delay`，故 `g()`、亦即 `delay(delay)()`，便代表着 `delay`。而 `delay(delay)()(6)` 便是将 `6` 作为参数传入进 `delay`，输出 `delayed`，此时 `delay(delay)()(6)` 便代表着 `g`。而再次调用 `g`，便是最终的 `delay(delay)()(6)()`，此时返回的 `arg` 便是刚才传入的 `6`，故而最终输出一个 `6`。

Q2 中，分析同理，只需特别注意，`print` 会输出参数内容，而自身返回一个 `None`。且当函数的返回值为 `None` 时，编译器不会输出。

## Week 4
### 递归(Recursion)
当一个函数在函数体内直接或间接地调用自身时，就形成了 **递归(Recursion)** 。

以计算阶乘的递归函数为例：

```python
def fact(n):
    if n == 0:
        return 1
    else:
        return n * fact(n-1)
```

要检验一个递归函数的正确性，我们只需要：

1. 考虑其 **边界情况(Base Case)** ，即例中的 `return 1 if n == 0`；  
2. 将 `fact` 视作一种函数的抽象，并**相信**它可以正常工作，即 `fact(n-1)` 是无误的；
3. 在此基础上，考虑 `fact(n)` 的内部结构是否符合正确逻辑。

第二步中，这种“考虑抽象、相信无误”的做法，被戏称为 **递归的信仰之跃(Recursive Leap of Faith)** 。

与迭代形式相比，递归形式不需要考虑局部变量命名之间的冲突，更易于进行构建。

而当递归的过程被分为两个或以上函数互相调用时，就被称为 **相互递归(Mututally Recursive)** 。

以此二函数为例：

```python
def is_even(n):
    if n == 0:
        return True
    else:
        return is_odd(n-1)

def is_odd(n):
    if n == 0:
        return False
    else:
        return is_even(n-1)
```

当然，互相递归的两个函数可以合并成一个函数。无论如何，前者提供了一种在复杂的递归过程中的依然可以保持 **抽象(Abstract)** 的计算方式。

当函数调用自身的次数超过一次时，就会因其形似于树的调用顺序被称为 **树状递归(Tree Recursive)** ，如 **斐波那契数列(Fibonacci Numbers)** 所示：

```python
def fib(n):
    if n == 1:
        return 0
    if n == 2:
        return 1
    return fib(n-2) + fib(n-1) 
```

类比于 **表达式树(Expressions Tree)** ，读者可以自行画出其树形图示，以观察其调用顺序。

## Week 5
### 原生数据类型(Native Data Types)
Python 中的每个值都属于某一个 **类(class)** ，我们将这个类称为 **数据类型(Data Types)** 。类可以视为其实例的模板。同一类的值则具有相同的 **行为(Behavior)** ，例如，`1` 和 `3` 都属于 `int` 类，所以我们可以对其都实施加减乘除等操作，而不需要考虑这些运算是否只对其中一个生效。

在 Python 中，有许多数据类型是原生的，我们将其称为 **原生数据类型(Native Data Types)** 。它们具有以下两大特性：

1. 有一些表示原生数据类型之值的表达式，被称为 **字面量(literals)** 。
2. 内嵌函数和内嵌操作符能够对原生数据类型之值进行运算。

所幸，原生数据类型的种类不多，降低了我们学习编程语言的记忆门槛。

### 数值原生类型(Native Numeric Types)
Python 中有三种数值原生类型，分别为 `int`, `float` 和 `complex`。

`int` 可以准确地表达一个整型数字，且无大小范围限制；  
`float` 能表示一个小数，精确但不完全准确，且存在大小范围限制。

举例来说，`7 / 3 * 3` 的运行结果是 `7.0`，而 `1 / 3 * 7 * 3` 的运行结果是 `6.9999999999999`，这两者都会可能被认为是 `7`。

因此，当使用多个 `float` 类型数据进行运算时，就要当心出现 **近似误差(Approximation Errors)** 。除此之外，除以一个 `int` 类型数据也会导致结果的数据类型为 `float`。

### 数据抽象(Data Abstraction)
将数据的创建与调用方式分割开来的思想就是数据抽象，通过一些函数可实现这两部分的连接。在这种思想下，我们在调用数据时就不需要考虑其是如何被创建的了。

举例来说，我们创建一个新的数据类型————有理数。有理数由分子、分母构成，受知识所限，我们暂时假定以下函数能够正常使用，而不关注其内部功能如何实现：

```python
def rational(n, d):
    """ Return a rational number with numerator n and denominator d. """

def numer(x):
    """ Return the numerator of the rational number x. """

def denom(x):
    """ Return the denominator of the rational number x. """
```

而拥有了这些基础函数后，我们便可以实现更高一层的操作了：

```python
def add_rationals(x, y):
    nx, dx = numer(x), denom(x)
    ny, dy = numer(y), denom(y)
    return rational(nx * dy + ny * dx, dx * dy)

def mul_rationals(x, y):
    return rational(numer(x) * numer(y), denom(x) * denom(y))

def print_rational(x):
    print(numer(x), "/", demon(x))

def rationals_are_equal(x, y):
    return numer(x) * denom(y) == numer(y) * denom(x)
```

可见，即便我们不知道函数 `rational` `numer` `denom` 的具体实现方式，即数据的创建方式，而仍可以通过这些函数对数据进行更高一层的操作，以此便实现了 **数据抽象** 。

### 对组(Pairs)
含有两个元素的 **列表(list)** 构成了 **对组(pair)** ，对组用来将两个元素绑定到一起，当然，对组的构成方式也并不只有这一种。

列表的操作可见下文，通过对组，我们便可以实现上文的三个函数：

```python
def rational(n, d):
    return [n, d]

def numer(x):
    return x[0]

def denom(x):
    return x[1]
```

### 抽象壁垒(Abstraction Barriers)
为便于程序编写，我们建立了一层层抽象，每一层的抽象都依附于上一层的抽象之中。在这样的情况下，要变更数据的组成构建而不更改数据的相关操作就变得十分容易。

上面所举的例子中，我们可以根据各层抽象来形成下表：

|程序实现的功能|分析实数的角度|用于实现的方式|
|:-----:|:-----:|:-----:|
|实数间的运算|一个数据类型|`add_rational`, `mul_rational` <br> `rationals_are_equal`, `print_rational`|
|实数内部运算|分子和分母|`rational`, `numer`, `denom`|
|实数的创建|一个对组|`list` 字面量|

表中最后一列的函数便组成了 **抽象壁垒(Abstraction Barriers)** ，这些函数只能被高一级的抽象调用，或由低一级的抽象构成。

当某一级抽象越级调用低其多级的抽象时，抽象壁垒就被 **打破** 了。举例来说，符合抽象壁垒的函数应如：

```python
def square_rational(x):
    return mul_rational(x, x)
```

而打破抽象壁垒的函数便是：

```python
def square_rational_breaking_barrier(x):
    return rational(numer(x) * numer(x), denom(x) * denom(x))
```

抽象壁垒的存在可以方便我们去维护、整改代码，如果某级抽象的实现细节发生改变，而行为功能没有变化，遵循抽象壁垒的代码就能保持正常运行。当然，某层抽象中所依赖的次级抽象函数越少，代码就越安全、越保险。

## Week 6
### 序列(Sequences)
Python 中有许多序列类型，其所具备的共同点如下：

- **长度(Length)** ，序列的长度有限，空序列的长度为 0。
- **元素选择(Element Selection)** ，序列的元素与小于其长度的非零整数索引一一对应，首元素的索引为 0。
- **成员检测(Membership)** ，返回布尔值，调用如 `2 in [1, 3, 5]`, `2 not in [1, 3, 5]`。
- **切片(Slicing)** ，`digits[x:y:z]` 可返回一个列表, 其内容为 `digits[x + n*z]` 的集合，其式满足 `n >= 0 && x + n*z < y`。其中各部分均可省略，`x` 与 `z` 省略后为 `0`，`y` 省略后为 `length`。

对于序列来说，加法乘法不会对元素值有所影响，只是会将序列进行连接或复制。

特别的，序列有一些特殊的处理方式：

- **序列迭代(Sequence Iteration)** ，通过 `for` 语句可以实现对序列元素的逐个访问。
- **序列解包(Sequence unpacking)** ，如 `for x, y in [[1, 2], [3, 4]]`，同时对两个变量进行迭代。本质上，这与 `a, b = 1, 2` 是相似的。
- **列表解析式(List Comprehensions)** ，`[x for x in [1, 3, 5, 7, 9] if 25 % x == 0]` 即可生成 `[1, 5]`。其中 `for`, `in`, `if` 为关键词。
- **序列聚合(Sequence Aggregation)** ，即将列表的所有元素聚合为一个值，其典型函数如 `sum()`, `min()`, `max()`。

#### 列表(Lists)
列表的元素可以是任意值，包括列表。在列表嵌套下， **元素选择** 可以多次调用，如 `pairs[1][0]`。

#### 范围(Range)
内置函数 `range(x, y)` 可以生成一个整数范围 `[x, y)`。另，`range(x)` 视为 `range(0, x)`。

一般来说，在进行指定次数的循环时，常使用 `for _ in range(x)` 实现，其中的 `_` 表明我们将不使用此变量。

#### 字符串(Strings)
字符串一般由单引号或双引号括起，也可由 `str()` 生成，如 `str(2) + " is in " + str([1, 2, 3])`。

当字符串内容包含引号时，由另一种引号括起，如 `"I'm a boy."`，或由转义符号`\`表示，如 `'I\'m a boy.`。

在字符串中， **成员检测(Membership)** 并不寻找元素，而是寻找字串。如 `"here" in "Where is Waldo?"` 的结果为真。

值得注意的是，Python 中没有 **字符(character)** 这个数据类型，`'x'` 被视为长度为 1 的字符串。 

欲表示多行字符串，可采用三引号的方式，亦或字符 `\n` (详阅ASCII码):

```python
""" Multiline
Literals
String """

"Multiline\nLiterals\nString"
```

三引号引起的字符串常用来做 **文档描述(Doctests)** 。

### 树状结构(Trees)
树是一种非常经典的数据类型，具有以下特性：

- 树有一个 **标签(Label)** 和任意数量的 **枝干(Branches)** 。
- 树的每一枝干都是树。
- 没有枝干的树被称为 **叶子(Leaves)** 。

根据特性，我们便可以实现树的基础功能：

```python
def tree(root_label, branches=[]):
    for branch in branches:
        assert is_tree(branch), 'branches must be trees'
    return [root_label] + list(branches)

def label(tree):
    return tree[0]

def branches(tree):
    return tree[1:]

def is_tree(tree):
    if type(tree) != list or len(tree) < 1:
        return False
    for branch in branches(tree):
        if not is_tree(branch):
            return False
    return True

def is_leaf(tree):
    return not branches(tree)
```

通过对 `tree()` 的递归调用，便可以实现树的构建。如此斐波那契数列树：

```python
def fib_tree(n):
    if n == 0 or n == 1:
        return tree(n)
    else:
        left, right = fib_tree(n-2), fib_tree(n-1)
        fib_n = label(left) + label(right)
        return tree(fib_n, [left, right])
```

### 链表结构(Linked Lists)
**嵌套数对(Nested Pairs)** 被称为 **链表(Linked Lists)** 。链表有两个元素，第一个元素保存着值，第二个元素为另一个链表，如 `[1, [2, [3, [4, 'empty']]]]`。

### 面向对象编程(Object-Oriented Programming)
**面向对象编程** 的一大特点就是在编写各部分代码时，我们只需要专注于考虑当前部分的逻辑，而最终各部分能够通过 **消息传递(message passing)** 组合在一起进行工作。

将 **状态(state)** 与 **数据(data)** 相绑定是 **面向对象编程(Object-Oriented Programming)** 的核心思想。

**对象(object)** 是 **状态(state)** 和 **数据(data)** 的连结，包含 **信息(information)** 与 **过程(processes)** ，用于展现复杂事物的 **属性(properties)** 、 **交互(interactions)** 与 **行为(behaviors)** 。

以日期为例，日期就是一个对象。`from datetime import data` 中，就将变量 `data` 与一个 **类(class)** 绑定，而每个单独的日期就是这个 **类(class)** 的一个 **实例(instances)** 。

实例可以通过对类的带参调用来生成，如 `tues = data(2014, 5, 13)`。

对象拥有 **属性(attributes)** ，亦即值。通常，我们用 `.` 来访问对象的值，如 `tues.year`。

对象也拥有 **方法(methods)** ，可以视为函数形式的值。通常， **方法** 是利用 **类** 与 **参数** 所得出 **理想结果** 的函数。

在 Python 中，所有的值都是类。也就是说，所有的值都会有 **行为(behavior)** 和 **属性(attributes)** 。

### 可变数据(Mutable Data)
Python 中，原始内置值的实例，如数字，都是 **不可变的(immutable)** ，这意味着在程序执行时，这些值本身不会发生改变。

而 **列表(lists)** 恰好相反，它是 **可变的(mutable)** ：

```python
chinese = ['coin', 'stirng', 'myriad']
suits = chinese
```

之后对 `suits` 的任何操作，都会导致 `chinese` 的改变。也就是说，`suits = chinese` 并没能创建新的列表。通过对 `suits` 的修改，导致列表中的值发生改变，指向此列表的 `chinese` 自然也发生改变。

如果使用 `list()`，即 `suits = list(chinese)`，便能够创建新的列表，改动也将不会同时作用于两变量。

可见，即使两列表的内容相同，他们也可能是不同的列表。为了能够作此区分，Python 提供了 `is` 和 `is not` 来检验两表达式是否是同一对象。

```python
a = [1, 2, 3]
b = a
c = [1, 2, 3]

>>> a is b
True
>>> a is not c
True
>>> a is [1, 2, 3]
False
>>> a == b
True
>>> a == c
True
>>> a == [1, 2, 3]
True
```

注，前文所提的 **列表解析式(List Comprehensions)** 总会创建一个新的列表。

同时，`a = a + [4]` 与 `a += [4]` 也不尽相同，前者会创建一个新的列表，而后者则是对原列表进行修改。

Python 的内置类型 **元组(tuple)** ，可以视为不可变的 **列表(list)** 。与列表的不同是，它由 `()` 括起。括号可以省略，尽管习惯上并不如此。任何类型都可以用元组来代替。

空元组和单元素元组有特殊的语法：`()` `(10,)`。

因为元组不可变，所以列表的一些修改操作无法应用，其他的特性，如切片、元素选择等，则可以类比。

以下问题选自 `Disc06` 中的 `Q1: WWPD: Mutability`，用于检验此部分的学习成果：

```python
>>> x = [1, 2, 3]
>>> y = x
>>> x += [4]
>>> x
# What would Python display?
>>> y
# What would Python display?
>>> x = [1, 2, 3]
>>> y = x
>>> x = x + [4]
>>> x
# What would Python display?
>>> y
# What would Python display?
>>> s1 = [1, 2, 3]
# What would Python display?
>>> s2 = s1
# What would Python display?
>>> s1 is s2
# What would Python display?
>>> s2.extend([5, 6])
# What would Python display?
>>> s1.append([-1, 0, 1])
>>> s2[5]
# What would Python display?
>>> s3 = s2[:]
>>> s3.insert(3, s2.pop(3))
>>> len(s1)
# What would Python display?
>>> s1[4] is s3[6]
# What would Python display?
>>> s3[s2[4][1]]
# What would Python display?
>>> s1[:3] is s2[:3]
# What would Python display?
>>> s1[:3] == s2[:3]
# What would Python display?
>>> s1[4].append(2)
>>> s3[6][3]
# What would Python display?
```

### 字典(Dictionaries)
**字典(Dictionaries)** 是 Python 中的内置数据类型之一，用于储存和构建对应关系。字典储存 **键值对(key-value pairs)** ，其键、值都可视为对象。

字典通过以 **键(key)** 为基础的索引储存 **值(value)** ，每个键都最多对应一个值。在程序中，字典的顺序是不可预测的。

通过 `dict.values()`, `dict.keys()` 和 `dict.items()`，可以迭代元素值。

`dict()` 也可以用来转换其他类型为字典，如 `dict([(3, 9), (4, 16), (5, 25)])`。

字典有一些限制：

- 字典的键不能是、也不能包含可变数据。
- 每个键至多对应一个值。

第一个限制在底层很好理解，如果键更改了，很难去寻找对应的值。因此，通常使用 **元组(tuple)** 来作为键。

第二个限制是字典这层抽象的必然结果，否则就不能实现键值的对应关系了。

字典中有个很实用的函数，即 `get()`。如 `dict.get(key, return_value)`。若字典中的 `key` 有对应值，函数返回值，否则返回 `return_value`。

字典也有解析式，形如 `{x: x*x for x in range(3, 6)}`。

### 局部状态(Local State)
列表和字典都有 **局部状态(Local State)** ，也就是说，他们可以在程序执行时的任何时间节点改变自己的值。

**函数(Functions)** 也可以拥有局部状态，举例来说：

```python
>>> withdraw(25)
75
>>> withdraw(25)
50
>>> withdraw(60)
'Insufficient funds'
```

以同样的参数多次调用，其返回结果不同，如此函数便具备了局部状态。当然，这个自定义函数也就是 **非纯函数(Non-Pure Function)** 。

要使函数功能能够实现，就需要一个变量来储存值，通过高阶函数便可以实现：

```python
def make_withdraw(balance):
    def withdraw(amount):
        nonlocal balance
        if amount > balance:
            return 'Insufficient funds'
        balance -= amount
        return balance
    return withdraw

withdraw = make_withdraw(100)
```

代码中的 `nonlocal balance` 一句用来声明：每当 `balance` 改变时，都是第一个 **框架(Frame)** 中的 `balance` 改变。如果没有 `nonlocal` 的话，则会在当前 **环境(Environment)** 中的第一个 **框架(Frame)** 中了。换言之，`nonlocal` 说明此变量不存在于 **本地框架(Local Frame)** 和 **全局框架(Global Frame)** 中。而若此变量没有实现绑定值，这句话就会报错。

追溯程序的执行，我们可以发现，**本地框架(Local Frame)** 之外的变量能够由赋值语句改变了。

虽然可以指定到其他框架，但在一个函数中，同名变量通常要保证指向相同，否则容易混淆，降低代码可读性。同时，如此可以保证程序的预运算，从而加快运行速度。

`nonlocal` 语句是将独立的对象互相连接的重要途径，同时各部分也可以保证分隔管理。

只含有 **纯函数(Pure Functions)** 的表达式中，我们可以用结果值替换函数，而不会影响表达式的结果，这称为 **参考透明性(Referentially Transparent)** 。而 `nonlocal` 则打破了这一性质。尽管如此，它依然是 **模块化编程(Modular Programs)** 的重要工具。通常，可变数据类型与局部状态会搭配使用。

### 调度函数(Dispatch Function)
调度函数是集许多函数为一体的，它的第一个参数为 **消息(message)** ，其余为所需量。

通过调度函数，我们可以模拟字典的实现：

```python
def dictiionary():
    records = []
    def getitem(key):
        matches = [r for r in records if r[0] == key]
        if len(matches) == 1:
            key, value = matches[0]
            return value
    def setitem(key, value):
        nonlocal records
        non_matches = [r for r in records if r[0] != key]
        records = non_matches + [[key, value]]
    def dispatch(message, key=None, value=None):
        if message == 'getitem':
            return getitem(key)
        elif message == 'setitem':
            setitem(key, value)
    return dispatch

d = dictionary()
d('setitem', 3, 9)
d('setitem', 4, 16)
d('getitem', 3) # 9
```

调度函数是抽象函数实现消息传递的方式，为此，我们用条件语句将消息与一组固定的已知消息进行比较。

### 传播约束(Propagating Constraints)
以约束的形式进行程序设计的思路被称为 **声明式编程(Declarative Programming)** ，在此规范下，只需要编程者考虑如何描述待解决问题，而不需要考虑如何去具体解决。

一般编程通常是单向计算，如 `9 * c = 5 * (f - 32)`，要计算 `c`，就要将公式转换为 `c = 5 * (f - 32) / 9`，这个公式自然不能计算 `f`。

而应用 **约束系统(Constraints)** 后，便要从两个参数入手，阐明其相互关系，从而实现转换。

## Week 7
### 隐式序列(Implicit Sequences)
**隐式序列(Implicit Sequences)** 不会被完整储存到计算机内存中，而是在被访问时计算出元素值。正如 `range()` 函数，只有其参数，即首末值，会被记录，而后计算出所需要的元素值。比如 `range(1000, 1000000000)[3]` 中，就会计算 `1000 + 3`，而后返回 `1003`。

这样直到需要时才会计算值的方法被称为 **懒计算(Lazy Computation)**

### 迭代器(Iterators)
迭代器可以用来提供对数据的逐个顺序访问。

迭代器有两个组成部分，一个用来计算序列中的下一个值，一个用来检测是否到达了序列末。

迭代器由函数 `iter()` 生成，`next()` 用来获取下一个值。当到达序列末时，`next()` 就会抛出 `StopIteration` 异常。

迭代器会维持 **局部状态(Local State)** 来记录序列中的索引位置。当 `next()` 被调用时，这个位置就会更新。

将迭代器传入 `iter()`，依然会返回此迭代器，而不是一个新迭代器。

迭代器可以实现顺序访问一系列的值，而不需要将所有值储存在内存中。虽然不如随意访问序列中的元素那样灵活，顺序访问依然在数据处理应用中十分有效。

迭代器是 **可变的(mutable)** ，也可以通过不抛出 `StopIteration` 异常来实现无限序列。

任何可以被传入 `iter()` 来生成迭代器的值被称为 **可迭代值(Iterable Value)** ，包括但不限于 **字符串(Strings)** 、 **元组(Tuples)** 、 **集合(Sets)** 、 **字典(Dictionaries)** 、 **迭代器(Iterators)** 。

像字典这样的无序序列，也会在生成迭代器时产生顺序。如果字典的内容有删增，原迭代器就会报废，而 **值(Values)** 的改变不会影响迭代器。

有一些内置函数，如 `map()` `fliter()` `zip()` `reversed`，都可以传入迭代器，并返回迭代器。

如 `range()` 一般， **可迭代对象(Interable Objects)** 可以作为 `for <name> in <expression>:` 中的 `<expression>`。

在此，`for` 的执行过程便得以追踪：

1. 检测 `<expression>` 是否可迭代。
2. 访问 `<expression>` 的下一个值。
3. 如果没有抛出 `StopInteration` 异常，就将值绑定到 `<name>` 变量，否则退出。
4. 执行内部语句。

### 生成器(Generator)
生成器是由 **生成器函数(Genrator Functions)** 返回的迭代器。生成器函数中没有 `return` 语句，而是以 `yield` 替代。

生成器通过控制生成器函数的执行过程来获取值。当生成器的 `__next__` 方法被调用时，生成器函数被执行，直到遇见 `yield` 停止。这样的执行顺序不会破坏之后创建的新环境。

```python
def letters_generator(): 
    current = 'a'
    while current <= 'd':
        yield current
        current = chr(ord(current) + 1)

for letter in letters_generator():
    print(letter)
```

调用函数时，函数并不返回 `yield` 后的值，而是一个生成器，这个生成器可以返回其值。

除 `yield` 外，还有 `yield from` 语句，可以理解为 `yield <each element> from <an iterator or iterable>`。

### 流(Streams)
流是另一种隐式序列的形式，本质上是 **懒计算(Lazy Computation)** 的 **链表(Linked List)** 。

像链表，流有两个元素，且第二个元素还是流。但其第二个元素只有在被检索时才会计算。

为了实现这样的懒计算，流需要储存计算第二个元素的方法。当函数被调用时，计算出来的值就会被赋予 `_rest`，其中的下划线表明这个变量不能够直接访问。

同样，流也可以表示无限序列。

### 对象和类(Objects and Classes)
每个 **对象(Object)** 都是 **类(Class)** 的 **实例(Instance)** 。如，某辆汽车是一个对象，它属于汽车这个类，因此是这个类的一个实例。

创建一个新的实例被称为 **类的实例化(Instantiating)** 。

对象的 **属性(Attribute)** 是与对象相关联的变量值，通过 `.` 进行访问。对象的 **方法(Methods)** 是操作对象的函数。

类可以进行自定义，一般语句结构如下：

```python
class <name>:
    <suite>
```

以银行账户举例，进行类的创建：

```python
class Account:
    def __init__(self, account_holder):
        self.balance = 0
        self.holder = account_holder
```

其中的 `__init__()` 被称为类的 **构造函数(constructor)** ，是用来初始化对象的。它有两个参数，第一个是 `self`，表明新创建的 `Account` 对象，第二个就是所需要的参数。

完成类的创建后，现在就可以进行类的实例化了：

```python
a = Account('Kirk')
```

这个对 `Account` 类的调用创建了新的对象，也就是一个此类的实例，而后 `__init__()` 函数就会被调用，`balance` `holder` 都完成了赋值。

同一类的两个实例会拥有各自独立的属性，他们相互不受影响。为了加强这样的独立性，每个自定义类的实例都有独一无二的 **身份(Identity)** 。同样，`is` 和 `is not` 可以用来判断，两实例是否相同。

```python
b = Account('Spock')
b.balance = 200
a is a # True
b is not a # True
c = a
c is a # True
```

只有在进行 **类的实例化** 的时候，才会创建新的对象实例。

在类中， **方法(Methods)** 通过 `def` 语句进行定义：

```python
class Account:
    def __init__(self, account_holder):
        self.balance = 0
        self.holder = account_holder
    def deposit(self, amount):
        self.balance += amount
        return self.balance
    def withdraw(self, amount):
        if amount > self.balance:
            return 'Insufficient funds'
        self.balance -= amount
        return self.balance
```

每个方法的第一个参数都是 `self`，通过这个，方法能够访问对象的内部状态。

对方法的调用依然是通过 `.`：

```python
spock_account = Account('Spock')
spock_account.deposit(100) # 100
spock_account.withdraw(90) # 10
spock_account.withdraw(90) # 'Insufficient funds'
spock_account.holder # 'Spock'
```

方法和属性都是面向对象编程的重要基础元素。像调度函数一样，对象通过 `.` 接收 **消息(Message)** ，但其并非字符串，而是类的本地变量名。

可以看出，对象也有局部状态值，也就是实例属性，但它是通过 `.` 来访问的，并没有 `nonlocal` 语句。

通过 `.`，我们可以实现对 **消息(Message)** 的直接运用，如 `spock_account.holder`，而不需要通过函数，如 `getattr(spock_account, 'holder')` 进行。

```python
>>> type(Account.deposit) 
<class 'function'>
>>> type(spock_account.deposit)
<class 'method'>
```

上述代码中，前者为 **函数(Functions)** ，后者为 **已绑定方法(Bound Methods)** 。简单来看，前者需要两个参数，而后者只需要一个：

```python
>>> Account.deposit(spock_account, 1001)
1011
>>> spock_account.deposit(1000)
2011
```

通常，类的变量名会使用 **驼峰命名法(CamelCase)** ，而方法依然采用小写与下划线的组合。

有些属性由所有实例共同享有，称为 **类属性(Class Attributes)** ，依此进行定义：

```python
class Account:
    interest = 0.02
    def __init__(self, account_holder):
        self.balance = 0
        self.holder = account_holder
```

当发生重名时，会以 `实例属性 -> 类属性` 的顺序进行访问。

对类属性的更改会对所有的同类实例生效，但对某一实例的类属性修改不会影响其他实例：

```python
>>> kirk_account.interest = 0.08
>>> kirk_account.interest
0.08
>>> spock_account.interest
0.02
>>> Account.interest = 0.04
>>> spock_account.interest 
0.04
>>> kirk_account.interest
0.08
```

以上代码中，其实第一行是为实例 `kirk_account` 创建了一个 `interest` 并赋值为 `0.08`。

### 继承(Inheritance)
很多时候，不同类型之间也是有所关联的，而且相似的类也可能会有细节上的不同。在这种时候，就可以用到继承了。

顾名思义，继承意为某一类从另一类中获取一些属性或方法。前者被称为 **子类(Sub Class \ Child Class)** ，后者被称为 **基类(Base Class)** 或 **父类(Parent Class)** 。

子类从父类处继承属性和方法，不过允许 **重载(Override)** ，即再次定义。

通过在`类名(CheckingAccount)`后面附带`父类名(Account)`来使用继承：

```python
class CheckingAccount(Account):
    withdraw_charge = 1
    interest = 0.01
    def withdraw(self, amount):
        return Account.withdraw(self, amount + self.withdraw_charge)
```

继承代表着 **是(is-a)** 而非 **包含(has-a)** ，即子类 **是** 父类的一种特殊类型，父类并不 **包含** 子类，正如这里的 `CheckingAccount` 也是一种 `Account`。

与继承对应的是 **构成(compostion)**，也就是 **包含(has-a)**。就像 `Bank` 可能会有 `Account` 一样，`class Back` 可能会就含有 `class Account`：

```python
class Bank:
    def __init__(self):
        self.accounts = []
    def open_account(self, holder, amount, kind=Account):
        account = kind(holder)
        account.deposit(amount)
        self.accounts.append(account)
        return account
```

在访问时，Python 会首先寻找实例的属性方法，而后是类属性方法，再之后回去父类中寻找。

正因如此，我们通常用类似于 `self.withdraw_charge` 而非 `CheckingAccount.withdraw_charge`，以防方法被重载后调用老函数。

重载时，也可能会有多个父类，用逗号分隔即可。

为解决**钻石继承**的问题，Python 提供了算法 [方法解析顺序(MRO)](https://www.zhihu.com/tardis/zm/art/416584599?source_id=1005)，通过 `[c.__name__ for c in TheClass.mro()]` 可以获取继承顺序列表。

## Week 8
### 字符串转换(String Conversion)
在 Python 中，所有的 **对象(object)** 都有两种字符串的表现形式，一种用于与人进行交互，一种用于 Python 的执行，其中前者是指 `str()`，后者则为 `repr()`。其工作原理是调用目标对象的 `__str__` 与 `__repr__` 方法。

举例来说，在 Python 的 `fractions` 库中，包含对分数进行操作的函数。通过 `a = fractions.Fraction(1, 2)` 可以创建分数 `1/2`。

此时 `str(a)` 的返回结果为 `'1/2'`，而 `repr(a)` 则为 `'Fraction(1, 2)`。

正因为 `repr()` 返回的结果可以被 Python 执行，通常会有 `eval(repr(object)) = object`。

而像 `__str__` `__repr__` 这样的方法，我们期望其对不同的数据类型 —— 包括那些 **用户自定义类型(user-defined classes)** —— 都能够适用。如果函数能够满足这样的性质，我们就称其为 **泛型函数(generic function)** 。同时，`.` 的另一功能也得到了发掘 —— 将已存函数的 **定义域(domain)** 扩展到新的对象类型。

容易想到，要创建这样的一个函数，可以在每个类中为同一属性名做出不同定义。

### 特殊方法(Special Methods)
Python 中，有一些 **特殊方法(Special Methods)** 会在某些时刻被执行器调用，如对象创建时会调用 `__init__` 、输出时会调用 `__str__`。

#### 真假值(True and False Values)
我们知道，在 Python 中，非 0 即真。对于自定义类型而言，如果没有特别说明，默认会返回 `True`。

要对此进行更改，就可以对 `__bool__` 进行定义：

```python
class A:
    def __bool__(self):
            return False
```

#### 序列操作(Sequence Operations)
`len()` 函数会调用 `__len__` 方法。

当 `__bool__` 没有被定义时，实则会根据 `__len__` 返回真假值。

用于选择元素的 `[]` 实则是调用了 `__getitem__`，如 `'ABC'[1] = 'ABC'.__getitem__(1)`。

#### 可调用对象(Callable Objects)
前面提到过，函数是 **一等对象(first-class objects)** ，可以被当成数据传递，也可以像其他对象一样拥有属性。

通过 `__call__` 方法，我们定义的类就可以像函数一样被调用。如此定义的 **类(class)** 就像 **高阶函数(higher-order function)** 一样了：

```python
def make_adder(n):
    def adder(k):
        return n + k
    return adder

class Adder(object):
    def __init__(self, n):
        self.n = n
    def __call__(self, k):
        return self.n + k

add_three_func = make_adder(3)
add_three_obj = Adder(3)
add_three_func(4) # return 7
add_three_obj(4)  # return 7
```

#### 算术(Arithmetic)
在使用 `+ - * / // **` 等运算符时，也会有一些特殊方法被调用。举例来说，当计算 `+` 时，Python 会首先调用左侧算子的 `__add__`，并将右侧算子当作参数传入；如果调用失败，则去尝试调用右侧算子的 `__radd__`，并将左侧算子当作参数传入。

### 多样模态(Multiple Representations)
此标题名为笔者自译，模态在此指存在形式，在下文中将得以澄清。

对于某一种数据而言，其模态并不唯一，而我们自然希望所设计的系统能够跨越多个模态进行工作。

举例来说，**复数(complex numbers)** 由两种很常见的模态，或称为表征方式，即 **复平面系(rectangular form)** 和 **极坐标系(polar form)** 。其中前者由 **实部(real part)** 和 **虚部(imaginary part)** 组成，后者由 **模长(magnitude)** 和 **复幅度(angle)** 组成。

以此举例，我们要如何设计程序系统以兼容两种模态。

首先，两种模态应该满足各自独立，即其之间具有 **抽象壁垒(abstract barriers)** 。为此，要先建立更高一层的抽象。注意到，两种 **复数(complex number)** 都是 **数(number)** ：

```python
class Number:
    def __add__(self, other):
        return self.add(other)
    def __mul__(self, other):
        return self.mul(other)
```

这里的 `Number` 既没有 `add`，又没有 `mul`，甚至没有 `__init__`，因为它仅仅是作为那些 **不同类型的数** 的基类存在。

放在这个具体的例子来看，所谓“不同类型的数”就是复数了。因此接下来的任务，就是为 `class Complex` 补足对应的方法：

```python
class Complex(Number):
    def add(self, other):
        return ComplexRI(self.real + other.real, self.imag + other.imag)
    def mul(self, other):
        magnitude = self.magnitude * other.magnitude
        return ComplexMA(magnitude, self.angle + other.angle)
```

显然，`ComplexRI` 是复平面系模态，而 `ComplexMA` 则是极坐标系模态。

#### @Property 装饰器 
虽然两种复数模态各自独立，但因为表示的是同一复数，所以必然存在某些对应关系。为维护这种关系，解决办法则是通过计算得出另一模态的参数信息。如通过实部虚部可以得出模长和复幅度，通过模长和复幅度也可以算出实部虚部。

在 Python 中，有一个特性使得能够像访问 **属性(arrtitude)** 一样访问 **方法(method)** ，即 `@property` 装饰器：

```python
from math import atan2
class ComplexRI(Complex):
    def __init__(self, real, imag):
        self.real = real
        self.imag = imag
    @property
    def magnitude(self):
        return (self.real ** 2 + self.imag ** 2) ** 0.5
    @property
    def angle(self):
        return atan2(self.imag, self.real)
    def __repr__(self):
        return 'ComplexRI({0:g}, {1:g})'.format(self.real, self.imag)
```

这样一来，就可以通过 `ComplexRI(5, 12).real` 这样来访问了。

类似地，`ComplexMA` 也如此定义：

```python
from math import sin, cos, pi
class ComplexMA(Complex):
    def __init__(self, magnitude, angle):
        self.magnitude = magnitude
        self.angle = angle
    @property
    def real(self):
        return self.magnitude * cos(self.angle)
    @property
    def imag(self):
        return self.magnitude * sin(self.angle)
    def __repr__(self):
        return 'ComplexMA({0:g}, {1:g} * pi)'.format(self.magnitude, self.angle/pi)
```

通过上述方法，在 `ComplexRI` 中对 `real` `imag` 的修改自然会引起 `magnitude` `angle` 的变化，而在 `ComplexMA` 中则反之。

通过对复数两模态的定义，`Complex` 类已经可以正常使用了：

```python
from math import pi
ComplexRI(1, 2) + ComplexMA(2, pi/2) # return ComplexRI(1, 4)
ComplexRI(0, 1) + ComplexRI(0, 1) # return ComplexMA(1, 1 * pi)
```

每个模态的类都是相互独立的，只是通过相同的属性名将其连接在一起，即上文所提的 **共享接口(shared interfaces)** 。这样的 **接口(interface)** 是具有 **可叠加性(additive)** 的 ———— 如果想要拓展到复数的第三种模态，只要再创建一个类，然后依然保持相同的属性名就可以了。

### 泛型函数(Type Dispatching)
**泛型函数(Generic Function)** 可以操作不同类型的值，主要通过 **共享接口(shared interfaces)** 、 **类型调度(type dispatching)** 和 **类型转换(type coercion)** 来实现。

共享接口是针对类的内部定义的操作，在前文中已经做出详尽解释。接下来将对类型调度与类型转换做出更深入的描述。

首先，我们引入分数的类：

```python
from fractions import gcd
class Rational(Number):
    def __init__(self, numer, denom):
        g = gcd(numer, denom)
        self.numer = numer // g
        self.denom = denom // g
    def __repr__(self):
        return 'Rational({0}, {1})'.format(self.numer, self.denom)
    def add(self, other):
        nx, dx = self.numer, self.denom
        ny, dy = other.numer, other.denom
        return Rational(nx * dy + ny * dx, dx * dy)
    def mul(self, other):
        numer = self.numer * other.numer
        denom = self.denom * ohter.denom
        return Rational(numer, denom)
```

按照常理，`Rational(2, 5) + ComplexRI(1, 0)` 理应是可以计算出来的。实现的方式便是类型调度与类型转换了。

#### 类型调度(Type Dispatch)
类型调度就是通过对参数的类型进行识别，进而选择对这些类型适用的操作。

Python 内置的函数 `isinstance()` 能够帮助我们实现这个操作。`isinstance(object, class)` 会返回一个 `bool` 值，表示 `object` 是否为 `class` 的一个实例。这样，我们就能写一个函数来判断复数能否化为实数了：

```python
def is_real(c):
    """ Return whether c is a real number with no imaginary part. """
    if isinstance(c, ComplexRI):
        return c.imag == 0
    elif isinstance(c, ComplexMA):
        return c.angle % pi == 0
```

这是一个非常基础的类型调度函数。而实现类型调度其实并不仅仅使用 `isinstance()`，也有其他办法，如为 `Rational` 和 `Complex` 定义一个 `type_tag`，当此数据相同时，则两实例为同一类型：

```python
Rational.type_tag = 'rat'
Complex.type_tag = 'com'
```

考虑混合运算复数与分数的函数：

```python
def add_complex_and_rational(c, r):
    return ComplexRI(c.real + r.numer/r.denom, c.imag)

def mul_complex_and_rational(c, r):
    r_magnitude, r_angle = r.numer/r.denom, 0
    if r_magnitude < 0:
        r_magnitude, r_angle = -r_magnitude, pi
    return ComplexMA(c.magnitude * r_magnitude, c.angle + r_angle)

def add_rational_and_complex(r, c):
    return add_complex_and_rational(c, r)

def mul_complex_and_rational(r, c):
    return mul_complex_and_rational(c, r)
```

当对不同类型的操作都已经完成时，就可以用类型调度将其组合在一起了：

```python
class Number:
    def __add__(self, other):
        if self.type_tag == other.type_tag:
            return self.add(other)
        elif (self.type_tag, other.type_tag) in self.adders:
            return self.cross_apply(other, self.adders)
    def __mul__(self, other):
        if self.type_tag == other.type_tag:
            return self.mul(other)
        elif (self.type_tag, other.type_tag) in self.multipliers:
            return self.cross_apply(other, self.multipliers)
    def cross_apply(self, other, cross_fns):
        cross_fn = cross_fns[(self.type_tag, other.type_tag)]
        return cross_fn(self, other)
    adders = {("com", "rat"): add_complex_and_rational,
              ("rat", "com"): add_rational_and_complex}
    multipliers = {("com", "rat"): mul_complex_and_rational,
                   ("rat", "com"): mul_rational_and_complex}
```

其执行逻辑是明确的：如果两者类型相同，则直接进行加乘；如果类型不同，则去调用相应的处理函数。

可以发现，通过向 `Number.adders` 与 `Number.multipliers` 中添加键值对，能够实现类型调度的拓展。

如此一来，也就实现了复数与分数直接的运算：

```python
ComplexRI(1.5, 0) + Rational(3, 2) # return ComplexRI(3, 0)
Rational(-1, 2) * ComplexMA(4, pi/2) # return ComplexMA(2, 1.5 * pi)
```

#### 类型转换(Type Coercion)
当分数与复数进行运算时，我们也可以将分数视为虚部为 0 的复数，从而使用复数的运算法则进行计算。这样的转换过程就是 **类型转换(Type Coercion)** 了。

通常，我们会用函数来实现这样的转换过程：

```python
def rational_to_complex(r):
    return ComplexRI(r.numer/r.denom, 0)
```

通常情况下，并不是任意两类型就能够转换的，所以我们需要有类型之间的关系，通过字典 `coercions` 可以储存这样的关系。而函数 `coerce` 就会返回两个相同类型的数据了：

```python
class Number:
    def __add__(self, other):
        x, y = self.coerce(other)
        return x.add(y)
    def __mul__(self, other):
        x, y = self.coerce(other)
        return x.mul(y)
    def coerce(self, other):
        if self.type_tag == other.type_tag:
            return self, other
        elif (self.type_tag, other.type_tag) in self.coercions:
            return (self.coerce_to(other.type_tag), other)
        elif (other.type_tag, self.type_tag) in self.coercions:
            return (self, other.coerce_to(self.type_tag))
    def coerce_to(self, other_tag):
        coercion_fn = self.coercions[(self.type_tag, other_tag)]
        return coercion_fn(self)
    coercions = {('rat', 'com'): rational_to_complex}
```

相比类型调度，类型转换拥有以下优势：

1. 对于每一组类型关系，只需要写一个转换函数，而非两个计算函数。不同类型间的运算也仅仅取决于类型，而与具体的运算操作无关。

2. 有时转换关系也不一定是 `a -> b`，也有可能是 `a -> c & b -> c`。

3. 转换函数也有可能是链式的，如 `a -> c -> b`。这样一来，转换函数的数量也会大大减少。

当然，一些劣势也依然存在，转换函数会丢失参数的原生信息，即丢失原类型的参数。

在早年的 Python 版本中，其实内置了 `__coerce__` 方法，但 Python 的内置类型转换系统并未使用，而是在需要时对操作符进行转换，故而此方法被移除了。

## Week 9
### 效率(Efficiency)
**效率**一般分为**时间效率**和**空间效率**。

正常来说，需要跑出程序才能较为准确地做出测量。但受到机器性能等多种因素的影响，反而不会以此作为参考标准。因此，对程序效率的**事前评估**更为实用。

时间的评估标准一般是程序的代码总执行次数，而对空间的评估标准则是同时存在的框架数量。

因为大多程序都会包含循环，所以时间上的消耗一般远远大于输入量；而框架会在程序运行中有创删，所以空间上的消耗一般都会小于输入量。

### 记忆化(Memoization)
以斐波那契数列的计算为例，计算 `fib(10)` 时，通过递归树可以发现，`fib(x)` 会被多次调用。

显然，对于同一个 `fib(x)`，当进行完第一次计算后，如果能够记录下其结果，就不必再进行重复的递归调用了。这种思想就被称为**记忆化(Memoization)**：

```python
def memo(f):
    cache = {}
    def memoized(n):
        if n not in cache:
            cache[n] = f(n)
        return cache[n]
    return memoized
```

### 复杂度(Orders of Growth)
通常来说，我们更加关注于**输入的增长规模**与**程序消耗**之间的关系，并以此来衡量程序的**效率**。

如果用 $n$ 代表程序的输入规模，$R(n)$ 代表程序消耗的资源，若存在 $$k_1 \times f(n) \leq R(n) \leq k_2 \times f(n)$$，则称 $R(n)$ 的复杂度为 $\Theta (f(n))$，记作 $R(n) = \Theta (f(n))$。

$f(n)$ 中，常系数、底数一般可以省略，如 $\Theta (50n) = \Theta (n)$, $\Theta (log n) = \Theta (log_2 n) = \Theta (log_10 n)$。

当 $f(n)$ 为多项式时，也会只取最高次项保留，如 $\Theta (n^2 + n) = \Theta (n^2)$ 。

## Week 10
目前为止，我们了解到了编程中的两大基本要素，即 **函数(functions)** 和 **数据(data)** 。我们知道两者之间有着密切的关系，函数通过 **高阶函数(higher-order functions)** 可以像数据一样处理，而数据通过 **消息传递(message passing)** 和 **对象系统(object system)** 可以被赋予行为。

接下来的主题就是第三大基本要素，即 **程序本身(programs themselves)** 。

Python 程序的本质就其实就是一大串文本，让它运行起来的是 **解释(interpretation)** 的过程。对于不同的解释的方式，亦即解释器，程序运行的结果自然也不相同。

而追根溯源， **解释器(interpreters)** 其实也是程序。这就意味着，我们可以自己去编写解释器去运行 Python 代码了。

解释器的通用结构是两个函数，一个用来执行 **环境(environment)** 中的表达式，一个用来执行函数。执行函数时需要执行内部的表达式，而执行表达式有时也需要执行函数，所以两个两者相互递归。

### Scheme
Scheme 由 Lisp 延伸而来，是如今广泛使用的第二古老的编程语言，仅次于 Fortran。它的运算模式类似于 Python，但是非常简单，没有类与对象、高阶函数、赋值、循环语句，而只有 **表达式(expressions)** 和 **符号运算(symbolic computation)** 。

此处是 CS61A 所搭建的[在线Scheme编辑](https://code.cs61a.org/scheme)。

#### 表达式(Expressions)
**表达式(expressions)** 分为 **调用表达式(call expression)** 和 **特型(special forms)** 。

调用表达式和 Python 中类似，由 **操作符(operator)** 和 **操作数(operand)** 构成，其两侧用小括号括起，如 `(quotient 10 2)`。

与 Python 类似，Scheme 也倾向于使用数学运算符号，但不同于 Python，Scheme 采用的是 **前缀表达式(prefix notation)**，如 `(+ (*3 5) (- 10 6))`。

调用表达式可以嵌套，也可以跨行编写：

```
(+ (* 3
       (+ (* 2 4)
          (+ 3 5)))
   (+ (-10 7)
     6))
```

特型与调用表达式的区别在于执行过程的不同。如特型 `(if <predicate> <consequent> <alternative>)`，会先判断 `<predicate>` 是否为真，进而执行 `<consequent>`，否则执行 `<alternative>`；而调用表达式 `(+ <operand_a> <operand_b>)`，会先计算出 `<operand_a>` 和 `<operand_b>`，而后进行相应运算。

`(if xxx xxx xxx)` 要处理多种情况时就要多次嵌套，难免会增大编写成本。相应的，`(cond (<p1> <e1>) (<p2> <e2>) ... [(else <else-expression>)])` 就可以用来处理这样的情况。`<p1> <p2> ... <pn>` 会被逐个计算，直到出现真值，就执行对应的 `<ei>`，若没有真值而存在 `(else <else-expression>)`，则去执行 `<else-expression>`。

Scheme 中的比较表达式依然采用前缀的形式，如 `(>= 2 1)`。其布尔值表示为 `#t` 或 `true`、`#f` 或 `false`，且布尔运算依然成立，如 `(and <e1> ... <en>)`、 `(or <e1> ... <en>)`、`(not <e>)`。

#### 定义(Definitions)
通过特型 `(define <symbol> <value>)` 可以将值 `<value>` 与 `<symbol>` 绑定，如 `(define pi 3.14)`；

通过特型 `(define (<symbol> <parameters>) <body>)` 可以定义函数，如 `(define (square x) (* x x))`。

定义完成后，就可以直接进行调用了，如 `(square (square pi))`。

Scheme 中定义的函数与 Python 中有同样的 **词法作用域(lexical scoping)** 规则，因此可以实现嵌套定义和递归：

```
(define (sqrt x)
  (define (good-enough? guess)
    (< (abs (- (square guess) x)) 0.001))
  (define (improve guess)
    (average guess (/ x guess)))
  (define (sqrt-iter guess)
    (if (good-enough? guess)
      guess
      (sqrt-iter (imrove guess))))
  (sqrt-iter 1.0))

(sqrt 9)
```

**匿名函数(Anonymous Functions)** 使用特型 `(lambda (<formal-parameters>) <body>)` 来创建，如 `(lambda (x) (+ x 4))`。它可以当作调用表达式被直接调用，如 `((lambda (x y z) (+ x y (square z))) 1 2 3)`。

对于函数来说，`(define (plus4 x) (+ x 4))` 与 `(define plus4 (lambda (x) (+ x 4)))` 是等价的。

#### 组合值(Compound Values)
**对组(Pairs)** 也存在于 Scheme 中，由内置函数 `cons` 来创建，以 `car` 与 `cdr` 对应，如：

```
(define x (cons 1 2))
(car x)
(cdr x)
```

同样，**递归列表(recursive lists)** 也存在，它就像 Python 中的 **链表(Linked List)** ，由嵌套的 `Pairs` 组成，以 `nil` 或 `'()` 来表示空列表，如：

```
(define one-through-four (list 1 2 3 4))
(car one-through-four)
(cdr one-through-four)
(car (cdr one-through-four))
(cons 10 one-through-four)
(cons 5 one-through-four)
```

`null? <list_name>` 用来检测列表是否为空，以此我们也可以得出一些列表的基本操作：

```
(define (length items)
  if (null? items)
    0
    (+ 1 (length (cdr items))))

(define (getitem items n)
  (if (= n 0)
    (car items)
    (getitem (cdr items) (- n 1))))

(define squares (list 1 4 9 16 25))
(length squares)
(getitem squares 3)
```

递归链表也内置有一些特型，如 `(append s t)` `(map f s)` `(filter f s)` `(apply f s)`。

#### 数据象征(Symblic Data)
要想能够使用**象征符号(Symbols)**，编程语言就应该能够 **引述(quote)** 数据对象。

**象征符号** 就像是 Python 中的 **名称(name)**，但不完全相同：  
象征符号是 Scheme 中的一种数据类型，当被读时为象征符号这一数据类型，而被求值时则是其所代表的数据对象；  
而 Python 中的名称只能是当作表达式来调用，去访问与其绑定的值，不会有 Python 表达式可以被操作成名称。  

举例来说，当我们定义了 `(define a 1)` `(define b 2)` 之后，当进行 `(list a b)` 时，其中的 `a b` 均为其所代表的值，而非 `a b` 的本身。

而如果我们想要的就是 `a b` 这两个符号，就可以在其前方加 `'`，表示 **引述(quote)**，如 `(list 'a 'b)` `(list a 'b)`。

其中 `'a` `'b` 其实是特型 `(quote a)` `(quote b)` 的缩写形式。

在 Scheme 中，不需要进行运算的表达式，就是被引述的。

## Week 10
### 异常(Exceptions)
异常，就是在程序运行出错时显示的错误信息。Python 中内含了许多异常，我们也可以自行决定在何时 **抛出(raise)** 何种 **异常(exceptions)** 。

异常也是一种类，由其基类 `BaseException` 继承而来，而某种异常便是一个实例了。通过语句 `raise Exception('Helper Message')` 可以自行抛出异常。

通常情况下，我们会如此处理异常：

```python
try:
    <try suite>
except <exception class> as <name>:
    <except suite>
```

其中 `try` 会首先被执行。若其中出现了 `<exception class>` 中所含的异常，则将 `name` 与此种异常绑定，并执行 `<except suite>` 。若未找到，则试图寻找最近的包含此种异常的 `<exception class>` 并执行其 `<except suite>` 。

以除零异常举例：

```python
def invert(x):
    result = 1/x
    print('Never printed if x is 0')
    return result

def invert_safe(x):
    try:
        return invert(x)
    except ZeroDivisionError as e:
        return str(e)

>>> invert_safe(2)
Never printed if x is 0
0.5
>>> invert_safe(0)
'division by zero'
```

## Week 11
本周主要为 `Project 4: Scheme Interpreter` 的细节原理讲解，在做项目的过程中自可习得。

## Week 12
### 函数式编程(Functional Programming)
#### 部分特点
1. 所有的函数都是 **纯函数(pure functions)** ；
2. 不存在 **重赋值(re-assignment)** 和 **可变数据类型(mutable data type)** ；
3. **命名(name)** 与 **值(value)** 的绑定是永久的。
#### 部分优势
1. 表达式的值与子表达式的计算顺序无关；
2. **懒计算(lazy computation)** ，即子表达式可以在任何时间被计算；
3. **引用透明(referential transparency)** ，即当用其值替代子表达式时，总表达式的值不会改变。

然而，对于一些纯函数语言，如 Scheme，不存在 `while\for` 语句，而只能用递归来实现相应操作。在这种情况下，要提高递归的效率，就引出了尾递归。

### 尾递归(Tail Recursion)
#### 判别
当一个递归函数只等待下一层递归而没有其他操作时，亦即下一层递归的调用是本层递归函数的最后执行语句时（就像尾巴一样），此递归函数就是可尾递归的。
#### 优化
因为本层递归只在等待着下层递归，这时本次递归所创建的框架就已经可以被释放掉了。这样一来，递归的空间复杂度就被降低到了 `O(1)` 。

## Week 13
### 数据库管理系统(Database Management System)
数据库(database)用于存放数据，具体方式是将数据存放在由行(raw)和列(column)组成的表格(Table)内。  

管理数据库的应用最广泛的语言是 SQL (Structured Query Language)，属于声明式语言(Declarative Language)，与之相对的是以 Python, Scheme 为代表的命令式语言(Imperative)。  前者告诉计算机所期盼的结果，由计算机自主执行过程；后者为计算机描述执行过程，计算机则照做。  

### SQL 基础语法
`select [expression] as [name], [expression] as [name], ... ;` 创建一个一行内容的表。  
 
`union` 将两张列数相同且同列中数据类型相同的表合为一张，两者的列名也可以不同，最终结果以第一个表为准。另外，合成之后的数据顺序也会被打乱。

`select` 与 `union` 结合的写法即为 `select [expression11], [expression12] union select [expression21], [expression22];`

`select` 只会显示结果，而不会进行保存。需要保存时会使用 `create table` 语句，其格式为 `create table [name] as [select statement];`

`select` 同样也可以从已经保存的表中获取数据：`select [column] as [name], [column] as [name], ... from [table] where [condition] order by [order];`

对多张表进行操作时，只需用 `,` 隔开即可，如 `select * from [table1], [table2], ...;`  

当多张表中含相同列名或表与自身进行操作时，需要进行区分，可用 **别名(Aliases)** 实现：`select [alias1].[column1], [alias2].[column2] from [table1] as [alias1], [table2] as [alias2] where ...;`

SQL 中也内置一些运算符，在 `select where` 和 `order by` 时都可以使用，其包含：
比较运算符：= > < <= >= <> !=
布尔运算符：AND OR
算术运算符：+ - * /
字符串连接：||
注：尽管有不少函数可以对字符串进行操作，但作为数据库管理语言，还是尽量少用为好。

### 聚合函数(Aggregate function)
在 `select` 时，如果 `select max([name1]), [name2] from [table]` ，其中 `[name2]` 会返回 `[name1]` 最大值所在行的 `[name2]`，但当 `[name2]` 的指向不唯一或不存在时，就会随机返回。

### 分组(Grouping)
`select [expression] as [name], [expression] as [name], ... from [table] group by [expression]` 可以将数据按 `[expression]` 分为多个组，此时的聚合函数会在各组分别被应用。
`select [...] from [...] group by [...] having [expression]` 按照 `[expression]` 筛选符合条件的组。

### 规范化表格(Modifying Tables)
创建： `create table`
删除： `drop table`
插入： `insert into`
更新： `update ... set ...=... where ...`
删行： `delete from ... where ... `


