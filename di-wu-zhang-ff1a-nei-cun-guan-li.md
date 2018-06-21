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



