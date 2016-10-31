===========
第一章 基础
===========

--------
常用类型
--------

scala中常用类型如下：

* Byte

* Char

* Short

* Int

* Long

* Float

* Double

* Booean


这些类型都是类，所以在scala中不需要包装类型，在基本类型和包装类型之间的转换工作是scala编译器的事。

除此之外，还有 ``Null`` 类型，指代 ``null`` 或者空引用。

``Nothing`` 是所有其它类型的子类型，包括空值。

``Any`` 是其它类型的父类型， ``AnyRef`` 是其它引用类型的父类型。

在scala中，我们使用方法而不是强制类型转换，来做数值类型之间的转换。例如：

.. code-block:: scala

	99.44.toInt         // 99
	99.toChar           // 'c'    

和Java一样， ``toString`` 将任意对象转换成字符串，要将包含了数字的字符串转换成数字，使用 ``toInt`` 或者 ``toDouble`` 。

-----------------
算术和操作符重载
-----------------


和Java相比，Scala并没有提供 ``++`` 和 ``--`` 操作符，我们需要使用 ``+=1`` 或者 ``-=1`` ；

对于常规的 ``BigInt`` 和 ``BigDecimal`` 对象，我们可以使用常规的方法使用那些数学操作符：

.. code-block:: scala

	val x:BigInt = 12121212
	x*x*x

在java中，我们需要使用 ``x.multiply(x).multiply(x)`` 。

---------------
调用函数和方法
---------------

相比Java，在scala中使用数学函数更简单，我们不需要从某个类的调用它的静态方法。

.. code-block:: scala

	import scala.math._
	sqrt(2)
	pow(2,4)

在使用以scala开头的包时，我们可以省略scala的前缀。例如 ``import math._`` 等价于 ``import scala.math._`` 。

Scala中没有静态方法，但是提供了单例对象。

不带参数的方法通常不使用圆括号，一般来讲，没有参数并不改变当前对象的方法都不带圆括号。

scala中允许使用数字 ``*`` 字符串，从而实现复制字符串的功能。

-----------
apply方法
-----------

在scala中，我们通常使用类似函数调用的语法。例如：

.. code-block:: scala

	"Hello"(4) // o

你可以将这种用法当作 ``()`` 操作符的重载形式，它背后原理是实现一个名为 ``apply`` 的方法。所以 ``"Hello"(4)`` 相当于以下代码：

.. code-block:: scala

	"Hello".apply(4)


