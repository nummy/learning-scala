=================
第十章 特质
=================

-------
特质
-------

scala中没有多重继承，scala提供特质而非接口，特质可以同时拥有具体方法和抽象方法，而类可以实现多个特质。

.. code-block:: scala

	trait Equal {
		def isEqual(x: Any): Boolean
		def isNotEqual(x: Any): Boolean = !isEqual(x)
	}

在重写特质的抽象方法时不需要使用 ``override`` 关键字。

如果你需要不止一个特质，可以用with关键字来添加额外的特质。

所有Java接口都可以作为scala特质来使用。

------------------
带有具体实现的特质
------------------

在scala中，特质的方法不一定需要是抽象的。

让特质拥有具体行为有一个弊端，当特质改变的时候，所有混入该特质的类都必须重新编译。

------------------
带有特质的对象
------------------

在构造单个对象时，可以给它添加特质。

.. code-block:: scala

	val act = new SavingAccount with ConsoleLogger

----------------------
在特质中重写抽象方法
----------------------

首先定义特质 ``Logger`` ：

.. code-block:: scala
	
	trait Logger {
    	def log(msg:String)
	}

接下来定义特质 ``TimestampLogger`` 并继承重写 ``log`` 方法：

.. code-block:: scala

	trait TimestampLogger extends Logger{
    	abstract override def log(msg:String){
        	super.log(new java.util.Date() + " " + msg)
    	}
	}


--------------
特质中的字段
--------------

特质中的字段可以是具体的，也可以是抽象的。如果给出了字段的初始值，那么该字段就是具体的。如果没有给出初始值，那么就是抽象的。抽象字段在具体的子类中必须被重写。重写的时候不需要使用 ``override`` 关键字。

-------------
特质构造顺序
-------------

和类一样，特质也有构造器，由字段的初始化和其他特质体中的语句构成。

构造器的执行顺序如下：

- 首先调用超类的构造器

- 特质构造器在超类构造器之后，类构造器之前执行

- 特质由左至右被构造

- 每个特质中，父特质首先被构造

- 如果多个特质共有一个父特质，而该父特质已经被构造，则不会再次构造

- 所有特质构造完，子类被构造


-------------------
初始化特质中的字段
-------------------

特质不能有构造参数，每个特质都有一个无参数的构造器。

初始化特质中的字段有两种方法：

- 一是需要提前定义。

.. code-block:: scala
	
	val act = new {

    	val filename = "a.jpg"

	} with SaveAccount with FileLogger


- 二是在FileLogger构造器中使用懒值：

.. code-block:: scala
	
	trait FileLogger extends Logger{
    	
    	val filename:String
    	
    	lazy val out = new PrintStream(filename)
    	
    	def log(msg:String) { out.println(msg)}
	}


------------
扩展类的特质
------------

特质也可以扩展类，这个类会自动成为所有混入该特质的超类。

特质的超类也自动成为我们类的超类。

-----------
自身类型
-----------

当特质以以下代码开始定义时：

.. code-block:: scala
		
		this: 类型 =>

它便只能混入指定类型的子类

.. code-block:: scala
	
	trait LoggedException extends Logged{
    	this:Exception =>
        
        def log(){log(getMessage()}
	}	

注意该特质并不扩展 ``Exception`` 类，而是有一个自身类型 ``Exception`` 。这意味着，它只能被混入 ``Exception`` 子类。


在特质的方法中，我们可以调用该自身类型的任何方法。








