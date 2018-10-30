---
layout: post
title: Java 核心知识回顾 - 异常处理机制
category: [Java]
tags: [Java]
---

异常是程序中的一些错误，但并不是所有的错误都是异常，有些错误有时候是可以避免的。

## 异常分类

### Java 中的异常主要分为两类 Error 和 Exception。

Error 和 Exception 都是 Throwable 的子类，Error 表示程序无法解决的错误，常见的有 `OutOfMemoryError`、`StackOverflowError`错误等。

Exception 表示程序执行时发生的异常，可以被处理。常见的有  `NullPointerException`、`ClassNotFoundException`、`IndexOutOfBoundsException` 异常等。

系统一旦出现 Error 错误，那么整个系统将无法继续运行。但是系统出现异常，只要处理得当，不会对系统造成多大影响。

### 异常又分为受检异常和运行时异常。

受检异常可以在编译时检测到，必须进行手动处理，否则无法通过编译。常见的有 `IOException`，`FileNotFoundException`异常。

运行时异常属于代码逻辑性异常，需要在运行期出现，可以不进行处理，同样可以通过编译，但是在运行时一旦出现该异常，则当前线程停止执行。一般情况下我们都会对运行时异常进行捕捉处理，以保证程序的正常运行，对于无关紧要的部分可以直接catch住，但对于影响当前线程后续执行的异常必须立即抛出来结束线程执行，这样能有效保护数据，不会因为异常而产生错误数据，一般我们会在事务中执行，一旦抛出异常，执行回滚，保证数据安全。常见的有 `NullPointerException`.

## 异常处理框架

### try catch

通常使用 `try catch` 来捕获异常

```java
try {
    int[] a = {1, 2};
    int x = a[0] / 0;
} catch (ArithmeticException e) {
    e.printStackTrace();
}
```

多重捕获块

```java
try {
    int[] a = {1, 2};
    int x = a[0] / 0;
    int y = a[2];
} catch (ArithmeticException e) {
    e.printStackTrace();
} catch (ArrayIndexOutOfBoundsException e) {
    e.printStackTrace();
}
```
或者这样

```java
try {
    int[] a = {1, 2};
    int x = a[0] / 0;
    int y = a[2];
} catch (ArithmeticException | ArrayIndexOutOfBoundsException e) {
    e.printStackTrace();
}
```

### finally

finally 不管有没有发生异常都会执行，除非退出 Java 虚拟机。通常用来用来关闭连接或者释放资源。

```java
OutputStream os = null;
try {
    os = new FileOutputStream("D:/a.txt");
    os.write("hello world".getBytes());
    os.flush();
} catch (IOException e) {
    e.printStackTrace();
} finally {
    try {
        if (os != null) {
            os.close();
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```
### try-with-resource

Jdk1.7 中新增了 `try-with-resource` 语法，当一个外部资源的对象实现了 `AutoCloseable` 接口，Jdk1.7 中便可以利用 `try-with-resource` 语法更优雅的关闭资源。

```
try (OutputStream os = new FileOutputStream("D:/a.txt");){
    os.write("hello world".getBytes());
    os.flush();
} catch (IOException e) {
    e.printStackTrace();
}
```

### throw throws

throw 用于手动抛出异常

throws 用于声明方法可能会抛出的异常

```java
public void throwsThrow(String str) throws Exception{
    if (str == null) {
        throw new Exception("parameter str not be null");
    }
}
```