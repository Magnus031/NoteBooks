# <center>堆</center>

> 堆 也就是优先队列 
> 
>  - Max-Heap
>  - Min-Heap 

在 Java 的 `Collections` 中的 `Priority_Queue`  是定义的最小堆，如果想让它为最大堆，则需要传入重载过后的`Comparable`接口，而`Cpp`的 `stl`恰恰相反，它默认的是最大堆。所以我在写题的时候，一般就是看需要定义的是什么堆，而进行选择语言偷懒了 x

## 例题 
### 1 数组中的第k个最大的元素
<a href = "https://leetcode.cn/problems/kth-largest-element-in-an-array/description/?envType=company&envId=bytedance&favoriteSlug=bytedance-thirty-days">题目来源</a>

给定整数数组 `nums` 和整数 `k`，请返回数组中第 `k` 个最大的元素。
请注意，你需要找的是数组排序后的第 `k` 个最大的元素，而不是第 `k` 个不同的元素。

你必须设计并实现时间复杂度为 $O(n)$ 的算法解决此问题。

#### 题解
很简单的思路，我们既然要找第 k 大的元素，那么很自然的想法就是，它又是最大的`k`个元素中的最小值。**所以我们自然的想法是维护一个最小堆。**等遍历完所有的元素的时候，只需要知道最后的堆的最小值----那个就是数组中的第`K`个最大的元素。

#### Code 
```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        PriorityQueue <Integer> pq = new PriorityQueue<>();
        // Step1: we set a Min-Heap;
        for(int i=0;i<k;i++){
            pq.add(nums[i]);
        }
        // Step2: 
        int len = nums.length;
        for(int i = k;i<len;i++){
            int e = nums[i];
            int minE = pq.peek();
            if(minE<e){
                pq.poll();
                pq.add(e);
            }
        }
        return pq.peek();
    }
}
```
