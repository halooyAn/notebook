# 对象池 {#对象池}

## 小整数对象池： {#小整数对象池：}

在一个程序中，\[-5,256\]之间的数字是经常被使用的。因此`Python`在程序运行过程中，定义好了一个小整数池，也就是说从-5到256之间的数字都是提前在内存中建立好的，不会被系统回收，并且以后你创建了一个变量是在这个范围之内，那么都是同一个对象。同样对于单个字母也是一样的。

示例：

```py
    a = 1
    b = 1
    # 以下两个值是一样的
    print(id(a))
    print(id(b))
    a = -6
    b = -6
    # 以下两个值是不一样的
    print(id(a))
    print(id(b))
    # 以下两个值是不一样的
    a = 257
    b = 257
    # 以下两个值是不一样的
    print(id(a))
    print(id(b))
```

## 对于只含英文字符的字符串，共用同一个对象： {#对于只含英文字符的字符串，共用同一个对象：}

如果两个字符串的字符都是一样，并且只包含英文字符。那么会共用同一个对象。示例代码如下：

```py
    a = "helloworld"
    b = "helloworld"
    # 以下两个值会相等
    print(id(a))
    print(id(b))

    a = "hello world"
    b = "hello world"
    # 以下两个值不想等
    print(id(a))
    print(id(b))
```

# Python垃圾回收机制 {#python垃圾回收机制}

## 引用计数： {#引用计数：}

在`Python`中，使用了引用计数这一技术实现内存管理。一个对象被创建完成后就有一个变量指向他，那么就说明他的引用计数为1，以后如果有其他变量指向他，引用计数也会相应增加，如果将一个变量不再执行这个对象，那么这个对象的引用计数减1。如果一个对象没有任何变量指向他，也即引用计数为0，那么这个对象会被`Python`回收。示例代码如下：

```py
    class Person(object):
        def __init__(self,name):
            self.name = name

        def __del__(self):
            print('%s执行了del函数'%self.name)

    while True:
        p1 = Person('p1')
        p2 = Person('p2')
        del p1
        del p2
        a = input('test:')
```

## 循环引用： {#循环引用：}

引用计数这一技术虽然可以在一定程度上解决内存管理的问题。但是还是有不能解决的问题，即**循环引用**。比如现在有两个对象分别为a和b，a指向了b，b又指向了a，那么他们两的引用计数永远都不会为0。也即永远得不到回收。看以下示例：
```py
    class Person(object):
        def __init__(self,name):
            self.name = name
    
        def __del__(self):
            print('%s执行了del函数'%self.name)
    
    while True:
        p1 = Person('p1')
        p2 = Person('p2')
        # 循环引用后，永远得不到释放
        p1.next = p2
        p2.prev = p1
        del p1
        del p2
        a = input('test:')
```
## 标记清除和分代回收：
在Python程序中，每次你新创建了一个对象，那么就会将这个对象挂到一个叫做零代链表中（当然这个链表是`Python`内部的，`Python`开发者是没法访问到的）。比如现在你在程序中创建四个`Person`对象，分别叫做`p1`、`p2`、`p3`以及`p4`，然后`p1`与`p2`之间互相引用，并且让`p3`和`p4`的引用计数为2，示例代码如下：
```py
    import sys
    
    class Person(object):
        def __init__(self,name):
            self.name = name
            self.next = None
            self.prev = None
    
    p1 = Person('p1')
    p2 = Person('p2')
    p3 = Person('p3')
    p4 = Person('p4')
    
    p1.next = p2
    p2.prev = p1
    
    temp1 = p3
    temp2 = p4
    
    print(sys.getrefcount(p1))
    print(sys.getrefcount(p2))
    print(sys.getrefcount(p3))
    print(sys.getrefcount(p4))
```



