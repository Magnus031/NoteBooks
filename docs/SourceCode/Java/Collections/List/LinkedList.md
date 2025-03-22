# <center>LinkedList</center>

> 现在来分析一下 `LinkedList` 的源码实现

因为 一个很尴尬的事实是 `LinkedList` 的作者都从来不会使用`LinkedList`.我们在项目中基本上都会使用`ArrayList`来进行操作。而我们绝大部分的操作都是读多写少，而 `LinkedList`的优势就是插入和删除的效率非常的高，所以我们在项目中很少使用`LinkedList`。


我就不多分析了，直接就看源码学了。



## 面试题
### Q1 介绍一下 `LinkedList` 的实现原理？以及它的常见操作的时间复杂度。

`LinkedList` 是一个双向链表来实现的。对于它来说，它跟 `ArrayList` 的区别就是在头尾进行插入和删除操作的时候，效率是高于 `ArrayList`的。但是在查询的时候，效率就比较低了。而且在指定位置的删除和插入操作的时候，效率也是比较低的。所以我们基本不会选择使用 `LinkedList`.


### Q2 为什么 `LinkedList` 不能实现`RandomAccess` 接口?

- 什么是 `RandomAccess` 标记接口？

    其实就是用来表明这个数据结构是否支持随机访问，通过索引来访问元素。有点类似于Cpp中的操作符重载。就是可以来随机访问指定元素。


    