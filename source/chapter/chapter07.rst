===========
第七章 对象
===========

--------
单例对象
--------

scala中没有静态方法或静态字段，不过可以使用object这个语法结构来达到同样的目的。对象定义了某个类的单个实例，包含了我们想要的特性。

例如：

.. code-block:: scala
    
    object Accounts {
        private var lastNumber = 0
        def newUniqueNumber() = {lastNumber += 1; lastNumber }
    }

对象的构造器在第一次使用时被调用，如果一个对象从未被使用，那么其构造器也不会被执行。

对象本质上可以拥有类的所有特性，它甚至可以扩展其他类和特质，只有一个例外，不能提供构造参数。

对于任何你在Java中会使用单例对象的地方，在Scala中都可以用对象实现：

- 作为存放工具函数和常量的地方

- 高效的共享单个不可变实例

- 需要用单个实例来协调某个服务时。

--------
伴生对象
--------


在Java中，通常会用到既有实例方法又有静态方法的类，在类中，可以通过类和与类同名的伴生对象来实现。例如：

.. code-block:: scala
    
    class Accounts {
        val id = Accounts.newUniqueNumber()
        private var balance = 0.0
    
        def despoit(amount:Double){balance += amount}
            ...
    }

    object Accounts {
        private var lastNumber = 0
        
        def newUniqueNumber() = {
            lastNumber += 1; lastNumber
        }
    }

类和它的伴生对象可以互相访问私有特性，它们必须存在于同一个源文件中。

类的伴生对象可以被访问，但是并不在作用域中。

------------------
扩展类或特质的对象
------------------

一个对象可以扩展类以及一个或者多个特质，其结果是一个扩展了指定类以及特质的类的对象，同时拥有在对象定义中给出的所有特性。

一个有用的场景是给出可被共享的缺省对象。

------------
apply方法
------------

我们通常会定义和使用对象的方法，当遇到如下形式的表达式时，apply方法就会被调用：

.. code-block:: scala
    
    Object(arg1, ..., argN)

通常这样一个 ``apply`` 方法返回的是伴生类的对象。

---------------
应用程序对象
---------------

每个scala程序都必须从一个对象的 ``main`` 方法开始，这个方法的类型为 ``Array[String]=>Unit``:

.. code-block:: scala
    
    object Hello {
    
        def main(args:Array[String]){
            println("hello,world")
        }
    }

除了每次都提供自己的main方法之外，我们还可以扩展App特质，然后将程序代码放到构造器方法体中：

.. code-block:: scala
    
    object Hello extends APP {
        
        println("hello,world")
    
    }

如果需要命令行参数，可以通过 ``args`` 属性获取。

-----
枚举
-----

和Java不同，scala并没有枚举类型，不过标准类库提供了一个 ``Enumeration`` 助手类，可以用于产生枚举。

定义一个扩展 ``Enumeration`` 类的对象并以Value方法调用初始化枚举中的所有可选值。

.. code-block:: scala

    object TrafficLightColor extends Enumeration {
    
        val Red, Yellow, Green = Value
    
    }

这里定义了三个字段：Red, Yeelow, Green。然后调用Value方法进行初始化：

.. code-block:: scala
    
    val Red = Value
    val Yellow 　= Value
    val Green = Green

每次调用 ``Value`` 方法都返回内部类的新实例，该内部类也叫 ``Value`` 。
或者，也可以向 ``Value`` 方法传递ID、名称，或两个参数都传。

.. code-block:: scala

    val Red = Value(0, "Stop")
    val Yellow = Value(10)
    val Green = Value("Go")

如果不指定，则ID将在前一个枚举值的基础上加一， 从零开始，缺省名称为字段名。

定义完成之后，就可以使用 ``TrafficLightColor.Red、TrafficLightCOlor.Yellow``  等来引用枚举值了。

枚举值的ID可以通过id方法返回，名称通过toString方法返回。


