==========================
第十三章 模式匹配和样式类
==========================

---------
模式匹配
---------

.. code-block::scala
    
    var sign = ....
    val ch：Char = ...
    ch match{
        case '+' => sign = 1
        case '-' => sign = -1
        case _ -> sign = 0
    }

 ``_`` 等效于 ``default`` 。如果没有模式匹配，代码会抛出 ``MatchError`` 。

与 ``switch`` 语句不同，scala模式匹配并不会有意外掉入到下一个分支的问题。

与 ``if`` 类似，``match`` 是表达式，而不是语句。你可以在 ``match`` 表达式中使用任何类型，而不仅仅是数字。

-----
守卫
-----

假如我们需要扩展我们的示例以匹配所有数字，在Java中，只能简单的添加多个 ``case`` 标签，而在scala中，可以使用守卫。

.. code-block:: scala

    ch match {
        case '+' => sign = 1
        case '-' => sign = -1
        case _ if Character.isDigit(ch) => digit = Character.digit(ch, 10)
        case _  => sign = 0
    }


守卫可以是任意 ``Boolean`` 条件。

------------
模式中的变量
------------

如果关键字 ``case`` 后面跟着一个变量名，那么匹配的表达式会被赋值给该变量。

.. code-block:: scala

    str(i) match{
        case '+' => sign = 1
        case '-' => sign = -1
        case ch => digit = Character.digit(ch, 10)
    }

也可以在守卫中使用变量。

注意变量模式可能与常量表达式相冲突。所以变量名必须以小写开头，如果你有一个小写字母开头的常量，则需要将它包在反引号中。

-----------
类型模式
-----------

还可以对表达式的类型进行匹配：

.. code-block:: scala
    
    obj match{
        case x:Int => x
        case s:String => Integer.parseInt(s)
        case _:BigInt => Int.MaxValue
        case _ => 0
    }


在scala中，我们更倾向于使用模式匹配，而不是 ``isInstanceOf`` 
操作符。

当你在匹配类型的时候，必须给出一个变量名，否则，你将会拿对象的本身进行匹配。

匹配发生在运行期，Java虚拟机中泛型的类信息会被擦掉，因此，不能用类型来匹配特定的 ``Map`` 类型。

.. code-block:: scala

    case m:Map[String, Int] => ....   //禁止这样做

不过，可以匹配一个通用的映射：

.. code-block:: scala

    case m:Map[_,_] => ... //Ok

但是对于数组而言，元素的类型信息是完好的，因此可以匹配 ``Array[Int]`` 。

--------------------
匹配数组、列表和元组
--------------------

要匹配数组的内容，可以在模式中使用 ``Array`` 表达式，例如：

.. code-block:: scala
    
    arr match{
        case Array(0) => "0"
        case Array(x,y) => x + "" + y
        case Array(0, _*) => "0 ..."
        case _ => "something else"
    }


第一个模式匹配包含0的数组，第二个模式匹配任何带有两个元素的数组，并将这两个元素分别绑定到变量x和y。第三个表达式匹配任何以0开始的数组。

你也可以用同样的方式匹配列表，使用List表达式，或者使用 ``::`` 操作符。

.. code-block:: scala
    
    lst match{
        case 0::Nil => "0"
        case x::y::Nil => x +"" + y
        case 0::tail => "0..."
        case _ => "something else"
    }

对于元组，可以在模式中使用元组表示法：

.. code-block:: scala
    
    pair match{
        case (0, _) => "0..."
        case (y, _) => y + "0"
        case _ => "neither is 0"
    }


--------
提取器
--------

模式匹配列表、数组或元组背后的机制其实是提取器。

正则表达式是另一个适合使用提取器的场景，如果正则表达式有分组，可以使用提取器来匹配分组。

例如：

.. code-block:: scala
    
    val pattern = "([0-9]+)([a-z]+)".r
    "99 bottles" match{
        case pattern(num, item)=>...
    }


-------------------
变量声明中的模式
-------------------

在变量声明中也可以使用模式。

.. code-block:: scala
    
    val (x,y) = (1,2)

-----------------
for表达式中的模式
-----------------

你可以在 ``for`` 推导式中使用带变量的模式，对每一个遍历的值，这些变量都会被绑定。

在 ``for`` 推导式中，失败的匹配将会被安静的忽略。

---------
样式类
---------

样式类是一种特殊的类，它经过优化以用于模式匹配。

当声明样式类的时候，自动做以下几件事：

- 构造器中的每一个参数都成为 ``val`` 。

- 在伴生对象中提供 ``new`` 方法

- 提供 ``unapply`` 方法

------------------
copy方法和带名参数
------------------

样式类的copy方法创建一个与现有对象值相同的对象。

----------------------
case语句中的中置表示法
----------------------

如果 ``unsupply`` 方法产生一个对偶，则你可以在 ``case`` 语句中使用中置表示法，尤其是对于有两个参数的样式类，可以使用中置表示法来表示。
