执行上下文
=========

``Future`` 和 ``Promise`` 的计算任务放在 ``ExecutionContext`` 中执行。一个 ``ExecutionContext`` 相当于一个执行器，它会将计算任务放在新线程或者线程池中执行，也可以放在当前线程中执行，不过不推荐。

``scala.concurrent`` 提供了一个 ``ExecutionContext`` 的实现，一个全局的静态线程池，它也可以将 ``Executor`` 转换成 ``ExecutionContext`` 。

当然我们也可以通过继承 ``ExecutionContext`` 来自定义执行上下文。

全局执行上下文
=============

``ExecutionContext.global`` 由  ``ForkJoinPool`` 提供，它能满足我们大多数情况下的需求，但是也需要注意一些情况。
