# <center>ConcurrentHashMap</center>

> 进行 ConcurrentHashMap 的源码分析

首先是 分成两个版本的 ConcurrentHashMap ，一个是 JDK1.7 的版本，一个是 JDK1.8 的版本。

- JDK 1.7 版本的 实现是通过设置多个 Segment 来实现的，每一个Segment是一个类似`HashMap` 的结构，每一个 `HashMap` 的内部是可以进行扩容的。但是 `Segement` 的个数，其实也就是支持最多的线程并发数目是固定的。

- JDK 1.8 版本的实现是通过 `Node` 和 `TreeNode` 来实现的，`Node` 是一个链表结构，`TreeNode` 是一个红黑树结构。在 JDK 1.8 中，`ConcurrentHashMap` 是通过 `CAS` 操作来保证线程安全的。


## JDK1.7 版本的 ConcurrentHashMap

### 初始化

```java
// 无参构造
public ConcurrentHashMap() {
    this(DEFAULT_INITIAL_CAPACITY, DEFAULT_LOAD_FACTOR, DEFAULT_CONCURRENCY_LEVEL);
}

    /**
     * 默认初始化容量
     */
    static final int DEFAULT_INITIAL_CAPACITY = 16;

    /**
     * 默认负载因子
     */
    static final float DEFAULT_LOAD_FACTOR = 0.75f;

    /**
     * 默认并发级别
     */
    static final int DEFAULT_CONCURRENCY_LEVEL = 16;
```

我们看上面的无参构造，其实就是指出了三个默认的参数:

- `DEFAULT_INITIAL_CAPACITY` : 默认的初始化容量是 16
- `DEFAULT_LOAD_FACTOR` : 默认的负载因子是 0.75
- `DEFAULT_CONCURRENCY_LEVEL` : 默认的并发级别是 16


有参构造
```java
@SuppressWarnings("unchecked")
public ConcurrentHashMap(int initialCapacity,float loadFactor, int concurrencyLevel) {
    // 参数校验
    if (!(loadFactor > 0) || initialCapacity < 0 || concurrencyLevel <= 0)
        throw new IllegalArgumentException();
    // 校验并发级别大小，大于 1<<16，重置为 65536
    if (concurrencyLevel > MAX_SEGMENTS)
        concurrencyLevel = MAX_SEGMENTS;
    // Find power-of-two sizes best matching arguments
    // 2的多少次方
    int sshift = 0;
    int ssize = 1;
    // 这个循环可以找到 concurrencyLevel 之上最近的 2的次方值
    while (ssize < concurrencyLevel) {
        ++sshift;
        ssize <<= 1;
    }
    // 记录段偏移量
    this.segmentShift = 32 - sshift;
    // 记录段掩码
    this.segmentMask = ssize - 1;
    // 设置容量
    if (initialCapacity > MAXIMUM_CAPACITY)
        initialCapacity = MAXIMUM_CAPACITY;
    // c = 容量 / ssize ，默认 16 / 16 = 1，这里是计算每个 Segment 中的类似于 HashMap 的容量
    int c = initialCapacity / ssize;
    if (c * ssize < initialCapacity)
        ++c;
    int cap = MIN_SEGMENT_TABLE_CAPACITY;
    //Segment 中的类似于 HashMap 的容量至少是2或者2的倍数
    while (cap < c)
        cap <<= 1;
    // create segments and segments[0]
    // 创建 Segment 数组，设置 segments[0]
    Segment<K,V> s0 = new Segment<K,V>(loadFactor, (int)(cap * loadFactor),
                         (HashEntry<K,V>[])new HashEntry[cap]);
    Segment<K,V>[] ss = (Segment<K,V>[])new Segment[ssize];
    UNSAFE.putOrderedObject(ss, SBASE, s0); // ordered write of segments[0]
    this.segments = ss;
}
```
1. 先是进行参数的校验

    - 参数是否合法
    - 并发级别是否已经超过的了最大的并发级别 ？ 如果超过了，就设置为最大的并发级别


1.7 版本的过时了，下面详细介绍 1.8 版本的 ConcurrentHashMap


## JDK1.8 版本的 ConcurrentHashMap

> JDK1.8 版本的 ConcurrentHashMap 是通过Node数组+链表/红黑树的办法实现的，和 HashMap一样的时候，当冲突的链表达到一定长度的时候，链表会被转化成红黑树。

### initTable 方法

> `initTable` 是一个懒方法


#### 自旋
自旋时一种高效的线程等待机制，通常用于段时间的等待资源释放，而不是简单的进行线程的阻塞。

> 两种最基本的锁:
>
> - 互斥锁 : 如果线程获取锁失败了，就会进入阻塞状态，加入等待队列中，直到被唤醒，或者轮到了。
> - 自旋锁 : 如果线程获取锁失败了，就会进行自旋，不断的尝试获取锁，直到获取到为止。 -> 其实就是进入一个 while 循环，一直的尝试获取锁。

上面的两种锁都是其他锁的最基本的实现方式。各有优缺点，这里提到的自旋锁，其实有缺点都很明显，优点就是 免去了线程的阻塞和上下文切换带来的开销。但是因为要一直的忙等待，所以会消耗 CPU 的资源。因为上下文切换是会花费时间的，自旋锁通常用在 如果在等待锁的时间小于线程上下文切换的时间，那么使用自旋锁是比较合适的。 




#### CAS(Compare and Swap) 是什么 
CAS 是无锁并发机制，保证多个线程同时修改变量的时候，只有一个线程是成功的。是一个 **原子操作**。也就是说 CPU 是把两个操作合并为一个操作，
要么同时都执行。要么就同时都不执行。

> 举一个原子操作的简单例子:
> 如果要对一个共享变量进行自增操作，那么会有如下的步骤:
>
> - 读取 i 的当前值
> - 对 i 值进行加1的操作
> - 将 i 值写回内存






然后我们来看这个 `initTable` 方法

```java
/**
 * Initializes table, using the size recorded in sizeCtl.
 */
private final Node<K,V>[] initTable() {
    Node<K,V>[] tab; int sc;
    while ((tab = table) == null || tab.length == 0) {
        //　如果 sizeCtl < 0 ,说明另外的线程执行CAS 成功，正在进行初始化。
        if ((sc = sizeCtl) < 0)
            // 让出 CPU 使用权
            Thread.yield(); // lost initialization race; just spin
        else if (U.compareAndSwapInt(this, SIZECTL, sc, -1)) {
            try {
                if ((tab = table) == null || tab.length == 0) {
                    int n = (sc > 0) ? sc : DEFAULT_CAPACITY;
                    @SuppressWarnings("unchecked")
                    Node<K,V>[] nt = (Node<K,V>[])new Node<?,?>[n];
                    table = tab = nt;
                    sc = n - (n >>> 2);
                }
            } finally {
                sizeCtl = sc;
            }
            break;
        }
    }
    return tab;
}
```


`sizeCtl` 是一个用来控制哈希表初始化大小的一个共享变量。

- `sizeCtl`: 如果这个值大于0，表示初始化哈希表的大小
- `sizeCtl`: 如果这个值小于0，表示正在初始化哈希表


`sc` 是`sizeCtl` 的副本，用于保存 `sizeCtl` 的值。方便进行后续的比较和更新操作。


### `put`方法

`concurrentHashMap` 不仅在初始化的时候利用 CAS 保证了线程安全，在`put`方法中依旧也保证了线程安全。


```java
public V put(K key, V value) {
    return putVal(key, value, false);
}

/** Implementation for put and putIfAbsent */
final V putVal(K key, V value, boolean onlyIfAbsent) {
    // key 和 value 不能为空
    if (key == null || value == null) throw new NullPointerException();
    int hash = spread(key.hashCode());
    int binCount = 0;
    for (Node<K,V>[] tab = table;;) {
        // f = 目标位置元素
        Node<K,V> f; int n, i, fh;// fh 后面存放目标位置的元素 hash 值
        if (tab == null || (n = tab.length) == 0)
            // 数组桶为空，初始化数组桶（自旋+CAS)
            tab = initTable();
        else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
            // 桶内为空，CAS 放入，不加锁，成功了就直接 break 跳出
            if (casTabAt(tab, i, null,new Node<K,V>(hash, key, value, null)))
                break;  // no lock when adding to empty bin
        }
        else if ((fh = f.hash) == MOVED)
            tab = helpTransfer(tab, f);
        else {
            V oldVal = null;
            // 使用 synchronized 加锁加入节点
            synchronized (f) {
                if (tabAt(tab, i) == f) {
                    // 说明是链表
                    if (fh >= 0) {
                        binCount = 1;
                        // 循环加入新的或者覆盖节点
                        for (Node<K,V> e = f;; ++binCount) {
                            K ek;
                            if (e.hash == hash &&
                                ((ek = e.key) == key ||
                                 (ek != null && key.equals(ek)))) {
                                oldVal = e.val;
                                if (!onlyIfAbsent)
                                    e.val = value;
                                break;
                            }
                            Node<K,V> pred = e;
                            if ((e = e.next) == null) {
                                pred.next = new Node<K,V>(hash, key,
                                                          value, null);
                                break;
                            }
                        }
                    }
                    else if (f instanceof TreeBin) {
                        // 红黑树
                        Node<K,V> p;
                        binCount = 2;
                        if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                                       value)) != null) {
                            oldVal = p.val;
                            if (!onlyIfAbsent)
                                p.val = value;
                        }
                    }
                }
            }
            if (binCount != 0) {
                if (binCount >= TREEIFY_THRESHOLD)
                    treeifyBin(tab, i);
                if (oldVal != null)
                    return oldVal;
                break;
            }
        }
    }
    addCount(1L, binCount);
    return null;
}
```

<span style = "color : red">首先，有一点很重要的是,HashMap是允许存在 `key == null`的，因为它不考虑线程安全，更加侧重于KV值的存储。但是 concurrentHashMap是不允许的，如果允许 `null`的 key值，就会导致多线程访问的时候，无法判定是 null 还是 不存在了</span>

`onlyIfAbsent` 变量表示的是是否在不存在该键的时候才插入

`binCount` 用于记录的是桶内的链表或者树的元素个数。


在添加元素的时候，首先会判断容器内部是否为空。

- 如果为空，那么就使用 `volatile` 和 CAS 来进行初始化，可以有效的避免并发带来的问题
- 如果不为空，那么就根据存储的元素计算该位置是否为空
  - 如果为空，那么依旧利用 CAS 来设置该节点。因为我们要保证这个操作是原子操作，不会受到其他的线程的干扰。

  - 如果不为空,那么就使用 `Synchronized`，然后遍历桶中的数据，替换或者新增数据加入桶中，然后再判断是否需要红黑树。


在 `putVal` 方法中，乐观锁和悲观锁都有用到。因为我们也知道两种锁就是一种trade-off,当插入的元素的时候，桶中为空，也就是没有发生哈希冲突的时候，说明此时的并发压力较小，我们可以采用 `CAS` 乐观锁。但是如果在插入元素的时候，发生了哈希冲突，那么就说明此时的并发压力较大，我们通常会采取使用悲观锁。`Synchronized`


**这里提一个特殊情况:**

因为都说乐观锁基本上都是使用在默认发生并发冲突基本上不会发生的情况。那么假设此时 操作的线程 A 默认不会有并发发生，就进行了 CAS 操作，但是此时线程B 也同时进行了操作。那么 `CAS` 操作的返回值就为 `false`.会停止 `线程A` 的操作，并且 线程 B 就会进入悲观锁`Synchronized`的代码段。

对于并发的本质就是，多个线程在同一时间进行了对同一个共享变量进行了修改。从而难以保持一致性的情况。所以我们加锁的目的就是，让所有发生冲突的时候，只允许一个线程此刻对共享变量进行修改。其他线程只能进行等待的状态，无论是忙等待，还是加入等待队列... **最终目的就是保持一致性**