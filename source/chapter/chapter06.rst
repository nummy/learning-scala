=========
第六章 类
=========

----------------
简单类和无参方法
----------------

Scala类最简单的形式和Java相似：

.. code-block:: scala

    class Counter {
        private var value = 0 //必须初始化
        def increment() { value += 1}   //方法默认是公有的
        def current () = value

在scala中，类并不声明为public，scala 源文件可以包含多个类，所有这些类都具有公有可见性。

调用无参方法时，可以使用圆括号，也可以不使用。一般的，对于改值器方法使用(),对于取值器方法则去掉()。

也可以使用不带()的方式声明 ``current`` 来强制调用的时候不使用()。

--------------------------
带有getter和setter的属性
--------------------------

Scala对每一个字段都提供了 ``getter`` 和 ``setter`` 方法。这里定义一个公有字段：

.. code-block:: scala

    class Person {
        var age = 0
    }

Scala生成面向JVM的类，其中有一个私有的 ``age`` 字段以及相应的 ``getter`` 和 ``setter`` 方法。这两个方法是公有的，因为我们没有将 ``age`` 声明为 ``private`` 。对于私有字段而言，``getter`` 和 ``setter`` 也是私有的。

在scala中，``getter`` 和 ``setter`` 分别叫做 ``age`` 和 ``age_=``。
例如：

.. code-block:: scala

    println(fred.age)
    fred.age = 20

在任何时候你都可以自己重新定义 ``getter`` 和 ``setter`` 方法。

如果字段是 ``val`` ，则只有 ``getter`` 方法被生成。如果你不需要任何 ``getter`` 和 ``setter`` ，可以将字段声明为 ``private[this]`` 。

-------------------
只带getter的属性
-------------------

是用 ``val`` 字段，scala会生成一个 ``final`` 字段和一个 ``getter`` 方法。

--------------
对象私有字段
--------------

在scala中，方法可以访问该类的所有对象的私有字段。scala允许我们定义更加严格的访问限制，通过 ``private[this]`` 这个修饰符实现。

.. code-block:: scala

    private[this] val value = 0 //类似某个对象.value这样的访问将不被允许

这样的访问有时候称之为对象私有的。

对于类私有的字段，scala会生成私有的 ``getter``和 ``setter`` 方法，但对于对象私有的字段，scala根本不会生成getter和setter方法。

scala允许将访问权赋予给指定的类，``private[类名]`` 修饰符可以定义仅有指定类的方法可以访问给定的字段。这里的类必须是当前定义的类，或者是包含该类的外部类。

-------------
辅助构造器
-------------

和Java一样，Scala也可以有任意多的构造器，包括主构造器与辅助构造器。

辅助构造器与Java的构造器类似，只有两处不同：

* 辅助构造器的名称为 ``this``

* 每一个辅助构造器都必须以一个对先前已定义的的其他辅助构造器或主构造器的调用开始。

下面的类中包含两个辅助构造器：

.. code-block:: scala

    class Person{
        private var name = ""
        private var age = 0
        
        def this(name:String) {
            this()
            this.name = name
        }
        
        def this(name:String, age:Int){
            this(name)
            this.age = age
        }
    }


一个类如果没有显示定义主构造器则自动拥有一个无参的主构造器。

现在，我们可以使用三种方式构建对象：

.. code-block:: scala

    val p1 = Person
    val p2 = Person("Jim")
    val p3 = Person("Jim",12）

---------
主构造器
---------

在scala中，每个类都有主构造器，主构造器并不以 ``this`` 方法定义，而与类定义交织在一起。

1. 主构造器的参数直接放置在类名之后。

.. code-block:: scala

    class Person(val name:String, val age:Int){
        ...
    }


主构造器的参数被编译成字段，其值被初始化成构造时传入的参数。

2. 主构造器会执行类定义中的所有字段。例如:

.. code-block:: scala

    class Person(val name:String, val age:Int){
        println("Just constructed another person")
        ...
    }


``println`` 语句是主构造器的一部分，每当有对象被构造出来的时候，上述代码就会被执行。当你I需要再构造过程中配置某个字段的时候这个特性特别有用。


如果类名之后没有参数，则该类具备一个无参主构造器，这样的一个构造器仅仅简单的执行类体中的所有语句而已。

构造函数也可以是普通的方法参数，不带 ``val`` 或 ``var``。这样的参数如何处理取决于它在类中如何被使用。

- 如果不带 ``val`` 或者 ``var`` 参数至少被一个方法所使用，它将被升格为字段。例如：

.. code-block:: scala

    class Person(name:String, age:Int){
        def description = name + " is " + age + " years old"
    }

上述代码声明并初始化了不可变字段的 ``name`` 和 ``age``。而这两个字段都是对象私有化的。类似这样的字段等同于 ``private[this] val`` 字段的效果。

- 否则，该参数将不被保存为字段，它仅仅是一个可以被主构造器中的代码访问的普通参数表。

如果想让主构造器变成私有的，可以像这样放置 ``private`` 关键字。

.. code-block:: scala

    class Person private (val id:Int){
        ...
    }

这样一来，用户就必须通过辅助构造器来构造Person对象了。

-------
嵌套类
-------

在scala中，你几乎可以在任何语法结构中内嵌任何语法结构，你可以在函数中定义函数，也可以在类中定义类。

