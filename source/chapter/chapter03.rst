===========
第三章 数组
===========

--------
定长数组
--------

如果需要一个长度不变的数组，可以使用scala中的Array。

--------
变长数组
--------

变长数组使用ArrayBuffer，在数组缓存的尾端添加或者移除元素是一个高效的操作。

使用toArray将数组缓存转为数组，使用toBuffer将数组转成缓存。

------------------
遍历数组和数组缓存
------------------

使用for循环遍历数组或者缓存：

.. code-block:: scala

	for( i <- 0 until a.length)
    	println(i)

如果想每两个元素一跳，可以这样遍历：

.. code-block:: scala

	0 until (a.length,2)

如果想从数组的尾端开始，遍历写法为:

.. code-block:: scala

	(0 until a.length).reverse

--------
数组转换
--------

从一个数组出发，以某种方式对它进行转换，这些转换操作不会修改原数组，而是产生一个新的数组。

.. code-block:: scala
	
	val a = Array(1,2,3,4)
	val result = for(elem <- a) yield 2*elem

结果返回一个类型与原始集合相同的新集合。

我们也可以给转换增加过滤条件：

.. code-block:: scala
	
	for(elem <- a if elem % 2 == 0） yield 2 * elem

注意原始集合并没有受到影响。

另一种做法是：

.. code-block:: scala
	
	a.filter(_ % 2 == 0).map(2 * _)

甚至：

.. code-block:: scala

	a.filter{ _ % 2 == 0 } map {2 * _}

--------
常用算法
--------


**求和**

.. code-block:: scala
	
	Array(1,2,3).sum

**最小值和最大值**

.. code-block:: scala
	
	Array(1,2,3).min
	Array(1,2,3).max

**排序**

.. code-block:: scala
	
	Array(5,2,1,4).sorted(_ < _)

**显示数组内容**

.. code-block:: scala
	
	a.mkString
	a.mkString(" and ")
	a.mkString("<",",",">")

--------
多维数组
--------

.. code-block:: scala

	val matrix = Array.ofDim[Double](3,4)

