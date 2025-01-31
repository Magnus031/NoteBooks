# <center>贪心算法</center>

## Introduce

贪心算法，字面意思就是选取当前状态下的最优解，也就是本地最优解`local optimal solution`
> 下面是 `wiki` 的定义:
> 
> A greedy algorithm is any algorithm that follows the problem-solving heuristic of making the locally optimal choice at each stage.[1] In many problems, a greedy strategy does not produce an optimal solution, but **a greedy heuristic can yield locally optimal solutions** that approximate a globally optimal solution in a reasonable amount of time.

> For example, a greedy strategy for the travelling salesman problem (which is of high computational complexity) is the following heuristic: "At each step of the journey, visit the nearest unvisited city." This heuristic does not intend to find the best solution, but it terminates in a reasonable number of steps; finding an optimal solution to such a complex problem typically requires unreasonably many steps. 


## 例题
### 1 跳跃游戏

<a href = "https://leetcode.cn/problems/jump-game/description/?envType=study-plan-v2&envId=top-100-liked"> 题目来源</a>

![P5](./assets/W1-T5.jpg)

#### 题解
我们这题主要是贪心算法。我们首先定义一个$distance$数组，他的作用是记录每一个位置$i$，所能跳到的最大位置。我们首先进行一遍遍历。然后按顺序依次遍历从该点开始到能跳跃的最大距离的过程中寻找下一个能跳跃的最大距离，依次往复，我们模拟跳跃的过程，这其中其实就是运用了贪心的算法，**选取当前状态中能跃迁的最大值进行跳跃。**

#### Code
```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        vector<int> distance;
        int len = nums.size();
        if(len==1)
            return true;
        for(int i =0;i<len;i++){
            // temp represents that the next boundary position that we can jump;
            distance.push_back(nums[i]+i);
        }
        int index = 0;
        for(int j=0;j<=len-2;){
            // The case that after we update the array, we get the destination, avoid ;
            if(distance[j]>=len-1)
                return true;
            int max = distance[j];
            for(int p = j+1;p<=distance[j];p++){
                if(distance[p]>max){
                    index = p;
                    max = distance[p];
                }
            }
            if(distance[j]==max)
                break;
            j = index;
        } 
        return false;
    }
};
```

### 2 跳跃游戏II

<a href = "https://leetcode.cn/problems/jump-game-ii/?envType=study-plan-v2&envId=top-100-liked">题目来源</a>
![P2](./assets/W1-T6.jpg)

#### 题解
这题就更简单了。在上一题的基础上加一个`count`作为记录跳跃次数即可。**over**


### 3 划分字母区间

<a href = "https://leetcode.cn/problems/partition-labels/description/?envType=study-plan-v2&envId=top-100-liked/">题目来源</a>
> 其实这题就是 合并最大连续区间的变式

![P3](./assets/W1-T7.jpg)

#### 题解

我们首先要明白这题的一个思路就是 **同一个字母最多出现在一个字符串内**，换句话说就是简单的理解为，不同的字符串段内不可能出现相同的字母。更简单的说法是，一种字母只可能出现在同一个字符串段内。所以我们这一题的贪心思路就是 **我们要找到保证最大的字符串段** 来包含某一字母。

那么思路就很简单了，我们的首要目标就是找到每个字母对应的区间。然后进行区间的合并。然后就没了。这题的区间合并我选择了使用`priority_queue`来进行合并。然后进行遍历，找到最大的区间即可。

#### Code
```cpp
class Solution {
public:
    vector<int> partitionLabels(string s) {
        set<char> charSet;
        map<char,pair<int,int>> mp;
        int len = s.length();
        for(int i=0;i<len;i++){
            char c = s[i];
            // find it;
            if(charSet.find(c)==charSet.end()){
                charSet.insert(c);
                mp.insert(make_pair(c,make_pair(i,i)));
            }else{
                mp[c].second = i;
            }
        }
        // combine the interval;
        priority_queue<pair<int,int>,vector<pair<int,int>>,greater<pair<int,int>>>pq;
        for(auto it:mp){
            pq.push(it.second);
        }
        // it stores for the intervals;
        vector<int> result;
        auto interval = pq.top();
        int a = 0;
        int b = interval.second;
        pq.pop();
        while(!pq.empty()){
            interval = pq.top();
            pq.pop();
            if(interval.first>=a&&interval.second<=b)
                continue;
            else if(interval.first<b&&interval.second>b){
                b = interval.second;
            }else if(interval.first==b+1){
                result.push_back(b-a+1);
                a = b+1;
                b = interval.second;
            }
        }
        result.push_back(b-a+1);
        return result;
    }
};
```


<style>
img {
  display: block;
  margin-left: auto;
  margin-right: auto;
  width : 80%;
  border-radius: 15px; /* 将图片设置为圆形 */
  
}
</style>