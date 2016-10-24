=================
第四章 映射和元组
=================

---------
构造映射
---------

**不可变映射**

.. code-block:: scala

	val score = Map("Alice"->80)

**可变映射**

.. code-block:: scala

	val score = scala.collection.mutable.Map("Alice"-90)

在scala中，映射是一个对偶，对偶是两个值构成的组，这两个值不一定是同一类型。

使用 ``->`` 来创建对偶。

------------
获取映射的值
------------

在scala中使用 ``()`` 表示法来查找某个键对应的值。

.. code-block:: scala

	val bobscore = scores("Bob")

如果映射并不包含请求中使用的键，则会抛出异常。

要检查映射中是否有某个指定的键，可以使用 ``contains()`` 方法。

除此之外，我们还可以使用 ``getOrElse()`` 方法。

--------------
更新映射中的值
--------------


在可变映射中，可以更新映射值，做法是在=的左侧使用()。

.. code-block:: scala

	scores("Bob") = 10


也可以使用 ``+=`` 来添加多个关系

.. code-block:: scala
	
	scores +=  ("Kate" -> 80)

使用 ``-=`` 移除某个键和对应的值。

---------
迭代映射
---------

使用for循环遍历映射中所有的键：

.. code-block:: scala
	
	for( (k, v) <- map)

-------------
访问键或者值
-------------

.. code-block:: scala
	
	scores.keySet
	scores.values

--------
反转映射
--------

.. code-block:: scala
	
	for( (k, v) <- map) yield (v, k)

-----------
已排序映射
-----------

.. code-block:: scala

	val scores = scala.collections.immutable.SortedMap("A"->1, "B"->2)

如果需要按插入顺序访问所有键的话，使用 ``LinkedHashMap`` 。

-----
元组
-----

元组是不同类型值的集合。

.. code-block:: scala
	
	val t = (1, "a", 3.14)

和数组或字符串中的位置不同，元组从1开始而不是0。

在创建两个元素的元组时，可以使用特殊语法： ``->``:

.. code-block:: scala

	scala> 1 -> 2
	res0: (Int, Int) = (1,2)

通常，使用模式匹配来获取元组的组员。

.. code-block:: scala
	
	val (first , second, third ) = t

如果并不是所有的部件都需要，则可以在不需要的位置使用 ``_`` 。

.. code-block:: scala
	
	val (first, second, _) =  t

----------
拉链操作
----------

使用元组的原因之一是多个值绑定在一起，以便它们能够一起被处理，这通常可以用 ``zip()`` 方法实现。

.. code-block:: scala
	
	val symbols = Array("<","-", ">")
	val counts = Array(2,10,2)
	val paris = symbols.zip(counts)


输出对偶的数组：

.. code-block:: scala

	Array(("<",2),("-",10),(">",2))

使用 ``toMap()`` 可以将对偶的集合转换成映射。

