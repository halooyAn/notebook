# 闭包和装饰器

# 1.闭包

## 什么是闭包：

在一个外函数中定义了一个内函数，内函数里运用了外函数的临时变量，并且外函数的返回值是内函数的引用。这样就构成了一个闭包。

一般情况下，在我们认知当中，如果一个函数结束，函数的内部所有东西都会释放掉，还给内存，局部变量都会消失。但是闭包是一种特殊情况，如果外函数在结束的时候发现有自己的临时变量将来会在内部函数中用到，就把这个临时变量绑定给了内部函数，然后自己再结束。

```py
    #闭包函数的实例
    # outer是外部函数 a和b都是外函数的临时变量
    def outer( a ):
        b = 10
        # inner是内函数
        def inner():
            #在内函数中 用到了外函数的临时变量
            print(a+b)
        # 外函数的返回值是内函数的引用
        return inner

    if __name__ == '__main__':
        # 在这里我们调用外函数传入参数5
        #此时外函数两个临时变量 a是5 b是10 ，并创建了内函数，然后把内函数的引用返回存给了demo
        # 外函数结束的时候发现内部函数将会用到自己的临时变量，这两个临时变量就不会释放，会绑定给这个内部函数
        demo = outer(5)
        # 我们调用内部函数，看一看内部函数是不是能使用外部函数的临时变量
        # demo存了外函数的返回值，也就是inner函数的引用，这里相当于执行inner函数
        demo() # 15

        demo2 = outer(7)
        demo2()#17
```

从上面例子是我写的一个最简单的很典型的闭包。我估计如果是初学的小伙伴，可能很多名词都不明白是什么意思，没关系，我把这些名词按照自己的理解去解释一下~

1. 外函数返回了内函数的引用：

   引用是什么？在`python`中一切都是对象，包括整型数据`1`，函数，其实是对象。

   当我们进行`a=1`的时候，实际上在内存当中有一个地方存了值`1`，然后用`a`这个变量名存了`1`所在内存位置的引用。引用就好像`c`语言里的指针，大家可以把引用理解成地址。`a`只不过是一个变量名字，`a`里面存的是`1`这个数值所在的地址，就是`a`里面存了数值`1`的引用。

   相同的道理，当我们在`python`中定义一个函数`def demo():` 的时候，内存当中会开辟一些空间，存下这个函数的代码、内部的局部变量等等。这个`demo`只不过是一个变量名字，它里面存了这个函数所在位置的引用而已。我们还可以进行`x = demo`， `y = demo`， 这样的操作就相当于，把`demo`里存的东西赋值给`x`和`y`，这样`x` 和`y`都指向了`demo`函数所在的引用，在这之后我们可以用`x()`或者`y()`来调用我们自己创建的`demo()`，调用的实际上根本就是一个函数，`x`、`y`和`demo`三个变量名存了同一个函数的引用。

   有了上面的解释，我们可以继续说，返回内函数的引用是怎么回事了。对于闭包，在外函数`outer`中 最后`return inner`，我们在调用外函数`demo = outer()` 的时候，`outer`返回了`inner`，`inner`是一个函数的引用，这个引用被存入了`demo`中。所以接下来我们再进行`demo()`的时候，相当于运行了`inner`函数。

   同时我们发现，一个函数，如果函数名后紧跟一对括号，相当于现在我就要调用这个函数，如果不跟括号，相当于只是一个函数的名字，里面存了函数所在位置的引用。

2. 外函数把临时变量绑定给内函数：

   按照我们正常的认知，一个函数结束的时候，会把自己的临时变量都释放还给内存，之后变量都不存在了。一般情况下，确实是这样的。但是闭包是一个特别的情况。外部函数发现，自己的临时变量会在将来的内部函数中用到，自己在结束的时候，返回内函数的同时，会把外函数的临时变量送给内函数绑定在一起。所以外函数已经结束了，调用内函数的时候仍然能够使用外函数的临时变量。

   在我编写的实例中，我两次调用外部函数`outer`,分别传入的值是5和7。内部函数只定义了一次，我们发现调用的时候，内部函数是能识别外函数的临时变量是不一样的。`python`中一切都是对象，虽然函数我们只定义了一次，但是外函数在运行的时候，实际上是按照里面代码执行的，外函数里创建了一个函数，我们每次调用外函数，它都创建一个内函数，虽然代码一样，但是却创建了不同的对象，并且把每次传入的临时变量数值绑定给内函数，再把内函数引用返回。虽然内函数代码是一样的，但其实，我们每次调用外函数，都返回不同的实例对象的引用，他们的功能是一样的，但是它们实际上不是同一个函数对象。

## `nonlocal` 关键字

在闭包内函数中，我们可以随意使用外函数绑定来的临时变量，但是如果我们想修改外函数临时变量数值的时候发现出问题了！咋回事捏？？！！（哇哇大哭）

在基本的`python`语法当中，一个函数可以随意读取全局数据，但是要修改全局数据的时候有两种方法:

1. `global` 声明全局变量 
2. 全局变量是可变类型数据的时候可以修改

在闭包内函数也是类似的情况。在内函数中想修改闭包变量（外函数绑定给内函数的局部变量）时：

1. 在`python3`中，可以用`nonlocal`关键字声明 一个变量， 表示这个变量不是局部变量空间的变量，需要向上一层变量空间找这个变量。
2. 在`python2`中，没有`nonlocal`这个关键字，我们可以把闭包变量改成可变类型数据进行修改，比如列表。

   ```py
    #修改闭包变量的实例
    # outer是外部函数 a和b都是外函数的临时变量
    def outer( a ):
        b = 10  # a和b都是闭包变量
        c = [a] #这里对应修改闭包变量的方法2
        # inner是内函数
        def inner():
            #内函数中想修改闭包变量
            # 方法1 nonlocal关键字声明
            nonlocal  b
            b+=1
            # 方法二，把闭包变量修改成可变数据类型 比如列表
            c[0] += 1
            print(c[0])
            print(b)
        # 外函数的返回值是内函数的引用
        return inner

    if __name__ == '__main__':

        demo = outer(5)
        demo() # 6  11
   ```

还有一点需要注意：使用闭包的过程中，一旦外函数被调用一次返回了内函数的引用，虽然每次调用内函数，是开启一个函数执行过后消亡，但是闭包变量实际上只有一份，每次开启内函数都在使用同一份闭包变量.

```py
    #coding:utf8
    def outer(x):
        def inner(y):
            nonlocal x
            x+=y
            return x
        return inner


    a = outer(10)
    print(a(1)) //11
    print(a(3)) //14
```

## 闭包的作用：

1. 装饰器！！！装饰器是做什么的？？其中一个应用就是，我们工作中写了一个登录功能，我们想统计这个功能执行花了多长时间，我们可以用装饰器装饰这个登录模块，装饰器帮我们完成登录函数执行之前和之后取时间。
2. 面向对象！！！经历了上面的分析，我们发现外函数的临时变量送给了内函数。大家回想一下类对象的情况，对象有好多类似的属性和方法，所以我们创建类，用类创建出来的对象都具有相同的属性方法。闭包也是实现面向对象的方法之一。在python当中虽然我们不这样用，在其他编程语言入比如avaScript中，经常用闭包来实现面向对象编程。
3. 实现单例模式！！! 其实这也是装饰器的应用。单例模式毕竟比较高大，，需要有一定项目经验才能理解单例模式到底是干啥用的。

# 2. 装饰器

## 什么是装饰器：

装饰器利用了函数也可以作为参数传递和闭包的特性，可以让我们的函数在执行之前或者执行之后方便的添加一些代码。这样就可以做很多事情了，比如`@classmethod`装饰器可以将一个普通的方法设置为类方法，`@staticmethod`装饰器可以将一个普通的方法设置为静态方法等。所以明白了装饰器的原理以后，我们就可以自定义装饰器，从而实现我们自己的需求。

## 理解：

拿网站开发的例子来说。网站开发中，经常会碰到一些页面是需要登录后才能访问的。那么如果每次都在访问的视图函数中判断，很麻烦，而且代码很难维护，示例如下：

```py
    user = {
        "is_login": False
    }

    def edit_user():
        if user['is_login'] == True:
            print('用户名修改成功')
        else:
            print('跳转到登录页面')

    def add_article():
        if user['is_login'] == True:
            print('添加文章成功')
        else:
            print('跳转到登录页面')

    edit_user()
    add_article()
```

以上现在是只有两个函数，如果以后网站越来越大，需要做判断的地方越来越大，那么这种判断将显得非常低效并且难以维护，因此这时候我们可以采用装饰器来解决：

```py
    def edit_user():
        print('用户名修改成功')
    
    def add_article():
        print('添加文章成功')
    
    def login_required(func):
    
        def wrapper():
            if user['is_login'] == True:
                func()
            else:
                print('跳转到登录页面')
    
        return wrapper
    
    login_required(edit_user)
    login_required(add_article)
```
这样我们把这个判断用户是否登录的逻辑就已经单独抽出放到`login_required`这个装饰器中了，以后如果某个函数想要做登录限制，那么就先传给`login_required`这个装饰器就可以了。
但是以上方式写法很别扭，每次调用一个函数的时候要记得先传给`login_required`，容易忘记每次都要写，因此我们采用另外一种写法：

```py
    def login_required(func):
    
        def wrapper():
            if user['is_login'] == True:
                func()
            else:
                print('跳转到登录页面')
    
        return wrapper
    
    @login_required
    def edit_user():
        print('用户名修改成功')
    
    @login_required
    def add_article():
        print('添加文章成功')
    
    edit_user()
    add_article()
```
以上这种写法是装饰器中正确的写法，在函数定义开头的地方，通过@装饰器名就可以了，这样在调用`edit_user`和`add_article`的时候，就不需要手动的传给`login_required`了。

## 被装饰的函数带有参数：

被装饰的函数有参数是非常普遍的一种情况，这时候我们只需要在里面的函数中传递参数就可以了：

```py
    def login_required(func):
    
        def wrapper(username):
            if user['is_login'] == True:
                func(username)
            else:
                print('跳转到登录页面')
    
        return wrapper
    
    @login_required
    def edit_user(username):
        print('用户名修改成功：%s'%username)
    
    edit_user("zhiliao")
```
也有可能被装饰的函数中，参数是不固定的，因此这时候写一个固定的参数，不能成为一个普遍的装饰器，这时候可以采用`*args`和`**kwargs`组合起来，包含所有的参数：

```py
    def login_required(func):
    
        def wrapper(*args,**kwargs):
            if user['is_login'] == True:
                func(*args,**kwargs)
            else:
                print('跳转到登录页面')
    
        return wrapper
    
    @login_required
    def edit_user(username):
        print('用户名修改成功：%s'%username)
    
    edit_user()
```
## 带参数的装饰器：
装饰器也可以传递参数。只不过如果给装饰器传递参数了，那么就要在这个装饰器中写两个方法了，示例代码如下：

```py
    def login_required(site='front'):
        def outter_wrapper(func):
            def inner_wrappre(*args,**kwargs):
                if site == 'front':
                    if user['is_login'] == True:
                        print('进入到前台了')
                        func(*args,**kwargs)
                    else:
                        print('跳转到前台的首页')
                else:
                    if user['is_login'] == True:
                        print('进入到后台了')
                        func(*args,**kwargs)
                    else:
                        print('跳转到后台的首页')
            return inner_wrappre
        return outter_wrapper
    
    
    @login_required()
    def edit_user():
        print('用户名修改成功')
        
    @login_required()
    def add_article():
        print('添加文章成功')

    edit_user()
```
## `wraps`装饰器

采用之前的装饰器，会让我们的函数失去一些属性，比如`__name__`，这样在一些代码中会产生错误，比如`Flask`开发中。如果我们想要用装饰器，并且仍想保留函数的一些属性，比如`__name__`，那么可以使用`wraps`装饰器，以下是没有使用`wraps`装饰器的代码：

```py
    def login_required(func):
    
        def wrapper(*args,**kwargs):
            if user['is_login'] == True:
                func(*args,**kwargs)
            else:
                print('跳转到登录页面')
    
        return wrapper
    
    @login_required
    def edit_user(username):
        print('用户名修改成功：%s'%username)
    
    
    edit_user()
    print(edit_user.__name__)
    # 打印wrapper
```
以下再使用wraps装饰器，来优化代码：

```py
    from functools import wraps
    
    def login_required(func):
        @wraps
        def wrapper(*args,**kwargs):
            if user['is_login'] == True:
                func(*args,**kwargs)
            else:
                print('跳转到登录页面')
    
        return wrapper
    
    @login_required
    def edit_user(username):
        print('用户名修改成功：%s'%username)
    
    
    edit_user()
    print(edit_user.__name__)
    # 打印edit_user
```
## 作业：实现一个可以计算一个函数用时多久的装饰器
```py
    from functools import wraps
    import datetime
    
    USERNAME = "xiaohei"
    USER_PASSWORD = "123456"
    
    
    def count_time(func):
    
        @wraps(func)
        def wrapper(*args,**kwargs):
            start_time = datetime.datetime.now()
            func(*args,**kwargs)
            end_time = datetime.datetime.now()
            print('您进行登录操作共用了%d秒' % (end_time- start_time).seconds)
    
        return wrapper
    
    
    @count_time
    def login():
        username = input("请输入您的账户名:")
        user_password = input("请输入您的密码:")
        if username == USERNAME and user_password == USER_PASSWORD:
            print("登录成功")
        else:
            print("账户名或密码错误，请重新尝试！")
    
    login()
```
