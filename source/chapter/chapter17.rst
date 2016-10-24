==================
第十七章 Either类型
==================

-----------
Either 语义
-----------

``Either`` 也是一个容器类型，但不同于 ``Try`` 、 ``Option`` ，它需要两个类型参数： ``Either[A, B]`` 要么包含一个类型为 ``A`` 的实例，
要么包含一个类型为 ``B`` 的实例。 这和 ``Tuple2[A, B]`` 不一样， ``Tuple2[A, B]`` 是两者都要包含。

``Either`` 只有两个子类型： ``Left`` 、 ``Right`` ， 如果 ``Either[A, B]`` 对象包含的是 ``A``  的实例，
那它就是 ``Left`` 实例，否则就是 ``Right`` 实例。

在语义上，``Either`` 并没有指定哪个子类型代表错误，哪个代表成功， 毕竟，它是一种通用的类型，适用于可能会出现两种结果的场景。
而异常处理只不过是其一种常见的使用场景而已， 不过，按照约定，处理异常时， ``Left`` 代表出错的情况， ``Right`` 代表成功的情况。

-----------
创建 Either
-----------

创建 ``Either`` 实例非常容易， ``Left`` 和 ``Right`` 都是样例类。 要是想实现一个 “坚如磐石” 的互联网审查程序，可以直接这么做：

.. code-block:: scala

  import scala.io.Source
  import java.net.URL
  def getContent(url: URL): Either[String, Source] =
   if(url.getHost.contains("google"))
     Left("Requested URL is blocked for the good of the people!")
   else
    Right(Source.fromURL(url))
调用 ``getContent(new URL("http://danielwestheide.com"))`` 会得到一个封装有 ``scala.io.Source`` 实例的 ``Right``， 
传入 ``new URL("https://plus.google.com")`` 会得到一个含有 ``String`` 的 ``Left`` 。

-----------
Either 用法
-----------

``Either`` 基本的使用方法和 ``Option``、 ``Try`` 一样： 调用 ``isLeft`` （或 ``isRight`` ）方法询问一个 ``Either`` ，判断它是 ``Left`` 值，
还是 ``Right`` 值。 可以使用模式匹配，这是最方便也是最为熟悉的一种方法：

.. code-block:: scala

  getContent(new URL("http://google.com")) match {
   case Left(msg) => println(msg)
   case Right(source) => source.getLines.foreach(println)
  }

