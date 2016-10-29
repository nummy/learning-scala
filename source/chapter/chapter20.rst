=========================
第二十章  Future 和Promise
=========================

``Future`` 提供了一套高效便捷的非阻塞并行操作管理方案。其基本思想很简单，所谓 ``Future`` ，指的是一类占位符对象，用于指代某些尚未完成的计算的结果。一般来说，由 ``Future`` 指代的计算都是并行执行的，计算完毕后可另行获取相关计算结果。
以这种方式组织并行任务，便可以写出高效、异步、非阻塞的并行代码。


默认情况下，``Future`` 和 ``Promise`` 并不采用一般的阻塞操作，而是依赖回调进行非阻塞操作。为了在语法和概念层面更加简明扼要地使用这些回调，Scala还提供了 ``flatMap`` 、 ``foreach`` 和 ``filter`` 等函数，使得我们能够以非阻塞的方式对 ``Future`` 进行组合。当然， ``Future``仍然支持阻塞操作——必要时，可以阻塞等待 ``Future``（不过并不鼓励这样做）。

一个典型的 ``Future`` 示例如下：

.. code-block:: scala
    
    val inverseFuture: Future[Matrix] = Future {
        fatMatrix.inverse() // non-blocking long lasting computation
    }(executionContext)

或者使用更加通用的写法：

.. code-block::scala
    
    implicit val ec: ExecutionContext = ...
        val inverseFuture : Future[Matrix] = Future {
        fatMatrix.inverse()
    } // ec is implicitly passed

上面的代码将 ``fatMatrix.inverse()`` 放在 ``ExecutionContext`` 中执行，然后将计算结果放在 ``inverseFuture`` 中。

.. toctree::
    :maxdepth: 1
    
    ./sub20/*
