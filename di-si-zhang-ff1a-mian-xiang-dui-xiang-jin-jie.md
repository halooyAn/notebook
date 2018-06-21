# 动态添加属性： {#动态添加属性：}

动态添加属性，就是这个属性不是在类定义的时候添加的，而是在程序运行过程中添加的，动态添加属性有两种方法，第一个是直接通过`对象名.属性名`，第二个是通过`setattr`添加：

1. 第一种：使用`对象名.属性名`添加，示例如下：

```py
    p.age = 18
```

1. 第二种：使用`setattr`函数添加，示例如下：

```py
    if not hasattr(p,'age'):
         setattr(p,'age',18)
```

`hasattr`是用来判断一个对象是否有某个属性，而`setattr`是用来给某个对象添加属性，并且指定这个属性的值。`getattr`是用来访问这个对象的某个属性，并且如果这个属性不存在的时候，可以指定一个默认值。

# 动态添加方法: {#动态添加方法}

### 动态添加实例方法： {#动态添加实例方法：}

添加类方法，是把这个方法添加给类。因此添加类方法的时候不是给对象添加，而是给类添加。并且添加类方法的时候不需要使用

`types.MethodType`，直接将这个函数赋值给类就可以了，但是需要使用`classmethod`装饰器将这个方法设置为一个类方法。示例代码如下：

```py
    import types

    class Person(object):
        country = 'china'
        def __init__(self,name):
            self.name = name

    @classmethod
    def run(cls):
        print('%s在奔跑' % cls.country)

    Person.run = run
    Person.run()
```

### 动态添加静态方法： {#动态添加静态方法：}

添加静态方法，是把这个方法添加给类。因此也是直接给类添加的，并且使用`staticmethod`这个装饰器。示例方法如下：

```py
    import types

    class Person(object):
        country = 'china'
        def __init__(self,name):
            self.name = name

    @staticmethod
    def run():
        print('在奔跑')

    Person.run = run
    Person.run()
```

### 动态删除属性和方法： {#动态删除属性和方法：}

1. `del 对象.属性名`
2. `delattr(对象,"属性名")`

# `__slots__`魔术变量： {#slots魔术变量：}

有时候我们想指定某个类的对象，只能使用我指定的这些属性，不能随便添加其他的属性，那么这时候就可以使用`__slots__`魔术变量。这个魔术变量是一个列表或者一个元组，里面存放属性的名字，以后在对象外面，就只能添加这个魔术变量中指定的属性，不能添加其他属性，示例代码如下：

```py
    class Person(object):
        __slots__ = ('name','age')
        def __init__(self,name):
            self.name = name

    p = Person('zhiliao')
    setattr(p,'height',180)
    # 报错
```

# 动态创建类：

## 1. 普通函数动态创建类

```py
    def class_create(name):
        def create():
            if name == "zhiliao":
                class Person(object):
                    pass
                return Person
            else:
                class Car(object):
                    pass
                return Car
        return create

    a = class_create("zhiliao")
    print(a)
    print(a())
```

## 2. `type("class name",(父类元祖，可多继承也可为空),{该类的属性字典})`

```py
    Person = type("Person",(object,),{"name":"xiaohei","age":19,"sex":"male"})
    print(Person)
    print(Person()
```

## 3. 元类：

### 动态地创建类：

因为类也是对象，你可以在运行时动态的创建它们，就像其他任何对象一样。首先，你可以在函数中创建类，使用`class`关键字即可。
```py
    >>> def choose_class(name):
    …       if name == 'foo':
    …           class Foo(object):
    …               pass
    …           return Foo     # 返回的是类，不是类的实例
    …       else:
    …           class Bar(object):
    …               pass
    …           return Bar
    …
    >>> MyClass = choose_class('foo')
    >>> print MyClass              # 函数返回的是类，不是类的实例
    <class '__main__'.Foo>
    >>> print MyClass()            # 你可以通过这个类创建类实例，也就是对象
    <__main__.Foo object at 0x89c6d4c>
```
但这还不够动态，因为你仍然需要自己编写整个类的代码。由于类也是对象，所以它们必须是通过什么东西来生成的才对。当你使用`class`关键字时，`Python`解释器自动创建这个对象。但就和`Python`中的大多数事情一样，`Python`仍然提供给你手动处理的方法。还记得内建函数`type`吗？这个古老但强大的函数能够让你知道一个对象的类型是什么，就像这样：
```py
    >>> print type(1)
    <type 'int'>
    >>> print type("1")
    <type 'str'>
    >>> print type(ObjectCreator)
    <type 'type'>
    >>> print type(ObjectCreator())
    <class '__main__.ObjectCreator'>
```
这里，`type`有一种完全不同的能力，它也能动态的创建类。`type`可以接受一个类的描述作为参数，然后返回一个类。（我知道，根据传入参数的不同，同一个函数拥有两种完全不同的用法是一件很傻的事情，但这在`Python中`是为了保持向后兼容性）
`type`可以像这样工作：
    type(类名, 父类的元组（针对继承的情况，可以为空），包含属性的字典（名称和值）)
比如下面的代码：
```py
    >>> class MyShinyClass(object):
    …       pass
```
可以手动像这样创建：
```py
    >>> MyShinyClass = type('MyShinyClass', (), {})  # 返回一个类对象
    >>> print MyShinyClass
    <class '__main__.MyShinyClass'>
    >>> print MyShinyClass()  #  创建一个该类的实例
    <__main__.MyShinyClass object at 0x8997cec>
```
你会发现我们使用“MyShinyClass”作为类名，并且也可以把它当做一个变量来作为类的引用。类和变量是不同的，这里没有任何理由把事情弄的复杂。

`type`接受一个字典来为类定义属性，因此：
```py
    >>> class Foo(object):
    …       bar = True
    
```
可以翻译为：
```py
    >>> Foo = type('Foo', (), {'bar':True})
```
并且可以将Foo当成一个普通的类一样使用：
```py
    >>> print Foo
    <class '__main__.Foo'>
    >>> print Foo.bar
    True
    >>> f = Foo()
    >>> print f
    <__main__.Foo object at 0x8a9b84c>
    >>> print f.bar
    True
    
```
当然，你可以向这个类继承，所以，如下的代码：

```py
    >>> class FooChild(Foo):
    …       pass
```
就可以写成：
```py
    >>> FooChild = type('FooChild', (Foo,),{})
    >>> print FooChild
    <class '__main__.FooChild'>
    >>> print FooChild.bar   # bar属性是由Foo继承而来
    True
```
最终你会希望为你的类增加方法。只需要定义一个有着恰当签名的函数并将其作为属性赋值就可以了。
## 到底什么是元类（终于到主题了）
元类就是用来创建类的“东西”。你创建类就是为了创建类的实例对象，不是吗？但是我们已经学习到了`Python`中的类也是对象。好吧，元类就是用来创建这些类（对象）的，元类就是类的类，你可以这样理解为：

你已经看到了`type`可以让你像这样做：
```py
    MyClass = type('MyClass', (), {})
```
这是因为函数`type`实际上是一个元类。`type`就是`Python`在背后用来创建所有类的元类。现在你想知道那为什么`type`会全部采用小写形式而不是`Type`呢？好吧，我猜这是为了和`str`保持一致性，`str`是用来创建字符串对象的类，而`int`是用来创建整数对象的类。`type`就是创建类对象的类。你可以通过检查`__class__`属性来看到这一点。`Python`中所有的东西，注意，我是指所有的东西——都是对象。这包括整数、字符串、函数以及类。它们全部都是对象，而且它们都是从一个类创建而来。
```py
    >>> age = 35
    >>> age.__class__
    <type 'int'>
    >>> name = 'bob'
    >>> name.__class__
    <type 'str'>
    >>> def foo(): pass
    >>>foo.__class__
    <type 'function'>
    >>> class Bar(object): pass
    >>> b = Bar()
    >>> b.__class__
    <class '__main__.Bar'>
```
现在，对于任何一个`__class__`的`__class__`属性又是什么呢？
```py
    >>> a.__class__.__class__
    <type 'type'>
    >>> age.__class__.__class__
    <type 'type'>
    >>> foo.__class__.__class__
    <type 'type'>
    >>> b.__class__.__class__
    <type 'type'>
```
因此，元类就是创建类这种对象的东西。如果你喜欢的话，可以把元类称为“类工厂”（不要和工厂类搞混了:D） `type`就是`Python`的内建元类，当然了，你也可以创建自己的元类。
## __metaclass__属性（Python2）：
你可以在写一个类的时候为其添加`__metaclass__`属性。
```py
    class Foo(object):
        __metaclass__ = something…
    […]
```
如果你这么做了，`Python`就会用元类来创建类`Foo`。小心点，这里面有些技巧。你首先写下`class Foo(object)`，但是类对象`Foo`还没有在内存中创建。`Python`会在类的定义中寻找`metaclass`属性，如果找到了，`Python`就会用它来创建类`Foo`，如果没有找到，就会用内建的`type`来创建这个类。把下面这段话反复读几次。当你写如下代码时：
```py
    class Foo(Bar):
        pass
```
Python做了如下的操作：

Foo中有`__metaclass__`这个属性吗？如果是，`Python`会在内存中通过`__metaclass__`创建一个名字为`Foo`的类对象（我说的是类对象，请紧跟我的思路）。如果`Python`没有找到`__metaclass__`，它会继续在`Bar`（父类）中寻找`__metaclass__`属性，并尝试做和前面同样的操作。如果`Python`在任何父类中都找不到`__metaclass__`，它就会在模块层次中去寻找`__metaclass__`，并尝试做同样的操作。如果还是找不到`__metaclass__`,`Python`就会用内置的`type`来创建这个类对象。

现在的问题就是，你可以在`__metaclass__`中放置些什么代码呢？答案就是：可以创建一个类的东西。那么什么可以用来创建一个类呢？`type`，或者任何使用到`type`或者子类化`type`的东东都可以。
## 自定义元类：
元类的主要目的就是为了当创建类时能够自动地改变类。通常，你会为`API`做这样的事情，你希望可以创建符合当前上下文的类。假想一个很傻的例子，你决定在你的模块里所有的类的属性都应该是大写形式。有好几种方法可以办到，但其中一种就是通过在模块级别设定`__metaclass__`。采用这种方法，这个模块中的所有类都会通过这个元类来创建，我们只需要告诉元类把所有的属性都改成大写形式就万事大吉了。

幸运的是，`__metaclass__`实际上可以被任意调用，以下代码自定义元类：
* 用函数的形式：
```py
    # 元类会自动将你通常传给‘type’的参数作为自己的参数传入
      def upper_attr(future_class_name, future_class_parents, future_class_attr):
          '''返回一个类对象，将属性都转为大写形式'''
          #  选择所有不以'__'开头的属性
          attrs = ((name, value) for name, value in future_class_attr.items() if not name.startswith('__'))
          # 将它们转为大写形式
          uppercase_attr = dict((name.upper(), value) for name, value in attrs)
    
          # 通过'type'来做类对象的创建
          return type(future_class_name, future_class_parents, uppercase_attr)
    
      class Foo(object):
          __metaclass__ = upper_attr
          bar = 'bip'
```
* 类的形式:
```py
    class UpperAttrMetaclass(type):
          def __new__(cls, name, bases, dct):
              attrs = ((name, value) for name, value in dct.items() if not name.startswith('__'))
              uppercase_attr = dict((name.upper(), value) for name, value in attrs)
              return super(UpperAttrMetaclass, cls).__new__(cls, name, bases, uppercase_attr)
```
## `python3`中创建类时指定`metaclass`时不用`__metaclass__`属性，
直接在定义创建类时指定`metaclass`
```py
    class Person(object,metaclass=UpperAttrsMetaClass))
        pass
```