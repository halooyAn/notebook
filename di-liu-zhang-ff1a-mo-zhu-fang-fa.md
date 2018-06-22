# 魔术方法——常见的魔术方法： {#魔术方法（一）}

## `__init__`魔术方法： {#init魔术方法：}

  
定义类的初始构造函数方法。

  
## `__del__`魔术方法：

对象即将被释放的时候，会调用这个方法。注意如果这个对象产生了循环引用，并且实现了__del__方法，那么这个对象将得不到释放，从而产生内存泄漏。因此慎用这个方法。
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
而重写了__str__方法，那么打印出来的就是__str__的返回值：
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

**第一种是:**如果在终端定义了一个类，然后初始化一个对象，然后直接输入这个对象接着按回车，这时候会使用`__repr__`魔术方法返回的值：
![](/assets/终端repr.png)

**第二种是：**在`pycharm`中，如果将一个对象创建完成后，放到一个列表中，然后再打印这个列表，那么会打印这个列表中所有的对象，这时候会调用`__repr__`魔术方法，示例代码如下：
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



