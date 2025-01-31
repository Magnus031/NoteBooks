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
