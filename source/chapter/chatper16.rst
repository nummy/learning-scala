====================
第十六章 Try与异常处理
====================

---------------
异常的抛出和捕获
---------------

Scala 也允许抛出异常：

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
