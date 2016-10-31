阻塞和异常
=========

阻塞
----

Future通常都是异步执行的，而且也不会阻塞底层的线程任务。但是在某些情况下需要阻塞，下面我们讨论两种阻塞线程的方法：

**Future中阻塞**

在全局的执行上下文中，可以通过 ``blocking`` 结构来通知执行上下文是一个阻塞调用。

.. code-block:: scala

  implicit val ec = ExecutionContext.fromExecutor(
                    Executors.newFixedThreadPool(4))
  Future {
   blocking { blockingStuff() }
  }

阻塞的代码可能抛出异常，这种情况下，异常会被传递给调用者。

**Future外阻塞**

前面也提到过，阻塞future并不被鼓励，因为这样容易导致死锁。 回调函数以及组合器是更加推荐的方式，但是阻塞在某些情况下也是必须的。

在前面的货币交易示例中，有一个地方需要用到阻塞，因为它需要等所有的future计算结果完成。

.. code-block:: scala
  
  import scala.concurrent._
  import scala.concurrent.duration._
  def main(args: Array[String]) {
   val rateQuote = Future {
      connection.getCurrentValue(USD)
   }
  
    val purchase = rateQuote map { quote =>
      if (isProfitable(quote)) connection.buy(amount, quote)
      else throw new Exception("not profitable")
    }
  
   Await.result(purchase, 0 nanos)
  }
 
使用 ``Await.results`` 等待计算完成，如果成功则返回成功的值，如果失败，则抛出 ``NoSuchElementException`` 。

还可以使用 ``Await.ready`` ， 该方法会等待计算完成，但是并不获取计算结果，也就是说计算失败的时候不会抛出异常。

The Future trait实现了 ``Awaitable trait`` 的 ``ready()`` 和 ``result()`` 方法。这些方法不能被客户端直接调用，它们只能通过执行环境上下文来进行调用。

异常
----

当异步计算抛出未处理的异常时，相应的 ``futures`` 就会失败。  失败的 ``future`` 包含了一个 ``Throwable`` 对象。

下面的异常处理是不一样的：

- scala.runtime.NonLocalReturnControl[_]

- ExecutionException
