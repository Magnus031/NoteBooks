# <center>注解</center>

> 其实注解不只有 Java 中存在，但是我既然是以Java为主的技术栈，那么就以Java为主来进行讲解。


## Introduction 

> 注解的出现，其实是一种 `低耦合` 和 `高效率` 的trade-off. 

首先，在注解出现之前，`XML` 是各个框架的青睐者，有点在于其的灵活性带来的松耦合的有点，但是随着项目的庞大，`XML` 文件中的内容也会变得越来越多，这样就会导致我们的项目的维护成本变得越来越高。而且，`XML` 文件的编写也是比较繁琐的，所以注解的出现就是为了解决这个问题。

二者各有利弊，`XML` 注重的是低耦合，而注解注重的是高效率。


### 什么是注解？

注解通常只包含一些元数据，并不包含实际业务的业务逻辑或者行为。注解方法定义时候，可以指定默认值(如果没有，方法需要通过代码实现)，但是注解本身并不实现这些方法。

或许可以直接理解为，是一种对代码的标记或者标签，它提供了编译时候，运行时候，或者其他工具的一些信息，但不改变程序本身的行为。注解本身并不执行任何操作，通常由工具或者框架通过反射来处理这些注解。从而实现不同的行为



就以我们最熟悉的 `@Data` 注解来说就是，它是 `Lombok` 提供的一个注解，用来自动生成 `getter` 和 `setter` 方法。主要就是一个标记，在进行编译代码的时候，`Lombok` 插件提供的编译器会通过这个注解来往字节码文件中插入这些方法。从而编译后最终的`.class`文件包含了这些自动生成的方法，开发者无需手动编写它们。

### **注解的本质**

> The common interface extended by all annotation types.

所有的注解类型都继承自 `java.lang.annotation.Annotation` 接口。

以简单的 `@Override` 注解为例。其实就是:
```java
// 内置定义
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.SOURCE)
public @interface Override {
}

// 本质上就是,继承了 Annotation 接口
public interface Override extends Annotation {

}


```


接下来的重点在于，程序要怎么解析这些注解，这个就是我们要讲解的重点。如果没有对应的解析，它们的作用甚至不如 `XML` 文件来的直观。也不如注释。


解析一个类的方法有两种:

- 一种是编译器的直接扫描

    编译器在对 Java 代码编译字节码的过程中会检测到某个类或者方法是否被注解修饰，这时它就会对这些注解进行处理。

    一个简单的例子就是 `@Override`, 编译器在编译字节码的时候，一旦发现出现了方法重写的情况，就会检测当前方法的函数签名是否真正重写了父类的某个方法，也就是比较父类中是否具有一个相同的方法签名。

    **但这种只限定于编译器内部内置熟悉的注解类型，如果是我们自己编写的注解，就不能通过编译器的自动扫描进行判定了**

- 一种是运行期间的反射



### **元注解**

> 字面意思就是用来修饰注解的注解

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.SOURCE)
public @interface Override {
}
```

上面的代码段中的`@Target` 和 `@Retention` 就是元注解，用来修饰注解的注解。作用就是用来指定某个注解生命周期以及作用目标等信息.

常见的元注解:

- `@Target` : 用来指定注解的作用目标

    - 也就是用来指明这个注解的修饰对象是谁 ? 类 ？ 方法？ 还是字段属性的？
- `@Retention` : 用来指定注解的生命周期
- `@Documented` : 用来指定注解是否会被javadoc工具提取
- `@Inherited` : 用来指定注解是否会被子类继承

#### `@Target`

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Target {
    ElementType[] value();
}
```

上面的代码段其实就是 `@Target` 的源码，其中 `ElementType` 就是一个枚举类型，其中包括各种类型的对象元素:

- `TYPE` : 类、接口（包括注解类型）或枚举声明
- `FIELD` : 字段声明（包括枚举常量）
- `METHOD` : 方法声明
- `PARAMETER` : 参数声明
- `CONSTRUCTOR` : 构造方法声明
- `LOCAL_VARIABLE` : 局部变量声明
- `ANNOTATION_TYPE` : 注解类型声明
- `PACKAGE` : 包声明
...

而我们可以在注解中指定 `@Target(value = {ElementType.METHOD})` 来指定这个注解只能修饰方法。


####  `@Retention`

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Retention {
    RetentionPolicy value();
}
```

`RetentionPolicy` 也是一个枚举类型，其中包括三种类型:

- `SOURCE` : 注解只保留在源码中，编译时会被丢弃
- `CLASS` : 注解会被编译到字节码文件中，但是在运行时会被丢弃
- `RUNTIME` : 注解会被编译到字节码文件中，并且在运行时也会被保留，这样我们就可以通过反射来获取这个注解的信息了。

### 注解与反射

> 接下来看一下虚拟机内部的注解是如何被解析的

```java
@Target(value = {ElementType.METHOD,ELementType.TYPE})
@Retention(value = RetentionPolicy.RUNTIME)
public @interface Hello{
    String value() default "hello";
}
```


因为虚拟机规范中定义了一系列和注解相关的属性表，也就是说无论是字段，方法，或者类本身，如果被注解修饰了，可以被写进字节码文件中。属性表有以下的几种 : 

- `RuntimeVisibleAnnotations` : 运行时可见的注解    
- `RuntimeInvisibleAnnotations` : 运行时不可见的注解
- `RuntimeVisibleParameterAnnotations` : 运行时可见的参数注解
- `RuntimeInvisibleParameterAnnotations` : 运行时不可见的参数注解
- `AnnotationDefault` : 注解的默认值

其实这些属性表的作用就是可以提醒大家从整体上构建一个基本的印象，注解在字节码文件中是如何储存的。


下面一个例子来进行整体的讲解:

首先 `-Dsun.misc.ProxyGenerator.saveGeneratedFiles=true` 这个参数是用来保存生成的代理类的文件的。用于捕捉 JDK 动态代理类。

```java

public class Test{
    @Hello("world")
    public static void main(String[] args){
        Class cls = Test.class;
        Method method = cls.getMethod("main",String[].class);
        Hello hello = method.getAnnotation(Hello.class);
    }
}
```
我们上面的代码通过 `getAnnotation` 方法来获取来获取 `Hello` 注解的实例。 其实JDK 是通过动态代理的机制来生成一个实现我们注解(接口)的代理类。


`AnnotationInvocationHandler` 是 JDK 动态代理的一个实现类，它的作用就是用来处理注解的。主要的方法就是 `invoke`,并且有一个成员变量是 

`private final Map<String,Object> memeberValues;` 用来存储注解的属性值。

下面是具体的代码实现:
```java
public Object invoke(Object var1,Method var2,Object[] var3){    
    // 通过反射，获得 Annotation 的属性值
    String var4 = var2.getName();
    Class[] var5 = var2.getParameterTypes();

    if(var4.equals("equals") && var5.length == 1 && var5[0].equals(Object.class)){
        Object var6 = var3[0];
        return this.equalsImpl(var6);
    }else if(var5.length != 0){
        throw new AssertionError("Too many arguments");
    }else{
        byte var7 = -1;
        switch(var4.hashCode()){
            case -1776922004:
                if(var4.equals("annotationType")){
                    var7 = 0;
                }
                break;
            case 147696667:
                if(var4.equals("toString")){
                    var7 = 1;
                }
                break;
            case 1444986633:
                if(var4.equals("hashCode")){
                    var7 = 2;
                }
            }
        }

        switch(var7){
            case 0:
                return this.toStringImpl();
            case 1:
                return this.hashcodeImpl();
            case 2:
                return this.type;
            default:
                // 如果都不是 `toString` 和 `hashcode` 方法，那么就调用注解的属性值,从memberValues中获取到对应的属性值
                Object var6 = this.memberValues.get(var4);
                if(var5 == null){
                    throw new IncompleteAnnotationException(this.type,var4);
                }else if(var6 instanceof ExceptionProxy){
                    throw ((ExceptionProxy)var6).generateException();
                else {
                    if(var6.getClass().isArray() && Array.getLength(var6) != 0){
                        var6 = cloneArray(var6);
                    }
                    return var6;
                }

    }
        }

}   
```

我们来看上面的代码 :

- `var(Object var1)` :  表示的是被代理的注解实例

- `var2(Method var2)` : 表示的是反射中被调用的目标方法，在这里，它是通过反射获取到调用方法的对象。里面包含了这个方法的详细信息。
- `var3(Object[] var3)` : 表示的是方法的参数列表

其实就是通过动态代理来帮我们实现注解。因为这个注解中可能有多个子方法，然后 `AnnotationInvocationHandler` 就是通过反射来调用这些方法。从而达到帮我们完成返回注解的属性值目的。


invoke 放啊并不直接调用注解方法，而是根据方法名来访问注解中的属性值，并且根据这些值来执行不同的操作。

