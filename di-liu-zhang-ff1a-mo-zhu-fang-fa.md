# 魔术方法（一）常见的魔术方法：

## `__init__`魔术方法： {#init魔术方法：}

定义类的初始构造函数方法。

## `__del__`魔术方法：

对象即将被释放的时候，会调用这个方法。注意如果这个对象产生了循环引用，并且实现了**del**方法，那么这个对象将得不到释放，从而产生内存泄漏。因此慎用这个方法。

## `__new__`魔术方法：

这个方法用来创建对象。如果你想在创建对象的时候做些事情，那么你可以重写这个方法。

## `__class__`魔术属性：

用来返回这个对象所属的类。如果一个类调用这个属性，那么得到的是这个类的元类。

## `__iter__`魔术方法：

返回一个迭代器。

## `__next__`魔术方法：

迭代器在被遍历的时候会每遍历一次调用这个方法（在Python3中，在Python2中使用的是next方法）。

## `__str__`魔术方法：

在打印某个对象的时候，通常显示的是这个对象所属类的名字以及内存地址，这种样子是不适合人类来阅读的。因此如果你想在打印某个对象的时候能更加友好一点，那么你可以自己重写这个方法，并且你自己定义好字符串再返回：  
在没有重写`__str__`函数的时候，打印出来的对象是类名+地址：

```py
  class Person(object):
      def __init__(self,name):
          self.name = name
  p1 = Person('zhiliao')
  print(p1)
  # 打印出来的结果：
  # <__main__.Person object at 0x008AD170>
```

而重写了**str**方法，那么打印出来的就是**str**的返回值：

```py
  class Person(object):
      def __init__(self,name):
          self.name = name
      def __str__(self):
          return 'Person<%s>' % self.name
  p1 = Person('zhiliao')
  print(p1)
  # 打印出来的结果：
  # Person<zhiliao>
```

同样，如果通过`str(p1)`的方式，将p1这个对象转换为字符串，那么也会调用这个对象的`__str__`方法。

## `__repr__`：

这个魔术方法是用来表述一个对象的，用于给机器看的。这样说可能不好理解，这里以两个小案例说明这个魔术方法的一个用法：

**第一种是:**

如果在终端定义了一个类，然后初始化一个对象，然后直接输入这个对象接着按回车，这时候会使用`__repr__`魔术方法返回的值  
![](/assets/终端repr.png)

**第二种是：**

在`pycharm`中，如果将一个对象创建完成后，放到一个列表中，然后再打印这个列表，那么会打印这个列表中所有的对象，这时候会调用`__repr__`魔术方法，示例代码如下：

```py
    class Person(object):
        def __init__(self,name):
            self.name = name

        def __repr__(self):
            return "Person(%s)" % self.name

    p1 = Person('zhiliao')
    p2 = Person('ketang')
    a = [p1,p2]
    print(a)
    # 打印的结果：
    [Person(zhiliao), Person(ketang)]
```

## `__dict__`魔术属性：

这个属性装的是所有用户自定义的属性。

---

# 魔术方法（二）用来比较的魔术方法：

有些时候我们想要比较两个对象。比如哪个对象更大，哪个对象更小，两个对象是否相等。如果我们不告诉`Python`要根据什么方式比较，那么`Python`是不知道如何比较的。这时候我们就可以实现一些比较的魔术方法来达到我们的需求。

## `__cmp__(self,other)`魔术方法：

这个魔术方法是**Python2**中用来比较大小的。这个方法接收另外一个参数用来作为比较的。如果`self`比`other`大，那么应该返回一个正数，如果`self`和`other`相等，那么应该返回0，如果`self`比`other`小，那么应该返回一个负数。这里以一个小案例来讲解这个方法的使用：

```py
    class Person(object):
        def __init__(self,name,age,height):
            self.name = name
            self.age = age
            self.height = height

        def __cmp__(self, other):
            if self.age == other.age:
                if self.height == self.height:
                    return 0
                else:
                    return 1 if self.height > other.height else -1
            return 1 if self.age > other.age else -1
```

`__cmp__`方法是在**Python2**中才能使用，在Python3中已经废弃这个方法了。因此不建议使用这个方法。

## `__eq__(self,other)`魔术方法：

在使用`==`比较运算符比较两个对象是否相等的时候会调用这个方法。如果是相等，那么应该返回`True`，否则返回`False`：

```py
    class Person(object):
        def __init__(self,name,age,height):
            self.name = name
            self.age = age
            self.height = height

        def __eq__(self, other):
            # self == other
            if self.age == other.age:
                if self.height == other.height:
                    return True
                else:
                    return True if self.height == other.height else False
```

## `__ne__(self,other)`魔术方法：

在使用`!=`比较运算符来比较两个对象是否不相等的时候会调用这个方法。如果这两个对象不想等，那么应该返回`True`，否则返回`False`：

```py
    class Person(object):
        def __init__(self,name,age,height):
            self.name = name
            self.age = age
            self.height = height

        def __ne__(self, other):
            # self != other
            return False if self.age == other.age and self.height == other.height else True
```

## `__lt__(self,other)`魔术方法：

在使用`<`比较运算符来比比较两个对象大小的时候会调用这个方法。如果`self<other`，那么应该返回`True`，否则返回`False`。示例代码如下：

```py
    class Person(object):
        def __init__(self,name,age,height):
            self.name = name
            self.age = age
            self.height = height

        def __lt__(self, other):
            # self < other
            if self.age == other.age:
                return True if self.height < other.height else False
            return self.age < other.age
```

## `__gt__(self,other)`魔术方法：

在使用`>`比较运算符的时候会调用这个方法。如果`self>other`，那么应该返回`True`，否则返回`False`。示例代码如下：

```py
    class Person(object):
        def __init__(self,name,age,height):
            self.name = name
            self.age = age
            self.height = height

        def __gt__(self, other):
            # self > other
            if self.age == other.age:
                return True if self.height > other.height else False
            return self.age > other.age
```

## `<=`和`>=`的解决方案：

以上讲了`<`、`>`、`==`以及`!=`的执行方法。没有讲`<=`和`>=`这两个运算符执行的方法，其实这两个方法是这样执行的，拿`<=`为例来讲，首先执行`<`判断，如果为`False`，那么会再执行`==`判断，如果都为`False`，那么就返回`False`。

---

# 魔术方法（三）运算符魔术方法:

## 一元操作符和函数：

1. `__pos__(self)`魔术方法：在这个对象前面使用`+`的时候执行的方法。

2. `__neg__(self)`魔术方法：在这个对象前面使用`-`的时候执行的方法。

3. `__abs__(self)`魔术方法：在这个对象上使用`abs`函数的时候执行的方法。

4. `__invert__(self)`魔术方法：在这个对象前面使用`~`的时候执行的方法。

相关的示例代码如下：

```py
    class Coornidate(object):  
        def __init__(self,x,y):  
            self.x = x  
            self.y = y

        def _pos__(self):  
            return self

        def __neg__(self):  
            new_coornidate = Coornidate(-self.x,-self.y)  
            return new_coornidate

        def __abs__(self):  
            new_coornidate = Coornidate(abs(self.x),abs(self.y))  
            return new_coornidate

        def __invert__(self):  
            new_coornidate = Coornidate(255-self.x,255-self.y)  
            return new_coornidate

        def __str__(self):  
            return "(%s,%s)" % (self.x,self.y)

    c1 = Coornidate(-1,2)  
    c2 = +c1  
    c3 = -c1  
    c4 = abs(c1)  
    c5 = ~c1  
    print(c5)
```

## 普通算数操作符：

1. `__add__(self,other)`魔术方法：在两个对象相加的时候执行的方法。

2. `__sub__(self,other)`魔术方法：在两个对象相减的时候执行的方法。

3. `__mul__(self,other)`魔术方法：在两个对象相乘的时候执行的方法。

4. `__floordiv__(self,other)`魔术方法：在两个对象使用`//`运算的时候执行的方法。

5. `__div__(self,other)`魔术方法：在两个对象使用`/`运算的时候执行的方法。

6. `__truediv__(self,other)`魔术方法：在两个对象之间使用真除的时候执行的方法。在`Python3`中使用`/`运算的时候会执行这个方法。在`Python2`中，默认使用`__div__方法`，如果`from __future__ import division`，那么将会使用`__truediv__`方法。

7. `__mod__(self,other)`魔术方法：在使用`%`取模运算的时候执行的方法。

相关示例代码如下：

```py
    class Coornidate(object):
         def __init__(self,x,y):
             self.x = x
             self.y = y

         def __add__(self, other):
             new_coornidate = Coornidate(self.x+other.x,self.y+other.y)
             return new_coornidate

         def __sub__(self, other):
             new_coornidate = Coornidate(self.x-other.x,self.y-other.y)
             return new_coornidate

         def __mul__(self, other):
             new_coornidate = Coornidate(self.x*other.x,self.y*other.y)
             return new_coornidate

         def __floordiv__(self, other):
             new_coornidate = Coornidate(self.x//other.x,self.y//other.y)
             return new_coornidate

         def __div__(self, other):
             new_coornidate = Coornidate(self.x/other.x,self.y/other.y)
             return new_coornidate

         def __truediv__(self, other):
             new_coornidate = Coornidate(self.x/other.x,self.y/other.y)
             return new_coornidate

         def __mod__(self, other):
             new_coornidate = Coornidate(self.x%other.x,self.y%other.y)
             return new_coornidate

         def __str__(self):
             return "(%s,%s)" % (self.x,self.y)

     c1 = Coornidate(4,6)
     c2 = Coornidate(3,4)
     c3 = c1+c2
     c4 = c1 - c2
     c5 = c1*c2
     c6 = c1/c2
     c7 = c1%c2
     print(c7)
```

## 增量赋值：

1. `__iadd__(self,other)`魔术方法：在给对象做`+=`运算的时候会执行的方法。

2. `__isub__(self,other)`魔术方法：在给对象做`-=`运算的时候会执行的方法。

3. `__imul__(self,other)`魔术方法：在给对象做`*=`运算的时候会执行的方法。

4. `__idiv__(self,other)`魔术方法：在给对象做`/=`运算的时候会执行的方法。

5. `__itruediv__(self,other)`魔术方法：在给对象做真`/=`运算的时候会执行的方法。

相关示例代码如下：

```py
    class Coornidate(object):
        def __init__(self,x,y):
            self.x = x
            self.y = y

        def __iadd__(self, other):
            self.x += other
            self.y += other
            return self

        def __isub__(self, other):
            new_coordinate = Coornidate(self.x-other,self.y-other)
            return new_coordinate

        def __imul__(self, other):
            new_coordinate = Coornidate(self.x*other,self.y*other)
            return new_coordinate

        def __itruediv__(self, other):
            new_coordinate = Coornidate(self.x/other,self.y/other)
            return new_coordinate

        def __str__(self):
            return "(%s,%s)" % (self.x,self.y)

    c1 = Coornidate(4,6)
    # c1 += 1
    # c1 -= 1
    # c1 *= 1
    # c1 /= 2
    print(c1)
```



