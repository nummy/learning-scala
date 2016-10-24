===========
第九章 继承
===========

----------
扩展类
----------

scala扩展类的方式和Java一样，使用 ``extends`` 关键字：

.. code-block:: scala

	class Employee extends Person{
	    val salary = 0.0
    	...
	}


和Java一样，你可以将类声明为 ``final`` ，这样它就不能被扩展。你可以将单个方法或者字段声明为 ``final`` ， 以确保它们不能够被重写。

----------
重写方法
----------

在scala中重写一个非抽象方法必须使用 ``override`` 修饰符。

.. code-block:: scala

	public class Person{
    	...
    	override def toString = getClass.getName + "[name=" + name + "]"
	}

``override`` 修饰符可以用在多个情况下给出有用的错误提示。包括：

- 当你拼错要重写的方法名

- 当你不小心在新方法中使用错误的参数类型。

- 当你在超类中引入新的方法，而这个新的方法与子类的方法相抵触。

在scala中调用超类的方法和Java一样，使用 ``super`` 关键字。

----------------
类型转换和检查
----------------

要检查某个对象是否属于某个特定的类，可以用 ``isInstanceOf`` 方法。如果测试成功，你就可以使用 ``asInstanceOf`` 方法将引用转换为子类的引用。

.. code-block:: scala

	if (p.isInstanceOf[Employee]){
    	val s = p.asInstanceOf[Employee]
	}

如果 ``p`` 指向的是 ``Employee`` 类及其子类，则 ``p.isInstanceOf[Employee]`` 就会成功；

如果 ``p`` 是 ``null`` ， 则 ``p.isInstanceOf[Employee]`` 将返回 ``false`` ，且 ``p.asInstanceOf[Employee]`` 将返回 ``null`` 。

如果 ``p`` 不是一个 ``Employee`` ，则 ``p.asInstanceOf[Employee]`` 将抛出异常。

如果你想要测试 ``p`` 指向的是一个 ``Employee`` 对象但又不是其子类的话，可以使用：

.. code-block:: scala
	
	if (p.getClass == classOf[Employee])

不过与类型检查和转化相比，模式匹配通常是更好的选择。

.. code-block:: scala
	
	p match{
    	case s:Employee => ...
    	case _ =>...
	}



-------------
受保护字段
-------------

如果字段或方法被声明为 	``protected`` ，则这样的成员可以被任何子类访问，但不能从其他位置看到。与Java不同的是，``protected`` 的成员对于类所属的包而言，是不可见的。

---------------
超类的构造
---------------

辅助构造器永远不能够直接调用超类的构造器，子类的辅助构造器最终都会调用主构造器，只有主构造器可以调用超类的构造器。

scala类可以扩展Java类，这种情况下，它的主构造器必须调用Java超类的某一个构造方法。

-----------
重写字段
-----------

注意以下限制：

- ``def`` 只能重写另一个 ``def``

- ``val`` 只能重写另一个 ``val`` 或不带参数的 ``def``

- ``var`` 只能重写另一个抽象的 ``var``


-----------
匿名子类
-----------


和Java一样，你可以通过包含带有定义或者重写代码块的方式创建一个匿名子类。

.. code-block:: scala

	val align = new Person("Fred"){
    	def greeting = "hello"
	}


--------
抽象类
--------

和Java一样，可以使用 ``abstract`` 关键字定义一个不能被实例化的类。

.. code-block:: scala
	
	abstract class Person(val name:String){
    	def id:Int
	}

但是在scala中，不像java，你不需要对抽象方法使用 ``abstract`` 关键字，你只是省去方法体。

在子类中重写父类的抽象方法时，你不需要使用 ``override`` 关键字。

-----------
抽象字段
-----------

除了抽象方法之外，类还可以拥有抽象字段，抽象字段就是一个没有初始化值的字段。

具体的子类必须提供具体的字段。和方法一样，在子类中重写超类的抽象字段时，不需要 ``override`` 关键字。


-------------
scala继承层级
-------------

所有其他类都是 ``AnyRef`` 的子类， ``AnyRef`` 相当于Java中的 ``Object`` 类。

``AnyVal`` 和 ``AnyRef`` 都扩展自 ``Any`` 类，而 ``Any`` 类是整个继承层级的根节点。

``Null`` 类型的唯一实例就是 ``null`` 值，你可以额将 ``null`` 值赋值给任何引用，但不能赋值给值类型的应用。

``Nothing`` 类型没有实例，它对于泛型结构时常有用。

--------------
对象相等性
--------------

在scala中， ``AnyRef`` 的 ``eq`` 方法检查两个引用是否指向同一个对象。 ``AnyRef`` 的 ``equals`` 方法调用 ``eq`` 。





