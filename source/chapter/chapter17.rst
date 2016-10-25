====================
第十七章 Try与异常处理
====================

---------------
异常的抛出和捕获
---------------

Scala中使用 ``throw`` 语句抛出异常：

.. code-block:: scala

  case class Customer(age: Int)
  class Cigarettes
  case class UnderAgeException(message: String) extends Exception(message)
  def buyCigarettes(customer: Customer): Cigarettes =
    if (customer.age < 16)
      throw UnderAgeException(s"Customer must be older than 16 but was ${customer.age}")
    else new Cigarettes
    
被抛出的异常能够以类似 Java 中的方式被捕获，虽然是使用偏函数来指定要处理的异常类型。 此外，
Scala 的 ``try/catch`` 是表达式（返回一个值），因此下面的代码会返回异常的消息：

.. code-block:: scala

  val youngCustomer = Customer(15)
  try {
    buyCigarettes(youngCustomer)
    "Yo, here are your cancer sticks! Happy smokin'!"
  } catch {
      case UnderAgeException(msg) => msg
  }

此外，还可以使用 ``finally`` 语句。

---------------
函数式的错误处理
---------------

Scala 使用一个特定的类型来表示可能会导致异常的计算，这个类型就是 ``Try`` 。

++++++++
try的语义
++++++++

解释 ``Try`` 最好的方式是将它与上一章所讲的 ``Option`` 作对比。

``Option[A]`` 是一个可能有值也可能没值的容器， ``Try[A]`` 则表示一种计算： 这种计算在成功的情况下，返回类型为 ``A`` 的值，在出错的情况下，返回 ``Throwable`` 。 这种可以容纳错误的容器可以很轻易的在并发执行的程序之间传递。

Try 有两个子类型：

- ``Success[A]`` ：代表成功的计算。
- 封装了 ``Throwable`` 的 ``Failure[A]`` ：代表出了错的计算。

如果知道一个计算可能导致错误，我们可以简单的使用 ``Try[A]`` 作为函数的返回类型。 这使得出错的可能性变得很明确，而且强制客户端以某种方式处理出错的可能。

假设，需要实现一个简单的网页爬取器：用户能够输入想爬取的网页 ``URL`` ， 程序就需要去分析 ``URL`` 输入，并从中创建一个 ``java.net.URL`` ：

.. code-block:: scala

  import scala.util.Try
  import java.net.URL
  def parseURL(url: String): Try[URL] = Try(new URL(url))

正如你所看到的，函数返回类型为 ``Try[URL]``： 如果给定的 ``url`` 语法正确，这将是 ``Success[URL]``， 否则， ``URL`` 构造器会引发 ``MalformedURLException`` ，从而返回值变成 ``Failure[URL]`` 类型。

上例中，我们还用了 ``Try`` 伴生对象里的 ``apply`` 工厂方法，这个方法接受一个类型为 ``A`` 的 传名参数， 这意味着， ``new URL(url)`` 是在 ``Try`` 的 ``apply`` 方法里执行的。

``apply`` 方法不会捕获任何非致命的异常，仅仅返回一个包含相关异常的 ``Failure`` 实例。

++++++++
使用Try
++++++++

使用 ``Try`` 与使用 ``Option`` 非常相似，在这里你看不到太多新的东西。

你可以调用 ``isSuccess`` 方法来检查一个 ``Try`` 是否成功，然后通过 ``get`` 方法获取它的值， 但是，这种方式的使用并不多见，因为你可以用 ``getOrElse`` 方法给 ``Try`` 提供一个默认值：

.. code-block:: scala

  val url = parseURL(Console.readLine("URL: ")) getOrElse new URL("http://duckduckgo.com")

如果用户提供的 ``URL`` 格式不正确，我们就使用 ``DuckDuckGo`` 的 ``URL`` 作为备用。

++++++++
模式匹配
++++++++

``Success`` 和 ``Failure`` 类都是样式类，所以 ``Try`` 也支持模式匹配：

.. code-block:: scala

  import scala.util.Success
  import scala.util.Failure
  getURLContent("http://danielwestheide.com/foobar") match {
    case Success(lines) => lines.foreach(println)
    case Failure(ex) => println(s"Problem rendering URL content: ${ex.getMessage}")
  }

++++++++++++
从故障中恢复
++++++++++++

如果想在失败的情况下执行某种动作，没必要去使用 ``getOrElse`` ， 一个更好的选择是 ``recover`` ，它接受一个偏函数，并返回另一个 ``Try`` 。 如果 ``recover`` 是在 ``Success`` 实例上调用的，那么就直接返回这个实例，否则就调用偏函数。 如果偏函数为给定的 ``Failure`` 定义了处理动作， ``recover`` 会返回 ``Success`` ，里面包含偏函数运行得出的结果。

下面是应用了 ``recover`` 的代码：

.. code-block:: scala

  import java.net.MalformedURLException
  import java.io.FileNotFoundException
  val content = getURLContent("garbage") recover {
   case e: FileNotFoundException => Iterator("Requested page does not exist")
   case e: MalformedURLException => Iterator("Please make sure to enter a valid URL")
   case _ => Iterator("An unexpected error has occurred. We are so sorry!")
  }
