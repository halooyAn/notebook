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

---

# 魔术方法（四）控制属性的访问和设置

## `__getattr__`魔术方法：

在访问一个对象的某个属性的时候，如果这个属性不存在，那么就会执行`__getattr__`方法，将属性的名字传进去。如果这个属性存在，那么就不会调用`__getattr__`方法：

```py
    class Person(object):
        def __init__(self,name):
            self.name = name

        def __getattr__(self, item):
            if item == 'age':
                return 18
            else:
                return None

    p1 = Person('zhiliao')
    print(p1.name)
    print(p1.age)
```

## `__setattr__`魔术方法：

只要给一个对象的属性设置值，那么就会调用这个方法。但是要注意的是，不要在这个方法中调用`self.xxx=xxx`的形式，因为会产生递归调用。如果想要给对象的属性设置值，那么应该使用`__dict__`这个魔术属性。示例代码如下：

```py
    class Person(object):
        def __init__(self,name):
            self.name = name
            self.is_adult = False

        def __setattr__(self, key, value):
            if key == 'age':
                self.__dict__[key] = value
                if value >= 18:
                    self.__dict__['is_adult'] = True
                else:
                    self.__dict__['is_adult'] = False
            else:
                self.__dict__[key] = value
```

## `__getattribute__`魔术方法： {#getattribute魔术方法：}

这个魔术方法是，只要你访问了一个对象的属性，不管这个属性存不存在都会去执行这个方法，所以在写这个方法的时候要小心循环调用。这个方法只能在新式类中使用，不能在旧时类中使用。示例代码如下：

```py
    class Person(object):
        def __init__(self,name):
            self.name = name

        def __getattribute__(self, item):
            print(item)
            return super(Person, self).__getattribute__(item)
```

---

# 魔术方法（五）创建定制的序列 {#魔术方法（五）——创建定制的序列}

在`Python`中，内置了一些数据类型。比如列表、元组、字典、集合等。这些数据类型之所以能够表现出一些序列的行为（下标操作、遍历等），是因为他们实现了一些协议，或者一些魔术方法。如果我们自己写一个类，也实现这些协议或者魔术方法，其实我们自己也可以定义属于自己的序列。

## 一个序列容器的魔术方法： {#一个序列容器的魔术方法：}

1. `__len__(self)`：在使用`len(obj)`函数的时候会调用这个魔术方法。

2. `__getitem__(self,key)`：在使用下标操作`temp['key']`以及切片操作的时候会执行这个魔术方法。

3. `__setitem__(self,key,value)`：在给这个容器设置`key`和`value`的时候会调用这个魔术方法。

4. `__delitem__(self,key)`：在删除容器中的某个`key`对应的这个值的时候会调用这个魔术方法。

5. `__iter__(self)`：在遍历这个容器的时候，会调用容器的这个方法，然后返回一个迭代器，再调用这个迭代器的`__next__`方法。

6. `__reversed__(self)`：在调用`reversed(obj)`函数的时候会调用这个方法。 以下代码将上面这些魔术方法总结起来，写一个自定义的容器，并且能够实现`head`、`tail`、`last`、`drop`和`take`的列表类。

代码示例如下：

```py
    class ZLList(object):
     def __init__(self,values=None):
         if values is None:
             self.values = []
         else:
             self.values = values

     def __iter__(self):
         return iter(self.values)

     def __len__(self):
         return len(self.values)

     def __getitem__(self, item):
         if isinstance(item,slice):
             # 介绍下slice数据类型
             # return self.values[item.start:item.end:item:step]
         # else:
             # return self.values[item]
         return self.values[item]

     def __setitem__(self, key, value):
         
         self.values[key] = value

     def __delitem__(self, key):
         del self.values[key]

     def __reversed__(self):
         return reversed(self.values)

     def append(self,value):
         self.values.append(value)

     def remove(self,value):
         return self.values.remove(value)

     def head(self):
         return self.values[0]

     def tail(self):
         return self.values[-1]

     def take(self,n):
         return self.values[:n]
```

## 可变容器和不可变容器：

可变容器可以对容器中的元素进行更改，比如删除一个元素，添加一个容器。而不可变容器不能做这些操作。因此如果你向定义一个不可变容器，那么不应该实现`__setitem__`、`__delitem__`方法。

---

# 魔术方法（六）可调用的对象 {#魔术方法（六）——可调用的对象}

在`Python`中，一个特殊的魔术方法可以让类的实例的行为表现的像函数一样，你可以调用他们，将一个函数当做一个参数传到另外一个函数中等等。这是一个非常强大的特性让`Python`编程更加舒适甜美。`__call__(self, [args...])`

允许一个类的实例像函数一样被调用。实质上说，这意味着`x()`与`x.__call__()`是相同的。注意`__call__`参数可变。这意味着你可以定义`__call__`为其他你想要的函数，无论有多少个参数。

例子如下：

```py
    class Coornidate(object):
        def __init__(self,x,y):
            self.x = x
            self.y = y

        def __call__(self,x,y):
            self.x,self.y = x,y

        def __str__(self):
            return '(%s,%s)' % (self.x,self.y)

    c1 = Coornidate(1,2)
    print(callable(c1))
    c1(2,3)
    print(c1)
```

---

# 魔术方法（七）会话管理



