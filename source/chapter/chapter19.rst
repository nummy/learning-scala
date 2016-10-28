=========================
第十九章 隐式转换与隐式参数
=========================

-------
隐式转换
-------

+++++++++++
隐式转换函数
+++++++++++

以 ``implicit`` 关键字声明的带有单个参数的函数，这样的函数会自动被应用，将值从一种类型装换成另一种类型。

例如，将整数n转换成分数n/1:

.. code-block:: scala
  
  implicit def int2Fraction(n:Int) = Fraction(n)
  
这样就可以对如下表达式进行求值了。

.. code-block:: scala
  
  var result = 3 * Fraction(4,5)

由于我们并不会显示调用这个函数，所以可以给函数取任意名字。

我们可以使用隐式转换丰富现有类库的功能。

++++++
隐式类
++++++

隐式类支持在主构造函数中进行隐式转换。隐式类用 ``implicit`` 注明，它必须位于 ``class`` 、 ``object`` 或者特质中，不能为顶级类。

它的语法格式如下所示：

.. code-block:: scala
  
  object <object name> {
     implicit class <class name>(<Variable>: Data type)
     {
         def <method>(): Unit=
     }
  }
  

下面在Run.scala中定义一个隐式类 ``IntTimes`` ，类中包含方法 ``times()``。

.. code-block:: scala

  object Run {

      implicit class IntTimes(x: Int) {
        
          def times [A](f: =>A): Unit={
              
             def loop(current: Int): Unit=if(current > 0){
                  f
                  loop(current - 1)
             }
             loop(x)
          }
     }
  }

然后在Demo.run中输入以下代码：

.. code-block:: scala

  import Run._

  object Demo {
     def main(args: Array[String]) {
          4 times println("hello")
      }
  }

当执行 ``4 times printl(""hello")`` 时会自动执行隐式转换，将 ``4`` 转换为 ``IntTimes`` 的实例，然后调用 ``times`` 方法。

隐式类有以下限制条件：

- 只能在别的trait/类/对象内部定义。

- 构造函数只能携带一个非隐式参数。 

- 在同一作用域内，不能有任何方法、成员或对象与隐式类同名。这意味着隐式类不能是case class。


+++++++++++
引入隐式转换
+++++++++++

有三种方法引入隐式转换：

- 将隐式函数放置于源或者目标类型的伴生对象中。

- 将隐式函数置于当前作用域中，使用 ``implicit`` 关键字声明。

- 从其他包中引入隐式函数。

在REPL中使用 ``:implicits`` 查看所有除 ``Predef`` 外被引用的隐式成员。

++++++++++++
隐式转换规则
++++++++++++

以下情况下会进行隐式转换：

- 当表达式的类型与预期的类型不同时;

- 当对象访问一个不存在的成员时；

- 当对象调用某个方法，而该方法的参数声明与传入参数不匹配时。

以下情况不会进行隐式转换：

- 如果代码能够在不使用隐式转换的前提下通过编译，则不会使用隐式转换；

- 编译器不会尝试同时执行多个转换；

- 存在二义性的转换是个错误。

--------
隐式参数
--------

函数或者方法可以带有一个标记为 ``implicit`` 的参数列表，这种情况下，编译器将会查找缺省值，提供给该函数或者方法。

例如：

.. code-block:: scala

  case class Delimiter(left:String, right:String)

  def quote(what:String)(implicit delims:Delimiter) ={
      delims.left + what + delims.right
  }

我们可以使用一个显示的Delimiter对象来调用quote方法，就像这样：

.. code-block:: scala

  quote("hello")(Delimiter("left", "right")

也可以省略隐式参数列表。

.. code-block:: scala
  
  quote("hello")

在这种情况下，编译器会查找一个类型为Delimiter的隐式值，这个隐式值必须被声明为implicit的值，编译器会尝试在以下两个地方查找这样的一个对象。

- 在当前作用域所有可以用单个标识符指代的满足类型要求的val和def

- 与所要求类型相关联的类型的伴生对象。相关联的类型包括所要求类型本身，以及它的类型参数。






