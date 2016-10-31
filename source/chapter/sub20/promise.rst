Promise
=======

前面我们介绍了通过 ``future`` 方法来创建 ``Future`` 对象， 实际上，``futures`` 还可以通过 ``Promise`` 来创建。

如果说 ``futures`` 是为了一个还没有存在的结果，而当成一种只读占位符的对象类型去创建，那么 ``promise`` 就被认为是一个可写的，
可以实现一个 ``future`` 的单一赋值容器。这就是说， ``promise`` 通过这种 ```success`` 方法可以成功去实现一个带有值的 ``future`` 。
相反的，因为一个失败的 ``promise`` 通过 ``failure`` 方法就会实现一个带有异常的 ``future`` 。

一个 ``Promise`` 对象 ``p`` 通过调用 ``p.future`` 方法返回 ``future`` 对象。

考虑下面的生产者消费者模式：

.. code-block:: scala
  
  import scala.concurrent.{ Future, Promise }
  import scala.concurrent.ExecutionContext.Implicits.global
  val p = Promise[T]()
  val f = p.future
  val producer = Future {
   val r = produceSomething()
    p success r
   continueDoingSomethingUnrelated()
  }
  val consumer = Future {
    startDoingSomething()
    f onSuccess {
      case r => doSomethingWithResult()
    }
  }

上面我们创建了一个 ``Promise`` 对象， 然后使用 ``future`` 方法获取 ``Future`` 对象，然后执行两个异步计算，第一个用于生成，第二个用于消费。

正如前面提到的， ``promises`` 具有单赋值语义。因此，它们仅能被实现一次。在一个已经计算完成的 ``promise`` 或者 ``failed`` 的 ``promise`` 
上调用 ``success`` 方法将会抛出一个 ``IllegalStateException`` 异常。

下面这个例子处理了Promise失败的情况：

.. code-block:: scala
  
  val p = promise[T]
  val f = p.future
  val producer = Future {
    val r = someComputation
    if (isInvalid(r))
      p failure (new IllegalStateException)
    else {
      val q = doSomeMoreComputation(r)
      p success q
    }
  }
  
Promises也能通过一个 ``complete`` 方法来实现，返回结果为 ``Try[T]`` 类型，这个值要么是一个类型为 ``Failure[Throwable]`` 的失败的结果值，要么是一个类型为 ``Success[T]`` 的成功的结果值。

类似 ``success`` 方法，在一个已经完成(completed)的 ``promise`` 对象上调用 ``failure`` 方法和 ``complete`` 方法同样会抛出一个 ``IllegalStateException`` 异常。

``completeWith`` 方法将用另外一个 ``future`` 完成 ``promise`` 计算。当该 ``future`` 结束的时候，该 ``promise`` 对象得到那个 ``future``对象同样的值，如下的程序将打印1：

.. code-block:: scala

  val f = Future { 1 }
  val p = promise[Int]
  p completeWith f
  p.future onSuccess {
    case x => println(x)
  }

工具库
======

为了简化并发开发过程中的时间处理，``scala.concurrent`` 库提供了 ``Duration`` 类。

``Duration`` 代表一段时间范围，它既可以代表有限时间，也可以代表无限时间。有限时间通过 ``FiniteDuration`` 表示，无限时间只有两种情况 ``Duration.Inf`` 和 ``Duration.MinusInf`` 。

Duration库还提供了其他一些子类用于隐式参数转换。

Duration类包括以下方法：

- 时间单位转换函数(toNanos, toMicros, toSeconds, toMinutes, toHours, toDays, toUnits等)

- 时间大小比较函数(<, <=, >, >=)

- 算术运算(+,-,*,/, unary_-)

- 获取两个Duration中最大值max或者最小值min

- 判断Duration对象是否无限

Duration可以通过以下方法实例化：

- 通过隐式参数转换，从Int或Long类型转换为Duration

- 通过一个Long类型的参数，以及一个 ``java.util.concurrent.TimeUnit`` 类型的参数构建， 例如：``val d = Duration(100, MILLISECONDS)``。

- 通过字符串构建，例如 ``val d = Duration("1.2 µs")``

Duration还提供了unapply方法，所以它支持模式匹配：

.. code-block:: scala
  
  import scala.concurrent.duration._
  import java.util.concurrent.TimeUnit._
 
  // instantiation
  val d1 = Duration(100, MILLISECONDS) // from Long and TimeUnit
  val d2 = Duration(100, "millis") // from Long and String
  val d3 = 100 millis // implicitly from Long, Int or Double
  val d4 = Duration("1.2 µs") // from String
  // pattern matching
  val Duration(length, unit) = 5 millis


