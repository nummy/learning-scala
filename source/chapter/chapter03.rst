=============
第三章 字符串
=============

在Scala中，字符串也是不可变对象。

----------
创建字符串
----------

.. code-block:: scala
	
	var greeting = "Hello, world"

	// or

	var greeting:String = "Hello, world"


如果需要使用可变字符串，可以使用 ``StringBuilder`` 类。

-----------
字符串长度
-----------

使用 ``length()`` 获取字符串长度。

.. code-block:: scala
	
	object Demo {
    	def main(args: Array[String]) {
        	var palindrome = "Dot saw I was Tod";
        	var len = palindrome.length();
        	println( "String Length is : " + len );
    	}
	}


-------------
拼接字符串
-------------

.. code-block:: scala
	
	string1.concat(string2);

	// or

	string1 + string2


-------------
格式化字符串
-------------

可以使用 ``printf`` 或者 ``format`` 来格式化字符串。 ``String`` 类有一个等价类的方法， ``format()`` ，它返回一个 ``String`` 对象，而不是一个 ``PrintStream`` 对象。

.. code-block:: scala
	
	scala > "%.2f".format(1212)
	1212.00
	
----------
字符串插值
----------

字符串插值允许用户将变量的引用直接嵌入到处理字符串字面量中。例如：

.. code-block:: scala
	
	val name = "James"
	println(s"Hello, $name")    //Hello, James


scala提供了三种字符串插值方法： ``s`` 、 ``f ``  和 ``raw`` 

+++++++
插值器s
+++++++

在任何字符串之前添加 ``s`` ，则该字符串允许直接包含变量。

字符串插值器也可以包含任意表达式：


.. code-block:: scala

	println(s" 1+1 = ${1+1}")


++++++++++++++
格式化插值器f
++++++++++++++

在任何字符串字面量前追加 ``f`` ，就可以创造一个简单的格式化字符串，类似于其他语言中的 ``printf`` 。
当使用插值器f时，所有变量的引用应该跟随 ``printf`` 风格的格式字符串，如同 ``%d`` 。
例如：

.. code-block:: scala
	
	val height = 1.9d
	val name = "James"
	println(f"$name%s is $height%2.2f meters tail")  // James is 1.90 meters tall

插值器 ``f`` 是类型安全的。如果你试图传递一个只能工作于整数的格式化字符串，却又传了一个浮点数，编译器会发出一个错误。

+++++++++++
插值器raw
+++++++++++

插值器 ``raw`` 和插值器 ``s`` 相似，不同的是它不对字符串字面量执行转义。

.. code-block:: scala
	
	scala> s"a\nb"
	res0: String =
	a
	b

插值器 ``s`` 将字符 ``\n`` 替换成了回车符。而插值器 ``raw`` 不会这么做。

.. code-block:: scala
	
	scala> raw"a\nb"
	res1: String = a\nb

当你想要避免有表达式（例如 ``\n`` 变成回车）时，插值器 ``raw`` 是很有用的。 


