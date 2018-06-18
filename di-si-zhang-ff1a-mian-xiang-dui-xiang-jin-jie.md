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

1. 普通函数动态创建类

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

2. `type("class name",(继承的父类元祖，可多继承也可为空),{该类的属性字典})`
   ```py
    Person = type("Person",(object,),{"name":"xiaohei","age":19,"sex":"male"})
    print(Person)
    print(Person())
   ```



