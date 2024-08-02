# Java异常与错误

## Exception

### 运行时异常（RuntimeException）

继承关系：Serializable接口-->Throwable类-->Exception类-->RuntimeException类

运行时异常是非受检异常：RuntimeException类及其子类，如NullPointerException、IndexOutOfBoundsException。



### 非运行时异常

继承关系：Serializable接口-->Throwable类-->Exception类

非运行时异常时是检异常：Exception类及其子类，如

### 区别

- 运行时异常是RuntimeException类及其子类的异常，是非受检异常，如NullPointerException、IndexOutOfBoundsException等。由于这类异常要么是系统异常，无法处理，如网络问题；
  要么是程序逻辑错误，如空指针异常；JVM必须停止运行以改正这种错误，所以运行时异常可以不进行处理（捕获或向上抛出，当然也可以处理），而由JVM自行处理。Java Runtime会自动catch到程序throw的RuntimeException，然后停止线程，打印异常。
- 非运行时异常是RuntimeException以外的异常，类型上都属于Exception类及其子类，是受检异常。非运行时异常必须进行处理（捕获或向上抛出），如果不处理，程序将出现编译错误。一般情况下，API中写了throws的Exception都不是RuntimeException。

### 自定义异常

自定义异常是该继承Exception还是RuntimeException呢？

定义异常是为了抛出异常，如果抛出的是运行时异常，不需要捕获和处理异常，如果定义的是非运行时异常，就需要捕获异常或者继续向外抛出异常（方法后面加throws）；

思考一个场景：在新增一条记录时，需要判断是否已存在，如果存在就抛出异常，且后续代码不继续执行（停止线程），此时就该抛出自定义的运行时异常。

思考：在抛出异常后，后续代码是否还会执行？首先代码需编译通过，如果直接抛出运行时异常，后续代码不可达，编译不通过，如果在if判断中抛出运行时异常，后续代码可达（前提时if条件不是恒定的），抛出异常后后续代码不会执行；如果直接抛出非运行时异常，不捕获处理，后续代码不可达；

总结：抛出运行时异常是为了直接停止线程，后续代码不需要继续执行；捕获非运行时异常后，做适当处理，后续代码可以继续运行，这种情况就使用费运行时异常。

## Error

