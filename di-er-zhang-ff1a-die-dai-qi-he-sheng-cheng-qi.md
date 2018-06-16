# 生成器和迭代器

# 1.迭代器

## 什么是迭代器：

迭代器可以让我们访问集合的时候变得非常方便。之前我们通过`for...in...`的方式访问一个集合的时候，就是使用迭代器完成的。如果没有迭代器，那么我们只能通过`while`循环，每次循环的时候通过下标访问了。

## 可迭代对象

以直接使用for循环遍历的对象，成为可迭代的对象，常见的可迭代的对象有：`list`、`tuple`、`dict`、`set`、`string`以及生成器。  
更加专业的判断一个对象是否是可迭代的对象是：这个对象有一个`__iter__`方法，并且这个方法会返回一个迭代器对象，这种对象就叫做可迭代的对象。

## 判断一个对象是否是可迭代：

可以使用`isinstance()`判断一个对象是否是`Iterable`的对象：

```py
    from collections import Iterable
    # 列表是一个可迭代的对象
    ret = isinstance([1,2,3],Iterable)
    print(ret)
    
    # 字符串是一个可迭代的对象
    ret = isinstance("abc",Iterable)
    print(ret)
    
    # 整形不是一个可迭代的对象
    ret = isinstance(123,Iterable)
    print(ret)
```

## 迭代器：

1. 在`Python2`中，实现了`next`方法和`__iter__`方法，并且在这个方法中返回了值的对象，叫做迭代器对象。
2. 在`Python3`中，实现了`__next__`方法和方法`__iter__`方法，并且这个方法返回了值的对象，叫做迭代器对象。
3. 如果迭代器没有返回值了，那么应该在`next`或者是`__next__`方法中抛出一个`StopIteration`异常。

## 使用`iter()`方法获取可迭代对象的迭代器：

有时候我们拥有了一个可迭代的对象，我们想要获取这个迭代器，那么可以通过`iter(x)`方法获取这个可迭代对象的迭代器。

## 自定义迭代器：

```py
    class MyRangeIterator(object):
            """
            迭代器
            """
            def __init__(self,start,end):
                self.index = start
                self.end = end
    
            def __next__(self):
                if self.index < self.end:
                    index = self.index
                    self.index += 1
                    return index
                else:
                    raise StopIteration()
    
    
        class MyRange(object):
            """
            可迭代对象
            """
            def __init__(self,start,end):
                self.start = start
                self.end = end
    
            def __iter__(self):
                return MyRangeIterator(self.start,self.end)
    
    
        # 初始化一个可迭代的对象
        for x in MyRange(1,10):
            print(x)
    
        # 使用itre函数将可迭代的对象转换为迭代器
        range_iterator = iter(MyRange(1,10))
    
        # 不能遍历迭代器，只能通过next来迭代
        for x in range_iterator:
            print(x)
```

# 2.生成器

## 为什么需要生成器：

假如现在有一个需求，我要打印从1-1亿的整形。如果我们采用普通的方式，直接调用`range`函数，那么程序肯定会崩溃。因为`range(1,100000000)`函数直接产生一个从1-1亿的列表，这个列表中的所有数据都是存放在内存中的，会导致内存爆满。这时候我们可以采用生成器来解决这个问题，生成器不会一次性把所有数据都加载到内存中，而是在循环的时候临时生成的，循环一次生成一个，所以在程序运行期间永远都只会生成一个数据，从而大大节省内存。

## 解决打印1-1亿的问题： {#解决打印11亿的问题：}

用`range`函数配合圆括号可以产生一个生成器：

```py
    # 普通的列表
    num_list = [x forx in range(1,100000000)]
    print(type(num_list))
    
    # 生成式
    num_gen = (x for x in range(1,100000000))
    print(type(num_gen))
```

### `next`函数和`__next__`方法：

`next`函数可以迭代生成器的返回值

### 自己写生成器：

生成器可以通过函数产生。如果在一个函数中出现了`yield`表达式，那么这个函数将不再是一个普通的函数，而是一个生成器函数。`yield`一次返回一个结果，并且会冻结当前函数的状态。以下是一个非常简单的生成器：

```py
    def my_gen():
        yield 1    
        yield 2
        yield 3
    ```
    
那么想要获取里面的值，可以通过以下方式获取：
    
    ```py
        ret = my_gen()
        print(next(ret))
        print(next(ret))
        print(next(ret))
```

### `send`方法：

`send`方法和`next`方法类似，可以用来触发生成器的下一个`yield`，但是`send`不仅可以触发下一个`yield`，还可以发送数据过去，作为`yield`表达式的值。

```py
    def my_gen(start):
        while start < 10 :
            temp = yield start
            print(temp)
            start += 1
    
    
    ret = my_gen(1)    
    # next(ret)返回1
    # 以下结果会打印1
    print(next(ret))    
    # send("Hello World")返回2
    # 但是会将"Hello World"赋值给yield start
    # 因此temp = "Hello World"
    # 那么会打印"Hello World"
    # 并且打印2
    print(ret.send('Hello World'))
```

### 生成器中的`return`语句会触发`StopIterator`异常：

```py
    def my_gen(start):
        while start < 3:
            temp = yield start            
            # print(temp)
            start += 1
            # 触发StopIterator异常，会导致不能继续遍历
            return "nihao"
    
    ret = my_gen(1)   
    for x in ret:
        print(x)
```

### 生成器的使用案例：

1. 用`yield`实现斐波拉契数列： 斐波拉契数列的算法：除第一个和第二个数以外，任意一个数都可由前两个数相加得到：0,1,1,2,3,5,8,13,21,34,55... 

```py
    def fibo(count):
        index = 0 
        a,b =0,1
        while index <= count:
            yield a
            c = b 
            b = a+b
            a = c
            index += 1
            

    for x in fibo(6):
        print(x)   
``` 




