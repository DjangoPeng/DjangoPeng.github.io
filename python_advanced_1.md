## Python 进阶系列1：Lambda函数

Python作为一门流行的编程语言，广受程序员与数据科学家的喜爱。如今的Python拥有丰富的第三方库和良好的生态发展，使其能够在数据科学、人工智能、web等各领域获得广泛的支持。Python进阶系列旨在分享一些Python的高级特性，如Lambda表达式、元类(metaclass)的使用、类(class)和类型(type)的统一、经典类与新式类区别与应用等。

#### Lambda函数

在计算机编程中，**匿名函数**（anonymous function）是指一类无需定义[标识符](https://zh.wikipedia.org/wiki/%E6%A0%87%E8%AF%86%E7%AC%A6)（函数名）的[函数](https://zh.wikipedia.org/wiki/%E5%87%BD%E6%95%B0)或[子程序](https://zh.wikipedia.org/wiki/%E5%AD%90%E7%A8%8B%E5%BA%8F)，普遍存在于多种编程语言中。1958年[LISP](https://zh.wikipedia.org/wiki/LISP)首先采用匿名函数，自此之后，越来越多编程语言陆续采用，如C++、Java、Python。

当你定义一个函数且仅需一次调用时，你会发现非常不划算，且污染了当前的名字空间。而匿名函数则有形式简单、无函数名冲突、方便实现闭包和高阶函数等优点。

Python支持在运行时动态创建Lambda函数（匿名函数）。下面简单对比lambda函数与普通函数在形式上的区别：

```python
>>> def f(x):
...     return x**2
... 
>>> g = lambda x : x**2
>>> [f(2), g(2)]
[4, 4]
```

函数f和g都实现了乘方运算，但lambda函数形式更简洁，无需"return"，而是直接返回表达式计算结果。如果只调用一次函数g，则无需将lambda函数赋值给g，可以直接调用lambda函数。

```python
>>>(lambda x : x**2)(2)
4
```

进一步，结合Python的map、reduce、filter函数，我们可以做出更酷炫的事：

```python
# 求[0,1,2,3,4,5,6,7,8,9]各自的平方
>>> map(lambda x : x**2, xrange(10))
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
# 求0~99的和
>>> reduce(lambda x, y: x + y, xrange(100))
4950
# 求0~10中的偶数
>>> filter(lambda x: x % 2==0, xrange(10))
[0, 2, 4, 6, 8]
```

如果用def定义普通函数来完成上面的工作，以求和为例，则显得过于冗长：

```python
>>> def sum(a,b):
...     return a + b
... 
>>> reduce(sum, xrange(100))
4950
```



