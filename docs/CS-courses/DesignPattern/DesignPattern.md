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
