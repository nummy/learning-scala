Future
=======

所谓 ``Future`` ，是一种用于指代某个尚未就绪的值的对象。而这个值，往往是某个计算过程的结果：

- 若该计算过程尚未完成，我们就说该 ``Future``未就位；

- 若该计算过程正常结束，或中途抛出异常，我们就说该 ``Future`` 已就位。

``Future`` 的就位分为两种情况：

- 当 ``Future`` 带着某个值就位时，我们就说该 ``Future`` 携带计算结果成功就位。
- 当 ``Future`` 因对应计算过程抛出异常而就绪，我们就说这个 ``Future`` 因该异常而失败。

``Future`` 的一个重要属性在于它只能被赋值一次。一旦给定了某个值或某个异常，``Future`` 对象就变成了不可变对象——无法再被改写。

创建 ``Future`` 对象最简单的方法是调用 ``Future.apply`` 方法，该方法启用异步(asynchronous)计算并返回保存有计算结果的 ``Futrue`` ，
一旦计算完成，``Future`` 中的结果就变的可用。

注意 ``Future[T]`` 是表示 ``Future`` 对象的类型，而 ``Future.apply`` 是方法，该方法创建和调度一个异步计算，并返回随着计算结果而完成的 ``Future`` 对象。

这最好通过一个例子予以说明。

假设我们使用某些流行的社交网络的假定API获取某个用户的朋友列表，我们将打开一个新对话(session)，然后发送一个请求来获取某个特定用户的好友列表。

.. code-block:: scala
  
  import scala.concurrent._
  import ExecutionContext.Implicits.global
  val session = socialNetwork.createSessionFor("user", credentials)
  val f: Future[List[Friend]] = Future {
    session.getFriends()
  }

以上，首先导入 ``scala.concurrent``  包使得 ``Future`` 类型和 ``future`` 构造函数可见。我们将等会解释第二个导入。

然后我们初始化一个 ``session`` 变量来用作向服务器发送请求，用一个假想的 ``createSessionFor`` 方法来返回一个 ``List[Friend]`` 。为了获得朋友列表，我们必须通过网络发送一个请求，这个请求可能耗时很长。这能从调用`getFriends`方法得到解释。为了更好的利用CPU，响应到达前不应该阻塞(block)程序的其他部分执行，于是在计算中使用异步。`Future`方法就是这样做的，它并行地执行指定的计算块，在这个例子中是向服务器发送请求和等待响应。

一旦服务器响应， ``f``  中的好友列表将变得可用。

未成功的尝试可能会导致一个异常(exception)。在下面的例子中， ``session`` 的值未被正确的初始化，
于是在 ``Future`` 的计算中将抛出 ``NullPointerException`` ，future ``f`` 不会圆满完成，而是以此异常失败。

.. code-block:: scala

  val session = null
  val f: Future[List[Friend]] = Future {
    session.getFriends
  }

``import ExecutionContext.Implicits.global`` 导入默认的全局执行上下文(global execution context)，执行上下文执行执行提交给他们的任务，也可把执行上下文看作线程池，这对于`Future`方法来说是必不可少的，因为这可以处理异步计算如何及何时被执行。我们可以定义自己的执行上下文，并在Future上使用它，但是现在只需要知道你能够通过上面的语句导入默认执行上下文就足够了。

我们的例子是基于一个假定的社交网络API，此API的计算包含发送网络请求和等待响应。提供一个涉及到你能试着立即使用的异步计算的例子是公平的。假设你有一个文本文件，你想找出一个特定的关键字第一次出现的位置。当磁盘正在检索此文件内容时，这种计算可能会陷入阻塞，因此并行的执行该操作和程序的其他部分是合理的(make sense)。

.. code-block:: scala

  val firstOccurrence: Future[Int] = Future {
    val source = scala.io.Source.fromFile("myText.txt")
    source.toSeq.indexOfSlice("myKeyword")
  }


Callback
---------

现在我们已经知道了如何创建 ``Future`` ，那么怎么获取 ``Future`` 中的计算结果呢？


在许多 ``Future`` 的实现中，一旦 ``Future`` 的用户对 ``Future`` 的结果感兴趣，它不得不阻塞它自己的计算直到 ``Future`` 完成，
然后才能使用 ``Future`` 的结果，然后继续它自己的计算。虽然这在Scala的 ``Future API`` （在后面会展示）中是允许的，但是从性能的角度来看更好的办法是一种完全非阻塞的方法，即在Future中注册一个回调。

注册回调函数最通常的形式是使用 ``OnComplete`` 方法，即创建一个 ``Try[T] => U`` 类型的回调函数。如果 ``Future`` 成功完成，
回调则会应用到 ``Success[T]`` 类型的值中，否则应用到 ``Failure[T]`` 类型的值中。

``Try[T]`` 和 ``Option[T]`` 或 ``Either[T, S]`` 相似，因为它是一个可能持有某种类型值的单值容器。然而，它被特意设计用来保存一个值或一个 throwable对象。`Option[T]` 既可以是一个值（如：Some[T]）也可以是无值（如：None），如果`Try[T]`获得一个值则它为`Success[T]` ，否则为`Failure[T]`的异常。 `Failure[T]`可以获取错误信息，而不仅仅是None。同时也可以把`Try[T]`看作一种特殊版本的`Either[Throwable, T]`，专门用于左值为可抛出类型(Throwable)的情形。

回到我们的社交网络的例子，假设我们想要获取我们最近的帖子并显示在屏幕上，
我们通过调用 ``getRecentPosts`` 方法获得一个返回值 ``List[String]`` ——一个近期帖子的列表文本：

.. code-block:: scala

  import scala.util.{Success, Failure}
  val f: Future[List[String]] = Future {
    session.getRecentPosts
  }
  f onComplete {
    case Success(posts) => for (post <- posts) println(post)
    case Failure(t) => println("An error has occured: " + t.getMessage)
  }


``onComplete`` 方法允许客户同时处理成功和失败的情况。如果仅需处理成功的情况，可以使用 ``onSuccess`` 回调函数使（该回调以一个偏函数(partial function)为参数）：

.. code-block:: scala

  val f: Future[List[String]] = Future {
    session.getRecentPosts
  }
  f onSuccess {
    case posts => for (post <- posts) println(post)
  }


如果只处理失败情况，可以使用 ``onFailure`` 回调函数：

.. code-block:: scala
  
  val f: Future[List[String]] = Future {
    session.getRecentPosts
  }
  f onFailure {
    case t => println("An error has occured: " + t.getMessage)  
  }
  f onSuccess {
    case posts => for (post <- posts) println(post)
  } 

如果任务失败，即 ``Future`` 抛出异常，则执行 ``onFailure`` 回调函数。

由于偏函数具有 ``isDefinedAt`` 方法， ``onFailure`` 回调函数只有在特定的 ``Throwable`` 类型对象下会触发。下面例子中的 ``onFailure`` 回调永远不会被触发：

.. code-block:: scala

  val f = Future {
    2 / 0
  }
  f onFailure {
    case npe: NullPointerException =>
      println("I'd be amazed if this printed out.")
  }

回到前面查找关键字的例子，我们可能想在屏幕上打印出此关键字的位置：

.. code-block:: scala

  val firstOccurrence: Future[Int] = Future {
    val source = scala.io.Source.fromFile("myText.txt")
    source.toSeq.indexOfSlice("myKeyword")
  }
  firstOccurrence onSuccess {
    case idx => println("The keyword first appears at position: " + idx)
  }
  firstOccurrence onFailure {
    case t => println("Could not process file: " + t.getMessage)
  }

回调函数 ``onComplete`` 、 ``onSuccess`` 、 ``onFailure`` 的返回结果为 ``Unit`` 类型，也就是说这些函数并不支持链式调用。

注意回调函数并不一定是由返回计算结果的线程调用，也不一定是由创建回调函数的线程来调用，只能说是由某个线程来调用。

而且回调函数的执行顺序不是固定的，实际上，回调函数不一定是顺序调用，也可能是并发执行的。

看下面的例子：

.. code-block:: scala

  @volatile var totalA = 0
  val text = Future {
    "na" * 16 + "BATMAN!!!"
  }
  text onSuccess {
    case txt => totalA += txt.count(_ == 'a')
  }
  text onSuccess {
    case txt => totalA += txt.count(_ == 'A')
  }

上面的例子中，两个回调函数如果是顺序执行的话，text的结果为18，但是也可能并发执行，这时结果可能为16或者2。

回调函数的相关规则：

- ``onComplete`` 不管结果是否成功或者失败都会执行

- ``onSuccess`` 只有成功才会执行

- ``onFailure`` 只有失败才会执行

- 回调的执行顺序不是固定的，除非使用自定义的 ``ExecutionContext``

- 在一些回调抛出异常的情况下，其他回调的执行不受影响

- 在某些情况下一些回调可能永远不能结束，导致其他回调不会执行，这时需要使用阻塞回调

- 一旦执行完，回调将从future对象中移除，这样更适合JVM的垃圾回收机制(GC)。


