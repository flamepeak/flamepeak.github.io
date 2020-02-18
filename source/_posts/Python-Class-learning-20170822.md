---
title: Python 类相关知识
date: 2017-08-22 10:21:29
tags: [Python]
categories: [Python]
---

### Basic

```Python
#类定义 
class people: 
    #定义基本属性 
    name = '' 
    age = 0
    #定义私有属性,私有属性在类外部无法直接进行访问 
    __weight = 0
    #定义构造方法 
    def __init__(self,n,a,w): 
        self.name = n 
        self.age = a 
        self.__weight = w 
    def speak(self): 
        print("%s is speaking: I am %d years old" %(self.name,self.age)) 

p = people('tom',10,30) 
p.speak()
```



---

### 继承

参考：[python中继承一个类](http://blog.csdn.net/goodluckac/article/details/53100957)

如果已经定义了Person类，需要定义新的Student和Teacher类时，可以直接从Person类继承：

```Python
class Person(object):
    def __init__(self, name, gender):
        self.name = name
        self.gender = gender
```

定义Student类时，只需要把额外的属性加上，例如score：

```Python
class Student(Person):
    def __init__(self, name, gender, score):
        super(Student, self).__init__(name, gender)
        self.score = score
```
一定要用 `super(Student, self).__init__(name, gender)` 去初始化父类，否则，继承自 Person 的 Student 将没有 name 和 gender。
函数`super(Student, self)`将返回当前类继承的父类，即 Person ，然后调用__init__()方法，注意self参数已在super()中传入，在__init__()中将隐式传递，不需要写出（也不能写）。

#### __init__ 方法

If a base class has an `__init__()` method the derived class's `__init__()` method must explicitly call it to ensure proper initialization of the base class part of the instance; for example: `BaseClass.__init__(self, [args...])`
As a special contraint on constructors, no value may be returned; doing so will cause a TypeError to be raised at runtime.

如果其基类也具有`__init__()`, 必须显式地在`__init__()`调用它, 以保证能够适当地初始化它的基类部分;例如: `BaseClass.__init__(self, [args...])`作为构造器的特殊情况, 它没有值被返回, 如果返回某个值, 会在运行时抛出异常TypeError.

**1. 子类定义了__init__方法时若未显示调用基类__init__方法，python不会帮你调用**

```Python
class A():
    def __init__(self):
        print 'a'
class B(A):
    def __init__(self):
        print 'b'

if __name__=='__main__':
      b=B()
>>> 
b
```

**2. 子类未定义__init__方法时,python会自动帮你调用首个基类的__init__方法，注意是首个。即：子类继承自多个基类时，只有第一个基类的__init__方法会被调用到:**

```Python
class A:
  def __init__(self):
    print 'a'
class B:
  def __init__(self):
    print 'b'
class C(B):
  def __init__(self):
    print 'c'
  pass
class D1(A,B,C):
  pass
class D2(B,A,C):
  pass
class D3(C,B,A):
  pass
if(__name__=='__main__'):
  print 'd1------->:'
  d1=D1()
  print 'd2------->:'
  d2=D2()
  print 'd3------->:'
  d3=D3()
>>> 
d1------->:
a
d2------->:
b
d3------->:
c
```

**3. 基类未定义__init__方法时，若此时子类显示调用基类__init__方法时，python向上超找基类的基类的__init__方法并调用,实质同2**

```Python
class A:
  def __init__(self):
    print 'a'
class B:
  def __init__(self):
    print 'b'
class C1(B,A):
  pass
class C2(A,B):
  pass
class D1(C1):
  def __init__(self):
    C1.__init__(self)
class D2(C2):
  def __init__(self):
    C2.__init__(self)
if(__name__=='__main__'):
  print 'd1------->:'
  d1=D1()
  print 'd2------->:'
  d2=D2()
>>> 
d1------->:
b
d2------->:
a
```
