创建型模式的作用就是创建对象，创建一个对象最熟悉的就是 new 一个对象了，然后 set 相关属性。但是，在很多场景下，我们需要给客户端提供更加友好的创建对象的方式，尤其是那种我们定义了类，但是需要提供给其他开发者用的时候。

## 简单工厂模式

简单工厂模式是属于创建型模式，又叫做静态工厂方法（Static Factory Method）模式，但不属于23种GOF设计模式之一。简单工厂模式是由一个工厂对象决定创建出哪一种产品类的实例。

简单工厂模式是工厂模式家族中最简单实用的模式，可以理解为是不同工厂模式的一个特殊实现。

简单工厂模式的实质是由一个工厂类根据传入的参数，动态决定应该创建哪一个产品类（这些产品类继承自一个父类或接口）的实例。

```java
public class ComputerFactory {

    public static Computer create(String type) {
        if ("notebook".equals(type)) {
            return new NotebookComputer(); // 笔记本
        } else if ("desktop".equals(type)) {
            return new DesktopComputer(); // 台式机
        }
        throw new IllegalArgumentException("No such computer:" + type);
    }
}
```

简单地说，简单工厂模式通常就是这样，一个工厂类 XxxFactory，里面有一个静态方法，根据我们不同的参数，返回不同的派生自同一个父类（或实现同一接口）的实例对象。

## 工厂模式

工厂模式需要使用两个或两个以上的工厂。

```java
public interface ComputerFactory {
     Computer create(String type);
}
public class MacComputerFactory implements ComputerFactory {

    @Override
    public Computer create(String type) {
        if ("notebook".equals(type)) {
            return new MacNotebookComputer(); // 苹果笔记本电脑
        } else if ("desktop".equals(type)) {
            return new MacDesktopComputer(); // 苹果台式机电脑
        }
        throw new IllegalArgumentException("No such computer:" + type);
    }
}
public class LenovoComputerFactory {

    public static Computer create(String type) {
        if ("notebook".equals(type)) {
            return new LenovoNotebookComputer(); // 联想笔记本电脑
        } else if ("desktop".equals(type)) {
            return new LenovoDesktopComputer(); // 联想台式机电脑
        }
        throw new IllegalArgumentException("No such computer:" + type);
    }
} 	
```

客户端调用

```java
public class App {
    public static void main(String[] args) {
        ComputerFactory f = new MacComputerFactory();
        ComputerFactory f1 = new LenovoComputerFactory();
        final Computer macNotebookFactory = f.create("notebook");
        final Computer lenovoNotebookFactory = f1.create("notebook");
    }
}
```

虽然都调用 create("notebook") 生产笔记本，但是不同的工厂生产出来的笔记本是不一样的。

## 抽象工厂模式

## 单例模式

## 代理模式

## 建造者模式

## 原型模式

