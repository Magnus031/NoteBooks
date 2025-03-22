# <center>DesignPattern</center>

## Introduce 
> From wiki :
>
> Software design pattern refers to a **reusable, proven solution to a specific, recurring problem typically focused on component-level design**, though they can sometimes span multiple components. Design patterns address specific issues related to **object creation, interaction, or behavior**.

## Factory Design Pattern
工厂设计模式

是一种创建型的设计模式，用于解决对象的创建问题，旨在通过讲对象实例化过程封装到了一个独立的类/方法中从而实现更高的灵活性和可维护性。

### Simple Factory 
简单工厂的设计模式，通过一个工厂类，根据不同条件创建并返回具体的实例

### Factory Method 
定义一个接口或抽象类，由子类决定实例化哪种产品。

- 每个具体产品都有一个对应的工厂类
- 新增产品时只需要拓展工厂类，遵循开闭原则



## Singleton Design Pattern
> 接下来我们介绍单例模式 
>
> from wiki Introduction:
> In object-oriented programming, **the singleton pattern is a software design pattern that restricts the instantiation of a class to a singular instance**. It is one of the well-known "Gang of Four" design patterns, which describe how to solve recurring problems in object-oriented software.[1] The pattern is useful when exactly one object is needed to coordinate actions across a system. 

依旧是通过 `RPC` 项目的例子来进行阐述。首先，由于我们的项目是一个`RPC`框架，目的是能够提供给不同的consumer进行使用，所以我们又不能让每个consumer进行单独的配置文件的配置。那么我们就需要一个单例模式来进行配置文件的读取。在引入 `RPC` 框架启动的时候，从配置文件中读取配置并创建对象实例，之后就集中地从这个对象中获取配置信息。**减少了性能的开销**

### property 

- Advantages : 
    - 保证了系统内存中只有一个实例，减少了内存的开销
    - 可以避免对资源的多重占用
    - 设置全局访问点，严格控制访问
- Disadvantages : 
    - 不方便进行测试，因为只有一个全局实例
    - **重点关注并发访问的情况，因为有可能会存在重复创建单个实例的情况**

### 并发处理的几种解决方式
#### 双重锁检查

```java
public class Singleton {
    private static volatile Singleton instance;

    private Singleton() {
    }

    public static Singleton getInstance() {
        if (instance == null) {                         // 第一次检查
            synchronized (Singleton.class) {
                if (instance == null) {                 // 第二次检查
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}

```

字面意思，就是如果已经存在了这个实例，那么我们就直接返回 `instance` 这个实例对象。倘若没有创建过这个实例对象，那么我们进行双重锁检查的目的就是为了保证在多线程的情况下，只有一个线程能够创建这个实例对象。首先第一次进行锁检查 `instance == null` 如果为`true` 则说明我们当前还没有成功的创建出来`instace` 实例，可以进入接下来的同步块。第二次进行锁检查，我们使用了 `synchronized` 关键字，目的是为了保证在多线程的情况下，只有一个线程能够进入这个同步块，然后进行实例的创建。

几点注意事项 :

1. `volatile` : 关键字的目的是为了保证可见性，防止指令重排，从而影响到多线程并发对于`instance`创建产生的影响。
2. `synchronized(Singleton.class)` 是给`Singleton.class`这个类的对象进行加锁。
   > 为什么不能直接使用 `synchronized(this)` ? 
   答案是因为，当前 `instance` 这个对象还未被成功的创建，因此我们需要对`Singleton.class`这个类的对象进行加锁，保证在多线程的情况下，只有一个线程能够进入这个同步块。

以上就是对于单例模式的简单介绍


## 设计模式的分类

- 创建型设计模式
- 结构型设计模式
- 行为型设计模式


### 结构型设计模式
> 结构型设计模式是关于类和对象组合的设计模式。它们用于创建类的对象模型，这些模型可以通过组合接口和实现继承来完成。

- 目的: 解决类或者对象的组合问题，定义如何将类或者对象组合在一起形成更大的结构。

- 举例: 
    
    - 适配器模式（Adapter）​：将一个类的接口转换成客户端期望的另一个接口。

    - ​桥接模式（Bridge）​：将抽象部分与实现部分分离，使它们可以独立变化.

    - 组合模式（Composite）​：将对象组合成树形结构以表示“部分-整体”的层次结构。
    ​
    - 装饰器模式（Decorator）​：动态地为对象添加额外的职责，而不会影响其他对象。
    
    - 外观模式（Facade）​：为复杂的子系统提供一个简单的接口。  
    - 享元模式（Flyweight）​：通过共享技术有效地支持大量细粒度对象。

    - 代理模式（Proxy）​：为其他对象提供一个代理，以控制对这个对象的访问。


### 创建型设计模式

- **目的:** 解决对象的创建问题，将对象的创建与行为分离开，从而降低系统的耦合度。
- **核心思想:** *提供灵活的机制来创建对象，避免硬编码的创建逻辑

- 举例:
    - ​单例模式（Singleton）​：确保一个类只有一个实例，并提供全局访问点。
    - ​工厂方法模式（Factory Method）​：定义一个创建对象的接口，但由子类决定实例化哪个类。 
    - 抽象工厂模式（Abstract Factory）​：提供一个接口，用于创  建相关或依赖对象的家族，而不需要指定具体类。
    - ​建造者模式（Builder）​：将一个复杂对象的构建与表示分离，使 得同样的构建过程可以创建不同的表示。
    - ​原型模式（Prototype）​：通过复制现有对象来创建新对象，而不是通过实例化类。


### 行为型设计模式
- **目的:** 解决对象之间的职责分配和通信的问题，定义对象之间的交互方式
- **核心思想:** *提供灵活的机制来实现对象之间的通信，降低系统的耦合度

- 举例:
    - ​模板方法模式（Template Method）​：定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。
    - ​命令模式（Command）​：将请求封装成对象，从而使你可用不同的请求对客户进行参数化。
    - ​迭代器模式（Iterator）​：提供一种方法顺序访问一个聚合对象中的各个元素，而又不暴露其内部表示。
    - ​观察者模式（Observer）​：定义对象间的一种一对多的依赖关系，以便当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并自动刷新。
    - ​中介者模式（Mediator）​：用一个中介对象来封装一系列的对象交互。
    - ​备忘录模式（Memento）​：在不破坏封装的前提下，保持对象的内部状态，并在适当的时候恢复。
    - ​解释器模式（Interpreter）​：给定一个语言，定义它的文法的一种表示，并定义一个解释器，这个解释器使用该表示来解释语言中的句子。
    - ​状态模式（State）​：允许一个对象在其内部状态改变时改变它的行为。
    - ​策略模式（Strategy）​：定义一系列算法，把它们一个个封装起来，并使它们可以互相替换。
    - ​职责链模式（Chain of Responsibility）​：为解除请求的发送者和接收者之间的耦合，而使多个对象都有机会处理这个请求。
    - ​访问者模式（Visitor）​：表示一个作用于某对象结构中的各元素