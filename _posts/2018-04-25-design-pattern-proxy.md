---
layout: post
title: Java设计模式(二) - 代理模式
category: 设计模式
tags: [设计模式]
---

代理模式其定义为给某一个对象提供一个代理对象，并由代理对象控制对原对象的引用

根据代理对象的创建时期，代理类分为静态代理类和动态代理类两种，也就是我们常说的静态代理和动态代理。
它们的主要区别就是代理类创建时间不同：
- 静态代理类由程序员编写，再对其编译，在程序运行前就已经存在。
- 动态代理类在程序运行时，由反射机制动态创建。

静态代理通常只代理一个类，动态代理代理一个接口的多个实现类。静态代理知道要代理的是什么，而动态代理不知道要代理什么，只有在运行时才知道。

## 代码实现

### 静态代理

静态代理就是代理类是由程序员编写的，在编译期就确定好了的。

定义接口及其实现类
```java
public interface BuyCar {
    void buyCar();
}

public class BuyCarImpl implements BuyCar {
    @Override
    public void buyCar() {
        System.out.println("买车了");
    }
}
```

这就是`目标对象`和`目标对象的接口`，接下来定义代理对象

```java
public class BuyCarProxy implements BuyCar {
    private BuyCar target;
    public BuyCarProxy(BuyCar target) {
        this.target = target;
    }
    @Override
    public void buyCar() {
        System.out.println("买车前，期待中。。。");
        this.target.buyHouse();
        System.out.println("买成后，兴奋中。。。");
    }
}
```

这就是 `代理类` ，它也实现了 `目标对象的接口` 和 `buyCar` 方法

测试一下
```java
public class ProxyPatterTest {

    @Test
    public void buyCar() {
        //原对象
        BuyCar buyHouse = new BuyCarImpl();
        buyHouse.buyCar();

        //代理对象
        BuyCarProxy proxy = new BuyCarProxy(buyHouse);
        proxy.buyCar();
        /*
         * 输出：
         * 买车了
         * 买车前，期待中。。。
         * 买车了
         * 买成后，兴奋中。。。
         */
    }
}
```

这就是一个静态代理模式的的实现。代理模式中的所有角色(目标对象、目标对象的接口、代理类)等都是在编译期就确定好了的。

### JDK动态代理

Jdk 的动态代理，就是在程序运行的过程中，根据被代理的接口来动态生成代理类的 class 文件，并加载运行的过程。Jdk 从 1.3 开始支持动态代理。那么 Jdk 是如何生成动态代理的呢？Jdk 动态代理为什么不支持类的代理，只支持接口的代理？首先来看一下如何使用 Jdk 动态代理。Jdk 提供了 java.lang.reflect.Proxy 类来实现动态代理的，可通过它的 newProxyInstance 来获得代理实现类。同时对于代理的接口的实际处理，是一个 java.lang.reflect.InvocationHandler，它提供了一个 invoke 方法供实现者提供相应的代理逻辑的实现。

实现一个java.lang.reflect.InvocationHandler

```java
package com.renguangli.proxy;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

public class BuyCarHandler implements InvocationHandler {
    private Object target;
    public BuyCarHandler(Object target) {
        this.target = target;
    }
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("买车前，期待中。。。");
        Object invoke = method.invoke(target, args);
        System.out.println("买成后，兴奋中。。。");
        return invoke;
    }
}
```
测试一下

```java
@Test
public void dynamicProxy() {
    BuyCarHandler handler = new BuyCarHandler(new BuyCarImpl());
    BuyCar buyCar = (BuyCar)Proxy.newProxyInstance(BuyCarImpl.class.getClassLoader(), new Class[]{BuyCar.class}, handler);
    buyCar.buyCar();
}

/*
 * 输出：
 * 买车前，期待中。。。
 * 买车了
 * 买成后，兴奋中。。。
 */
```

