================
第八章 包和引入
================

---
包
---

scala的包目的和Java中的包一样：管理大型程序中的名称。

要增加条目到包中，可以将其包含在包语句中，例如：

.. code-block:: scala

    package com{
        package horstman {
            package patient{
                class Employee{
                ...
                }
            }
        }
    }

这样一来，类名 ``Employee`` 就可以在任意位置以 ``com.horstman.patient.Employee`` 访问了。

与对象和类的定义不同，同一个包可以定义在多个文件中。

源文件的目录和包之间并没有强制的关联关系，也就是说可以在同一个文件中定义多个包。

----------
作用域规则
----------

在scala中，包的作用域比Java更加前后一致，scala的包和其他作用域一样支持嵌套。可以访问上层作用域中的名称。

在Java中包名是绝对的，从包层级的最顶端开始，但是在scala中，包名是相对的，就像内部类的名称一样。

------------
串联式包语句
------------

包语句可以包含一个串，或者说路径区段：

.. code-block:: scala

    package com.hosrtman.patient{
        package people{
            class Person{
                ...
            }
        }
    }

这条语句限制了可见的成员。

--------------
文件顶部标识法
--------------

除了使用嵌套标记法之外，还可以在文件顶部使用 ``package`` 语句，不带花括号。

.. code-block:: scala

    package com.hosrtman.people.patient
    package people

------
包对象
------

包可以包含类，对象和特质，但是不能包含函数和变量的定义。

每个包可以有一个包对象，你需要在父包中定义它，且名称和子包一样。
例如：

.. code-block:: scala

    package com.horstman.patient
    
    package object people{
        val defaultName = "John"
    }
    
    package people{
        class Person{
            val name = defaultName  //从包对象中拿到常量
            ...
        }
    }


--------
包可见性
--------

在Java中，没有被声明为 ``public`` 、 ``private`` 或 ``protected`` 的类成员在包含该类的包中可见。在scala中，可以通过修饰符达到同样的目的。

-----
引入
-----

引入语句可以让你使用更短的名字而不是原来较长的名字。
写法如下：

.. code-block:: scala

    import java.awt.Color

引入包中全部成员：

.. code-block:: scala

    import java.awt._

----------------------
任何地方都可以声明引入
----------------------


在scala中， ``import`` 语句可以出现在任意地方，并不仅限于文件顶部， ``import`` 语句的效果一直延伸到包含该语句的块末尾。

-------------
重命名和隐藏
-------------

如果你想引入包中的几个成员，可以像这样使用选取器：

.. code-block:: scala
    
    import java.awt.{Color, Font}


选取器还允许你重命名选到的成员：

.. code-block:: scala
    
    import java.util.{HashMap => JavaHashMap}

选取器 ``HashMap => _`` 将隐藏某个成员而不是重命名它。

----------
隐式引入
----------

每个scala程序都隐式的以如下代码开始：

.. code-block:: scala
    
    import java.lang._
    import scala._
    import Predef._


由于scala包默认导入，对于那些以scala开头的包，你完全不需要写全这个前缀。

