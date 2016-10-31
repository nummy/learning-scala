Promise
========

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
