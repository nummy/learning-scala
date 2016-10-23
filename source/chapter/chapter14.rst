==========================
第十四章 文件和正则表达式
==========================

--------
读取行
--------

要读取文件中的所有行，可以调用 ``scala.io.Source`` 对象的 ``getLines`` 方法。

.. code-block:: scala
	
	import scala.io.Source  
	val source = Source.fromFile("a.txt", "UTF-8")
	val lineIterator = source.getLines

结果是一个迭代器。可以用来逐条处理这些行。或者也可以对迭代器应用 ``toArray`` 和 ``toBuffer``方法。

有时候，你只想将整个文件读取成一个字符串，这更加简单：

.. code-block:: scala

	val contents = source.mkString

在调用完 ``Source`` 对象之后，记得 ``close`` 。

--------
读取字符
--------

如果需要读取单个字符，可以直接把 ``Source`` 对象当作迭代器，因为 ``Source`` 类扩展自 ``Iterator[Char]`` :

.. code-block:: scala
	
	for(c <- source) //处理c

-------------------
从URL或者其他源读取
-------------------

``Source`` 对象有读取非文件源的方法：

.. code-block:: scala

	val source1 = Source.fromURL("http://www.baidu.com")
	val source2 = Source.fromString("hello,world")
	val source3 = Source.stdin


--------------
读取二进制文件
--------------

Scala没有提供读取二进制的方法，需要使用Java类库。

.. code-block:: scala
	
	val file = new File(filename)
	val in = new FileInputStream(file)
	var bytes = new Array[Byte](file.length.toInt)
	in.read(bytes)
	in.close()

------------
写入文本文件
------------

一样没有提供内建方法，需要使用Java类库。

.. code-block:: scala
	
	import java.io.PrintWriter
	val out = new PrintWriter("numbers.txt")
	for(i <- 1 to 10) out.println(i)
	out.close()

------
序列化
------

首先声明一个可序列化的类：

.. code-block:: scala

	@SerialVersionUID(42L) classs Person extends Serializable


``Serializable`` 特质定义在scala包中，因此不需要显示导入。

如果你能接受缺省的ID，也可以省略掉 ``@SerialVersionUID`` 注解。

Scala集合类都是可序列化的，因此可以把他们用作可序列化类的成员。

--------
进程控制
--------

scala提供了 ``scala.sys.process`` 包用于与 ``shell`` 交互。

.. code-block:: scala
	
	import sys.process._
	"ls -al "!

----------
正则表达式
----------

要构造一个 ``Regex`` 对象，用 ``String`` 类的 ``r`` 方法即可。

.. code-block:: scala
	
	val pattern = "[0-9]+".r

如果正则表达式包含了反斜杠或者引号的话，最好使用原始字符串的形式 ``"""...""""`` 。

``findAllIn`` 方法返回遍历所有匹配项的迭代器。

使用 ``toArray`` 可以将迭代器转换成数组。

要想找到字符串中的首个匹配项，可以使用 ``findFirstIn`` ，得到的结果是一个 ``Option[String]`` 。

使用 ``replaceFirstIn`` 或者 ``replaceAllIn`` 替换首个或者全部匹配字符串。

-------------
正则表达式组
-------------

分组可以让我们方便的获取正则表达式的子表达式。

.. code-block:: scala
	
	val numItemPattern = "([0-9]+) ([a-z]+)".r
	val numItemPattern(num, item) = "99 bottles"
