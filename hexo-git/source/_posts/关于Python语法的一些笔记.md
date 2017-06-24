---
title: 关于Python语法的一些笔记（持续更新）
date: 2017-04-16 20:56:37
tags:
	Python
---

## 写在前面

记录一些Python使用过程中的没见过或者优化的使用方法……

### 什么是**args和***kwargs？

先来看个例子：

```python
def foo(*args, **kwargs):
    print 'args = ', args
    print 'kwargs = ', kwargs
    print '---------------------------------------'

if __name__ == '__main__':
    foo(1,2,3,4)
    foo(a=1,b=2,c=3)
    foo(1,2,3,4, a=1,b=2,c=3)
    foo('a', 1, None, a=1, b='2', c=3)
```
<!-- more -->
输出结果如下：
```python
args =  (1, 2, 3, 4) 
kwargs =  {} 
--------------------------------------- 
args =  () 
kwargs =  {'a': 1, 'c': 3, 'b': 2} 
--------------------------------------- 
args =  (1, 2, 3, 4) 
kwargs =  {'a': 1, 'c': 3, 'b': 2} 
--------------------------------------- 
args =  ('a', 1, None) 
kwargs =  {'a': 1, 'c': 3, 'b': '2'} 
---------------------------------------
```

可以看到，这两个是python中的可变参数。*args表示任何多个无名参数，它是一个tuple；**kwargs表示关键字参数，它是一个dict。并且同时使用*args和**kwargs时，必须*args参数列要在**kwargs前，像foo(a=1, b='2', c=3, a', 1, None, )这样调用的话，会提示语法错误“SyntaxError: non-keyword arg after keyword arg”。

 还有一个很漂亮的用法，就是创建字典：
```python
    def kw_dict(**kwargs):
        return kwargs
    print kw_dict(a=1,b=2,c=3) == {'a':1, 'b':2, 'c':3}
```
其实python中就带有dict类，使用dict(a=1,b=2,c=3)即可创建一个字典了。

### lambda表达式以及实例

lambda表达式可以理解成为匿名函数，其功能就是定义了一个函数，不过这个函数形式非常简单，通常是一行。过于复杂的函数还是正经定义的好。

#### sorted函数中使用lambda表达式的例子：

下面这个例子在很多list排序中使用到，故单独列出来。

```python
points = [[10,16], [2,8], [1,6], [7,12]]
#用sorted函数来为list中的元素按照元素的第二位进行排序，key是排序的比较关键字，这里通过lambda表达式来给出关键字。
points = sorted(points, key = lambda x: x[1])
```


#### 简单例子:
```python
g = lambda x:x+1
```
执行结果：
```python
g(1)
>>>2
g(2)
>>>3
```
另外一种使用方式:
```python
lambda x:x+1(1)
>>>2
```
可以这样认为,lambda作为一个表达式，定义了一个匿名函数，上例的代码x为入口参数，x+1为函数体。非常容易理解，在这里lambda简化了函数定义的书写形式。使得代码更为简洁，但是使用函数的定义方式更为直观，易理解。

Python中，也有几个定义好的全局函数方便使用的，filter, map, reduce：
```python
>>> foo = [2, 18, 9, 22, 17, 24, 8, 12, 27]
>>> print filter(lambda x: x % 3 == 0, foo)
[18, 9, 24, 12, 27]
>>> print map(lambda x: x * 2 + 10, foo)
[14, 46, 28, 54, 44, 58, 26, 34, 64]
>>> print reduce(lambda x, y: x + y, foo)
139
```

### range和xrange的区别

**range**
    函数说明：range([start,] stop[, step])，根据start与stop指定的范围以及step设定的步长，生成一个**list**。
range示例: 
```python
>>> range(5) 
[0, 1, 2, 3, 4] 
>>> range(1,5) 
[1, 2, 3, 4] 
>>> range(0,6,2)
[0, 2, 4]
```

**xrange**
    函数说明：用法与range完全相同，所不同的是生成的不是一个数组，而是一个**generator**。
xrange示例: 
```python
>>> xrange(5)
xrange(5)
>>> list(xrange(5))
[0, 1, 2, 3, 4]
>>> xrange(1,5)
xrange(1, 5)
>>> list(xrange(1,5))
[1, 2, 3, 4]
>>> xrange(0,6,2)
xrange(0, 6, 2)
>>> list(xrange(0,6,2))
[0, 2, 4]
```
由上面的示例可以知道：要生成很大的数字序列的时候，用xrange会比range性能优很多，因为不需要一上来就开辟一块很大的内存空间，这两个基本上都是在循环的时候用：

```python
for i in range(0, 100): 
	print i 
for i in xrange(0, 100): 
	print i 
```

这两个输出的结果都是一样的，实际上有很多不同，range会直接生成一个list对象：
```python
a = range(0,100) 
print type(a) 
print a 
print a[0], a[1] 
```
输出结果：
```python
<type 'list'>
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51, 52, 53, 54, 55, 56, 57, 58, 59, 60, 61, 62, 63, 64, 65, 66, 67, 68, 69, 70, 71, 72, 73, 74, 75, 76, 77, 78, 79, 80, 81, 82, 83, 84, 85, 86, 87, 88, 89, 90, 91, 92, 93, 94, 95, 96, 97, 98, 99]
0 1
```

而xrange则不会直接生成一个list，而是每次调用返回其中的一个值：
```python
a = xrange(0,100) 
print type(a) 
print a 
print a[0], a[1] 
```
输出结果：
```python
<type 'xrange'>
xrange(100)
0 1
```

所以**xrange做循环的性能比range好，尤其是返回很大的时候，尽量用xrange吧**，除非你是要返回一个列表。

### for循环中的`_`

一段代码:
```python
for _ in xrange(10):
	block
```
如果循环中block不需要用到迭代值的话，我们可以不用给迭代值命名，用`_`就好。

### if-else语句的简单赋值
示例:
```python
a = 20 if b == 2 else 3 #如果b==2,则a被赋值为20，否则被赋值为3
```
很多其他语言，如C++, Java都有这种赋值语句:
```c
a = b==2? 20:3
```

---
Reference:

http://www.cnblogs.com/fengmk2/archive/2008/04/21/1163766.html
