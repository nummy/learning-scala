=======================
第五章 映射、选项以及元组
=======================

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


**获取映射的值**


在scala中使用 ``()`` 表示法来查找某个键对应的值。

.. code-block:: scala

	val bobscore = scores("Bob")

如果映射并不包含请求中使用的键，则会抛出异常。

要检查映射中是否有某个指定的键，可以使用 ``contains()`` 方法。

除此之外，我们还可以使用 ``getOrElse()`` 方法。


**更新映射中的值**



在可变映射中，可以更新映射值，做法是在=的左侧使用()。

.. code-block:: scala

	scores("Bob") = 10


也可以使用 ``+=`` 来添加多个关系

.. code-block:: scala
	
	scores +=  ("Kate" -> 80)

使用 ``-=`` 移除某个键和对应的值。


**迭代映射**

使用for循环遍历映射中所有的键：

.. code-block:: scala
	
	for( (k, v) <- map)

**访问键或者值**


.. code-block:: scala
	
	scores.keySet
	scores.values

**反转映射**

.. code-block:: scala
	
	for( (k, v) <- map) yield (v, k)

**已排序映射**


.. code-block:: scala

	val scores = scala.collections.immutable.SortedMap("A"->1, "B"->2)

如果需要按插入顺序访问所有键的话，使用 ``LinkedHashMap`` 。

----
选项
----

``Option`` 是一个表示有可能包含值的容器。

``Option`` 基本的接口是这样的：

.. code-block:: scala

	trait Option[T] {
	  def isDefined: Boolean
 	  def get: T
 	  def getOrElse(t: T): T
	}

``Option`` 本身是泛型的，并且有两个子类： ``Some[T]``  或  ``None``

``Map.get`` 使用 ``Option`` 作为其返回值，表示这个方法也许不会返回你请求的值。

模式匹配能自然地配合 ``Option`` 使用。

.. code-block:: scala

	val result = res1 match {
	  case Some(n) => n * 2
	  case None => 0
	}

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

------------
常用集合操作
------------

+++
Map
+++

``map`` 对列表中的每个元素应用一个函数，返回应用后的元素所组成的列表。

.. code-block:: scala

	scala> numbers.map((i: Int) => i * 2)
	res0: List[Int] = List(2, 4, 6, 8)

或者传入一个函数：

.. code-block:: scala

	scala> def timesTwo(i: Int): Int = i * 2
	timesTwo: (i: Int)Int

	scala> numbers.map(timesTwo _)
	res0: List[Int] = List(2, 4, 6, 8)

+++++++
foreach
+++++++

``foreach`` 很像 ``map`` ，但没有返回值。 ``foreach`` 仅用于有副作用的函数。

.. code-block:: scala

	scala> numbers.foreach((i: Int) => i * 2)

该函数返回值为Unit类型。

++++++
filter
++++++

``filter`` 移除任何对传入函数计算结果为 ``false`` 的元素。返回一个布尔值的函数通常被称为谓词函数[或判定函数]。

.. code-block:: scala

	scala> numbers.filter((i: Int) => i % 2 == 0)
	res0: List[Int] = List(2, 4)
	
	scala> def isEven(i: Int): Boolean = i % 2 == 0
	isEven: (i: Int)Boolean

	scala> numbers.filter(isEven _)
	res2: List[Int] = List(2, 4)

+++
zip
+++

``zip`` 将两个列表的内容聚合到一个对偶列表中。

.. code-block:: scala

	scala> List(1, 2, 3).zip(List("a", "b", "c"))
	res0: List[(Int, String)] = List((1,a), (2,b), (3,c))

+++++++++
partition
+++++++++

``partition`` 将使用给定的谓词函数分割列表。

.. code-block:: scala

	scala> val numbers = List(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
	scala> numbers.partition(_ % 2 == 0)
	res0: (List[Int], List[Int]) = (List(2, 4, 6, 8, 10),List(1, 3, 5, 7, 9))

++++ 
find
++++

``find`` 返回集合中第一个匹配谓词函数的元素。

.. code-block:: scala

	scala> numbers.find((i: Int) => i > 5)
	res0: Option[Int] = Some(6)

++++++++++++++++
drop & dropWhile
++++++++++++++++

``drop`` 将删除前i个元素

.. code-block:: scala

	scala> numbers.drop(5)
	res0: List[Int] = List(6, 7, 8, 9, 10)

``dropWhile`` 将删除元素直到找到第一个匹配谓词函数的元素。例如，如果我们在numbers列表上使用 ``dropWhile`` 奇数的函数, 1将被丢弃（但3不会被丢弃，因为他被2“保护”了）。

.. code-block:: scala

	scala> numbers.dropWhile(_ % 2 != 0)
	res0: List[Int] = List(2, 3, 4, 5, 6, 7, 8, 9, 10)

++++++++++++++++++++
foldLeft & foldRight
++++++++++++++++++++

.. code-block:: scala

	scala> numbers.foldLeft(0)((m: Int, n: Int) => m + n)
	res0: Int = 55
	
0为初始值（记住numbers是 ``List[Int]`` 类型），m作为一个累加器。

``foldRight`` 和 ``foldLeft`` 一样，只是运行过程相反。

+++++++
flatten
+++++++

``flatten`` 将嵌套结构扁平化为一个层次的集合。

.. code-block:: scala

	scala> List(List(1, 2), List(3, 4)).flatten
	res0: List[Int] = List(1, 2, 3, 4)
	
+++++++
flatMap
+++++++

``flatMap`` 是一种常用的组合子，结合映射 ``[mapping]`` 和扁平化 ``[flattening]`` 。 ``flatMap`` 需要一个处理嵌套列表的函数，然后将结果串连起来。

.. code-block:: scala

	scala> val nestedNumbers = List(List(1, 2), List(3, 4))
	nestedNumbers: List[List[Int]] = List(List(1, 2), List(3, 4))

	scala> nestedNumbers.flatMap(x => x.map(_ * 2))
	res0: List[Int] = List(2, 4, 6, 8)
	
可以把它看做是“先映射后扁平化”的快捷操作。
