# 魔术方法（一）——常见的魔术方法：

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

`__dict__`是用来存储对象属性的一个字典，其键为属性名，值为属性的值。并不是所有对象都拥有`__dict__`属性。许多内建类型就没有`__dict__`属性，如`list`，此时就需要用`dir()`来列出对象的所有属性。

1. 实例的`__dict__`仅存储与该实例相关的实例属性，正是因为实例的`__dict__`属性，每个实例的实例属性才会互不影响。

2. 类的`__dict__`存储所有实例共享的变量和函数(类属性，方法等)，类的`__dict__`并不包含其父类的属性。

*  `dir()`是Python提供的一个`API`函数，`dir()`函数会自动寻找一个对象的所有属性(包括从父类中继承的属性),返回一个列表。

​ 一个实例的`__dict__`属性仅仅是那个实例的实例属性的集合，并不包含该实例的所有有效属性。所以如果想获取一个对象所有有效属性，应使用`dir()`。

---

# 魔术方法（二）——用来比较的魔术方法：

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

# 魔术方法（三）——运算符魔术方法:

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

# 魔术方法（四）——控制属性的访问和设置

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
            if key == "age":
                self.__dict__['key'] = value
                if value >= 18:
                    self.__dict__['is_adult'] = True
                else:
                    self.__dict__['is_adult'] = False
            else:
                self.__dict__['key'] = value
    
    
    person1 = Person("xiaobai")
    person1.age = 19
    print(person1.is_adult)
```

## `__getattribute__`魔术方法： {#getattribute魔术方法：}

当访问某个实例属性时， getattribute会被无条件调用，如未实现自己的getattr方法，会抛出AttributeError提示找不到这个属性，如果自定义了自己getattr方法的话，方法会在这种找不到属性的情况下被调用，比如上面的例子中的情况。所以在找不到属性的情况下通过实现自定义的getattr方法来实现一些功能是一个不错的方式，因为它不会像getattribute方法每次都会调用可能会影响一些正常情况下的属性访问。                                                                                                                                                                                                                            在写这个方法的时候要小心循环调用。这个方法只能在新式类中使用，不能在旧时类中使用。示例代码如下：

```py
    class Person(object):
        def __init__(self,name):
            self.name = name
            self.is_adult = False
    
        def __getattr__(self, item):
    
                return "default"
    
    
        def __getattribute__(self, item):
            print(item)
            return super(Person, self).__getattribute__(item)
    
    person1 = Person("xiaobai")
    
    print(person1.age)
```

---

# 魔术方法（五）——创建定制的序列 {#魔术方法（五）——创建定制的序列}

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

# 魔术方法（六）——可调用的对象 {#魔术方法（六）——可调用的对象}

在`Python`中，一个特殊的魔术方法可以让类的实例的行为表现的像函数一样，你可以调用他们，将一个函数当做一个参数传到另外一个函数中等等。这是一个非常强大的特性让`Python`编程更加舒适甜美。`__call__(self, [args...])`

允许一个类的实例像函数一样被调用。实质上说，这意味着`x()`与`x.__call__()`是相同的。注意`__call__`参数可变。这意味着你可以定义`__call__`为其他你想要的函数，无论有多少个参数。

例子如下：

```py
    class Person(object):
        def __init__(self,x,y):
            self.x = x
            self.y = y
    
        def __str__(self):
            return "(%d,%d)" %(self.x,self.y)
    
        def __call__(self,x,y):
            self.x = x
            self.y = y
    person1 = Person(12,18)
    print(person1)
    person1(13,11)
    print(person1)
```

---

# 魔术方法（七）——会话管理
在`Python2.5`中，为了代码利用定义了一个新的关键词`with`语句。之前在讲文件操作的时候，用过以下代码来打开一个文件以及关闭一个文件：

```py
with open('xxx.txt','r') as fp:
    print(fp.read())
```
那么这种代码底层的原理是什么呢？这种代码专业术语叫做会话控制器，他通过控制两个魔术方法：`__enter__(self)`以及`__exit__(self,exception_type,exception_value,traceback)`来定义一个代码块被执行或者终止后会话管理器应该做什么。他可以被用来处理异常，清除工作或者做一些代码块执行完毕之后的日常工作。如果代码执行成功，没有任何异常，那么`exception_type`、`exception_value`以及`traceback`将会是`None`。否则的话你可以选择处理这个异常或者是直接交给用户处理。如果你想处理这个异常的话，那么必须在`__exit__`在所有结束之后返回`True`。
这里自定义一个文件打开器，示例代码如下：
```py
    class FileOpener(object):
        def __init__(self,*args,**kwargs):
            self.args = args
            self.kwargs = kwargs
    
        def __enter__(self):
            self.fp = open(*self.args,**self.kwargs)
            return self.fp
    
        def __exit__(self, exc_type, exc_val, exc_tb):
            self.fp.close()
            print(exc_type)
            return False
    
    
    with FileOpener('test.txt','r') as fp:
        a = 1
        b = 0
        # c = a/b
        print(fp.read())
```

____
# 魔术方法（八）——序列化对象
有时候一个对象，想要保存在硬盘中。这时候我们就需要使用到对象持久化的技术了。在`Python`中，如果要将一个对象存储到硬盘中，需要使用`pickle`模块，其中`dump`方法可以将一个对象存到硬盘中，`load`方法可以从硬盘中加载一个对象。`Python`许多内置的数据结构是可以直接序列化的，比如：字典、列表、元组、字符串等。以下举几个例子来介绍下序列化的大体步骤：

```py
    import pickle
    
    a= {
        "a":[1,2,3],
        "b":"zimu",
        "c":False
    }
    
    # 写入对象到硬盘中
    with open("dump_obj.pkl","wb") as fp :
        pickle.dump(a,fp)
    
    # 从硬盘中加载对象
    with open("dump_obj.pkl","rb") as fp1:
        date = pickle.load(fp1)
        # for value in date.values():
        #     print(value)
        # for key in date.keys():
        #     print(key)
        for item in date.items():
            print(item)
    
```

## 自己定义可持续化的对象：
自己定义的类的对象，默认情况下是不能持续化的。如果想要让自定义的对象可持续化，那么应该实现两个魔术方法：第一个是`__getstate__`，这个魔术方法在把对象存储到硬盘中的时候会调用的，会将这个方法的返回值存储进去，返回值应该是可以持续化的数据类型，比如字典、列表、字符串等；第二个是`__setstate__`，这个魔术方法是从硬盘中加载对象的时候，会调用，会将你之前存储进去的值，通过参数的形式传递进来。以下举个例子来说明下：
```py
    import pickle
    
    class Person(object):
        def __init__(self,name,age):
            self.name = name
            self.age = age
    
        def __getstate__(self):
            return {"name":self.name,"age":self.age}
    
        def __setstate__(self, state):
            self.name = state["name"]
            self.age = state["age"]
    
        def __str__(self):
    
            return "<name:%s,age:%d>" % (self.name,self.age)
    
    
    person = Person("xiaobai",19)
    
    with open("person.pkl","wb") as fp:
        pickle.dump(person,fp)
    
    with open("person.pkl","rb") as fp2:
        data = pickle.load(fp2)
        print(data)
    
```
## 哪些数据结构是可持续化的：
列表、字典、字符串、元组、整型、集合、浮点类型、布尔类型。

## 更多：
`pickle`并不是很完美， `Pickle`文件很容易被不小心或者故意损坏， `Pickle`文件比纯文本文件要稍微安全一点，但是还是可以被利用运行恶意程序。 `Pickle`不是跨版本兼容的，所以尽量不要去分发 `Pickle`过的文本，因为别人并不一定能够打开。不过在做缓存或者其他需要序列化数据的时候， `Pickle`还是很有用处的。




