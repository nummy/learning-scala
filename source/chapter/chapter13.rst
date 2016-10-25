=============
第十三章 集合
=============

---------------
主要的集合特质
---------------

所有的集合都继承了 ``Iterable`` 特质。

每个scala集合特质H或类都有一个带有 ``apply`` 方法的伴生对象，这个 ``apply`` 方法可以用来构建该集合中的实例。

----------------
可变和不可变集合
----------------

Scala支持可变集合和不可变集合。Scala优先使用不可变集合。

------
序列
------

``Vector`` 是 ``ArrayBuffer`` 的不可变版本，一个带下标的序列，支持快速的随机访问。

``Range`` 表示一个整数序列， 可以使用 ``to`` 和 ``until`` 构造 ``Range`` 对象。

----
列表
----

列表与数组类似，所有成员的类型都相同，但是列表是不可变的，因此不能直接赋值。

.. code-block:: scala

	// List of Strings
	val fruit: List[String] = List("apples", "oranges", "pears")
	
	// List of Integers
	val nums: List[Int] = List(1, 2, 3, 4)
	
	// Empty List.
	val empty: List[Nothing] = List()
	
	// Two dimensional list
	val dim: List[List[Int]] =
		List(
			List(1, 0, 0),
			list(0, 1, 0),
			List(0, 0, 1)
	)	

在Scala中，列表要么是 ``Nil`` ，要么是一个 ``head`` 元素加 ``tail`` ，而 ``tail`` 又是一个列表。

使用 ``::`` 操作符从给定的头到尾创建一个新列表。注意 ``::`` 是右结合的。

.. code-block:: scala

	// List of Strings
	val fruit = "apples" :: ("oranges" :: ("pears" :: Nil))

	// List of Integers
	val nums = 1 :: (2 :: (3 :: (4 :: Nil)))
	
	// Empty List.
	val empty = Nil

--------
可变列表
--------

可变的 ``LinkedList`` 和不可变的 ``List`` 相似，只不过你可以通过 ``elem`` 引用赋值来修改其头部，对 ``next`` 引用赋值来修改其尾部。

---
集
---

集是不重复元素的集合，集不保存元素插入的顺序。

``LinkedHashSet`` 可以记住元素被插入的顺序，它会维护一个链表来达到目的。
如果你想要按照已排序的顺序来访问集合中的元素，可以使用 ``SortedSet`` 。

位集是集的另一种实现，以一个字位序列的方式存放非负整数。

``contains`` 方法检查某个集是否包含给定的值，``subsetOf`` 方法检查某个集中所有元素是否被另一个集合包含。

``union`` 、``intersect`` 和 ``diff`` 方法执行通常的集操作，也可以将它们写成 ``|、&、&~`` 。 ``union`` 还可以写成 ``++`` ， ``diff`` 还可以写成 ``--`` 。

--------------------------
用于添加或者去除元素的集合
--------------------------

``+`` 用于将元素添加至无先后次序的集合，而 ``+:`` 和 ``:+`` 用于将元素添加至有先后次序的集合的开头或者末尾。

这些操作符都会返回新的集合，不会修改原有的集合。

------------------
将函数映射到集合
------------------

``map`` 方法可以将某个函数应用到集合中的每个元素并产生其求结果的集合。

如果函数产生一个集合而不是单个值的话，你可能想将所有的值串接在一起，这时可以使用 ``flatMap`` 。

.. code-block:: scala
	
	def ulcase(s:String) = Vector(s.toUpperCase(), s.toLowerCase())  
	#names.map(ulcase)得到  

	List(Vector("PETER","peter"), Vector("PAUL","paul"), Vector("MARY","mary"))  

	# names.flatMap(ulcase)得到  
	List("PETER","peter","PAUL","paul","MARY","mary")  


``collect`` 方法用于 ``partial function`` ，那些并没有对所有可能的输入值进行定义的函数， 产出被定义的所有参数的函数值得集合  

.. code-block:: scala
	
	"-3+4".collect(case '+' -> 1; case '-' -> -1) // vector(-1,1)  

----------------
化简、折叠和扫描
----------------

``reduceLeft`` 用于从左至右对元素执行操作。

``reduceRight`` 用于从右至左对元素进行操作。

-------------
拉链操作
-------------

``zip`` 方法用于将两个集合中的元素组合成一个对偶的列表。

-------
迭代器
-------

通过迭代器，可以使用 ``next`` 和 ``hasNext`` 方法遍历集合元素。

----
流
----

迭代器很脆弱，每次调用 ``next`` 都会改变迭代器的指向， 流提供了一个不可变得替代品，流的尾部是一个被懒计算的不可变列表，也就是说只有需要的时候才会被计算。



