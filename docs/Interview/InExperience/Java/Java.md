# <center>Java</center>

## 常见八股题
### Q1: `String`,`StringBuilder`,`StringBuffer` 三者的区别是什么？

两句话讲清楚就是 `String` 是不可变的，但后面的两者是可变的字符串对象，`StringBuilder` 是线程不安全的，`StringBuffer` 是线程安全的。所以在单线程的时候，我们可以使用 `StringBuilder` 来代替 `StringBuffer`。这样的性能会更高，因为省去了加锁的过程。

我们看源码来辨析，上面的一段是 `StringBuilder`,而下面那一段是`StringBuffer`的 `append()` 方法

```java

// AbstractStringBuilder append()
public AbstractStringBuilder append(String str) {
        if (str == null) {
            return appendNull();
        }
        int len = str.length();
        ensureCapacityInternal(count + len);
        putStringAt(count, str);
        count += len;
        return this;
    }

// StringBuilder append()
    @Override
    public StringBuilder append(String str) {
        super.append(str);
        return this;
    }


// StringBuffer append()
    @Override
    public synchronized StringBuffer append(Object obj) {
        toStringCache = null;
        super.append(String.valueOf(obj));
        return this;
    }
```
我们可以很显然的看到，二者都实现了`AbstractStringBuilder`接口中的`append()` 方法，但是`StringBuffer`中的`append()`方法是加了`synchronized`关键字的，所以是线程安全的。


### Q2 Java中的字符串比较

- `equals` : 比较的是内容
- `==` : 比较的是引用地址

> `intern()` 方法会将字符串对象加入到常量池中，如果常量池中已经存在该字符串对象，则直接返回常量池中的对象引用。


1. 对于字面量创建的字符串, Java 会将其存放在 **常量池** 中，多个相同的字面量会共享一个对象:

```java
String s1 = "hello";
String s2 = "hello";
System.out.println(s1 == s2); // true（指向常量池中的同一对象）
```

2. 对于使用 `new` 关键字创建的字符串, Java 会在堆中创建一个新的对象:

```java
String s3 = new String("hello");
String s4 = new String("hello");
System.out.println(s3 == s4); // false（不同对象）
```

3. 对于动态拼接生成的字符串, Java 会在堆中创建一个新的对象: 也就是不会存入常量池中。

```java
String s5 = "he";
String s6 = s5 + "llo"; // 运行时拼接，生成新对象
System.out.println(s6 == s1); // false
```