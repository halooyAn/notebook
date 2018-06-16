# 闭包和装饰器

# 1.闭包

## 什么是闭包：

如果在一个函数中，定义了另外一个函数，并且那个函数使用了外面函数的变量，并且外面那个函数返回了里面这个函数的引用，那么称为里面的这个函数为闭包。例如：

```py
    def greet(name):
        def say_hello():
            print('hello my name is %s' % name)
        return say_hello
            
        
```



