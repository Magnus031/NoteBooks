# <center>导论</center>

### Collections 中的 `fail-fast` 和 `fail-safe`

首先，介绍一下什么是 `fail-fast` 和 `fail-safe` ? 在Java的集合框架中，二者是对应的两种不同的行为模式，用于处理集合在遍历过程中对集合结构的修改。他们的核心区别是在遍历集合的时候，对集合结构修改时候对响应方式。


#### `fail-fast`
- `fail-fast` : 这种方式是在遍历集合的时候，如果集合的结构发生了变化，那么就会抛出 `ConcurrentModificationException` 异常。这种方式是一种及时失败的方式，可以保证在多线程的情况下，不会出现数据的不一致性。就如同它的名称一样，**快速的发现问题出现，及时的进行处理。**

这种机制主要用于单线程环境，用于检测不正确的集合修改操作。

##### 常见的`fail-fast`集合类:

- `ArrayList`
- `HashMap`
- `HashSet`
- `Linked-List`


```java
import java.util.ArrayList;
import java.util.Iterator;

public class FailFastExample {
    public static void main(String[] args) {
        ArrayList<String> list = new ArrayList<>();
        list.add("One");
        list.add("Two");
        list.add("Three");

        Iterator<String> iterator = list.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
            // 在遍历过程中修改集合结构
            list.add("Four");  // 会抛出 ConcurrentModificationException
        }
    }
}
```
我们看上面的代码，就是在 `Iterator` 进行遍历的时候，对集合进行了改变，就会抛出异常。


#### `fail-safe` 安全失败

`fail-safe` 重点在 `safe` 上，表示的是在遍历集合的过程中，即使集合在遍历的过程中被修改，集合的结构也不会引发异常。**这种机制通常会创建一个新的集合副本，然后在副本上进行遍历，这样就不会影响原集合的结构。**

<span style = "color:red">区别在于，`fail-safe` 不会立即的抛出异常</span>

##### 常见的`fail-safe`集合类:
我们通过分析，其实也就知道了`fail-safe`的集合类都是并发的集合类，因为在并发的情况下，我们需要保证数据的一致性。


- `CopyOnWriteArrayList` 在修改的时候，会复制整个数组并在副本上进行修改
- `CopyOnWriteArraySet` 
- `ConcurrentHashMap` 在修改的时候，会复制整个数组并在副本上进行修改

```java
import java.util.concurrent.CopyOnWriteArrayList;

public class FailSafeExample {
    public static void main(String[] args) {
        CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();
        list.add("One");
        list.add("Two");
        list.add("Three");

        // 创建一个迭代器
        for (String item : list) {
            System.out.println(item);
            // 在遍历过程中修改集合结构
            list.add("Four");  // 不会抛出异常
        }
    }
}
```

优点 ： 

- 支持并发修改
- 不会抛出异常

**缺点也很明显**
缺点:

- 具有比较高的内存消耗
- 副本与原来的集合不是实时同步的
