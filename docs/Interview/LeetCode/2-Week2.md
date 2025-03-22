# <center>2-week2</center>

这是2月的第二周的刷题记录，还是一样每天两题，marscode+leetcode


## 2月8日
### 题1 删除有序数组中的重复项

[题目链接](https://leetcode.cn/problems/remove-duplicates-from-sorted-array-ii/description/?envType=study-plan-v2&envId=top-interview-150)


#### 题解
这题很简单，主要就是利用一个 `map` 来进行维护已经遍历的变量的次数，如果超过2了，那么就直接在原数组中删除这个元素即可。值得注意的是，我们如果要在 `vector` 中直接删掉这个元素，我们可以利用迭代器的思想，直接在原数组中删除这个元素即可。`erase`函数的返回值是一个迭代器，指向删除元素之后的元素。


#### Code
```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        map<int,int> mp;
        for(auto it = nums.begin();it!=nums.end();){
            int temp = *it;
            if(mp.contains(temp)){
                if(mp[temp] == 2)
                    it = nums.erase(it);
                else if(mp[temp]==1){
                    mp[temp]++;
                    it++;
                }
            }else{
                mp.insert(make_pair(temp,1));
                it++;
            }
        }
        return nums.size();
    }
};
```

### 题2 打点计数器的区间合并

[题目链接](https://www.marscode.cn/practice/vkjvr8kynj72ek?problem_id=7414004855075618860)

#### 题解

这题其实也很简单，本质上就是区间的合并。对于区间合并，我选择了使用 `priority_queue` 来进行维护。因为我们需要维护一个区间的最大值和最小值，所以我们可以使用 `priority_queue` 来进行维护。这里我们使用了一个 `greater` 的比较器，这样我们就可以维护一个最小堆。这样我们就可以维护一个区间的最大值和最小值。然后我们就可以进行区间的合并了。而要获得最终的区间，其实也就是利用 `l` 和 `r` 来进行维护即可。直到遍历为`empty()` 的时候，也就得到了最终的结果。

#### Code
```cpp
#include <iostream>
#include <vector>
#include <bits/stdc++.h>
using namespace std;

int solution(std::vector<std::vector<int>> inputArray) {
    // Please write your code here
    std::priority_queue<
        std::pair<int, int>,                      // 元素类型
        std::vector< std::pair<int, int> >,       // 底层容器
        std::greater< std::pair<int, int> >       // 比较器
    > pq;

    for(auto &it:inputArray){
        int a = it[0];
        int b = it[1];
        pq.push(make_pair(a,b));
    }

    // vector<pair<int,int>> intervalSet;
    int l = pq.top().first;
    int r = pq.top().second;
    pq.pop();
    int count = 0;

    while(!pq.empty()){
        auto it = pq.top();
        pq.pop();

        int ln = it.first;
        int rn = it.second;

        if(l<=ln&&r>=rn){
            continue;
        }else if(r>=ln){
            r = rn;
        }else{
            //intervalSet.push_back(make_pair(l,r));
            count += r-l+1;
            l = ln;
            r = rn;
        }
    }

    count += r-l+1;

    return count;
}

int main() {
    //  You can add more test cases here
    std::vector<std::vector<int>> testArray1 = {{1, 4}, {7, 10}, {3, 5}};
    std::vector<std::vector<int>> testArray2 = {{1, 2}, {6, 10}, {11, 15}};

    std::cout << (solution(testArray1) == 9) << std::endl;
    std::cout << (solution(testArray2) == 12) << std::endl;

    return 0;
}
```

## 2月9日
### 题1 叠盘子排序 

[题目链接](https://www.marscode.cn/practice/vkjvr8kynj72ek?problem_id=7414004855075749932)


#### 题解
题目很简单，就不写题解了。主要就是利用一个 `vector` 来进行维护。然后遍历一遍即可。
#### Code
```Java
import java.util.ArrayList;

public class Main {
    public static String solution(int[] plates, int n) {
        // Please write your code here
        StringBuilder stringBuilder = new StringBuilder("");
        ArrayList<String> vectorStrings = new ArrayList<>();
        for(int i=0;i<n;i++){
            int start = plates[i];
            boolean flag = false;
            int ptr = 1;
            while(i+ptr<n && plates[i+ptr-1]+1==plates[i+ptr]){
                ptr++;
                if(ptr == 3)
                    flag = true;
            }

            if(flag == true){
                int end = plates[i+ptr-1];
                StringBuilder substring = new StringBuilder();
                substring.append(start).append("-").append(end);
                vectorStrings.add(substring.toString());            
                i = i + ptr - 1;
            }else{
                vectorStrings.add(Integer.toString(start));
            }
        }
        int len = vectorStrings.size();
        for(int i=0;i<len;i++){
            if(i == 0)
                stringBuilder.append(vectorStrings.get(i));
            else 
                stringBuilder.append(",").append(vectorStrings.get(i));
        }

        return stringBuilder.toString();
    }

    public static void main(String[] args) {
        //  You can add more test cases here
        System.out.println(solution(new int[]{-3, -2, -1, 2, 10, 15, 16, 18, 19, 20}, 10).equals("-3--1,2,10,15,16,18-20"));
        System.out.println(solution(new int[]{-6, -3, -2, -1, 0, 1, 3, 4, 5, 7, 8, 9, 10, 11, 14, 15, 17, 18, 19, 20}, 20).equals("-6,-3-1,3-5,7-11,14,15,17-20"));
        System.out.println(solution(new int[]{1, 2, 7, 8, 9, 10, 11, 19}, 8).equals("1,2,7-11,19"));
    }
}
```


## 2月10日

### 题1 股票市场交易策略优化
[题目链接](https://www.marscode.cn/practice/vkjvr8kynj72ek?problem_id=7414004855076012076)



#### 题解 DP 
这题的思路其实是开一个多状态的数组。不同以往的使用可能一维的数组，我们这里因为有三状态 : 

- 当日持有股票
- 当日不持有股票，但是处于冷静期
- 当日不持有股票，也不处于冷静期

所以我们这里开辟了一个数据 `dp[days][3]` 分别来表示`i` 天的时候，三种状态的最大利润，方便后面的状态转移方程的使用。我们可以得到状态转移方程：

我们进行逻辑的分析:

- 对于 `dp[i][0]` 表示的是第 `i` 天持有股票的最大利润，其实就是表示的是两种情况。有可能是前一天的股票继承到了今天，我们依旧没有抛售，也有可能是今天买入了股票，所以我们取两者的最大值。因为我们 `dp` 表示的是遍历多日以来的最大利润，所以我们选择 `Math.max(dp[i-1][0],dp[i-1][2]-stocks[i])`


- 对于 `dp[i][1]` 表示的是第 `i` 天下的当日不持有股票，而且处于冷静期，其实这个就属于比较简单的，因为我们只有一种情况，就是前一天持有股票，今天卖出了，所以我们可以得到 `dp[i][1] = dp[i-1][0] + stocks[i]`


- 对于 `dp[i][2]` 表示的是第 `i` 天不持有股票，也不处于冷静期，这个就是比较简单的，因为我们只有两种情况，一种是前一天就不持有股票，也不处于冷静期，另一种是前一天处于冷静期，今天不持有股票，所以我们可以得到 `dp[i][2] = Math.max(dp[i-1][2],dp[i-1][1])`


**这个冷静期我们就可以简单的理解为 股票售出日**，因为我们在抛售股票的过程中，都是要在下一次购买前，售出所有的股票，所以我们就仅仅考虑单日即可。

 
#### Code
```java
public class Main {
    public static int solution(int[] stocks) {
        // Please write your code here
        int days = stocks.length;
        int [][] dp = new int[days][3];
        // dp[i][0] 表示的是第i天结束持有股票的最大利润
        // dp[i][1] 表示的是第i天结束不持有股票且处于冷静期的最大利润
        // dp[i][2] 表示的是第i天结束不持有股票且不处于冷静期的最大利润;

        dp[0][0] = -stocks[0];
        dp[0][1] = 0;
        dp[0][2] = 0;

        for(int i=1;i<days;i++){
            dp[i][0] = Math.max(dp[i-1][0],dp[i-1][2]-stocks[i]);
            dp[i][1] = dp[i-1][0] + stocks[i];
            dp[i][2] = Math.max(dp[i-1][2],dp[i-1][1]);
        }

        return Math.max(dp[days-1][1],dp[days-1][2]);  
    }


    public static void main(String[] args) {
        // You can add more test cases here
        System.out.println(solution(new int[]{1, 2}) == 1);
        System.out.println(solution(new int[]{2, 1}) == 0);
        System.out.println(solution(new int[]{1, 2, 3, 0, 2}) == 3);
        System.out.println(solution(new int[]{2, 3, 4, 5, 6, 7}) == 5);
        System.out.println(solution(new int[]{1, 6, 2, 7, 13, 2, 8}) == 12);
    }
}
```


### 买卖股票的最佳时机 II 

[题目链接](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/description/?envType=study-plan-v2&envId=top-interview-150)

#### 题解
这题是比较简单的动态规划，相比上面这题，我们选择了两个状态的进行动态规划。我们就不再进行重复的分析了。

#### Code
```cpp
class Solution {
    public int maxProfit(int[] prices) {
        int days = prices.length;
        int[][] dp = new int[days][2];
        // dp[i][0] 表示的是第i天持有股票的最大利润
        // dp[i][0] 表示的是第i天不持有股票的最大利润 
        dp[0][0] = -prices[0];
        dp[0][1] = 0;

        for(int day = 1;day<days;day++){
            // 今天没买所以继承了前一天的持有股票的时候的最大值，或者昨天没买，我们今天买了
            dp[day][0] = Math.max(dp[day-1][0],dp[day-1][1]-prices[day]);
            dp[day][1] = Math.max(dp[day-1][0]+prices[day],dp[day-1][1]);
        }

        return dp[days-1][1];
    }
}
```


## 2月11日

### 题1 饭馆菜品选择问题

[题目链接](https://www.marscode.cn/practice/vkjvr8kynj72ek?problem_id=7424436653624754220)

#### 题解
这题就是一个很简单的贪心+优先队列的问题。我们为了让价格最低，那么我们每次都优先选择价格最低的菜品即可。这里我们使用了一个优先队列来进行维护。同时这个优先队列中的元素就是一个 `pair`，第一个元素是价格，第二个元素是这个菜品是否包含蘑菇。同时维护可以容忍的蘑菇数目`m` 和需要购买的菜品数`k`。然后进行维护，直到我们购买的菜品数目达到`k`为止。最终返回的就是我们的总价格，当然也有可能是`-1`就是不存在我们期望的方案。 

#### Code
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <bits/stdc++.h>
using namespace std;

long solution(const std::string& s, const std::vector<int>& a, int m, int k) {
    // PLEASE DO NOT MODIFY THE FUNCTION SIGNATURE
    // write code here
    int size = a.size();
    priority_queue<pair<int,int>,vector<pair<int,int>>,greater<pair<int,int>>> pq;
    for(int i=0;i<size;i++){
        auto it = make_pair(a[i],s[i]-'0');
        pq.push(it);
    }

    long sum  = 0 ;
    while(!pq.empty()){
        auto it = pq.top();
        pq.pop();

        if(it.second == 0){
            sum += it.first;
            k--;
        }else if(it.second == 1&&m>0){
            m--;
            sum += it.first;
            k--;
        }

        if(k==0){
            break;
        }
    }

    if(k==0)
        return sum;

    return -1;
}

int main() {
    std::cout << (solution("001", {10, 20, 30}, 1, 2) == 30) << std::endl;
    std::cout << (solution("111", {10, 20, 30}, 1, 2) == -1) << std::endl;
    std::cout << (solution("0101", {5, 15, 10, 20}, 2, 3) == 30) << std::endl;
    return 0;
}
```


## 2月12日

### 题1 最少字符串的操作次数
[题目链接](https://www.marscode.cn/practice/vkjvr8kynj72ek?problem_id=7424436653623017516)


#### 题解
这题比较简单，我们采取的是贪心的策略，每次都让个数最大的字符进行压缩操作，并且加到数量最少的字符中。其实也就是我开了两个堆，分别是最小堆和最大堆用来统计各个字符的次数。只要两个堆结合一下即可。

#### Code
```cpp
#include <iostream>
#include <string>
#include <bits/stdc++.h>
using namespace std;

int solution(const std::string& S) {
    // PLEASE DO NOT MODIFY THE FUNCTION SIGNATURE
    // write code here
    int words[26]={0};
    // 统计各个字母的数量;
    for(char c:S){
        words[c-'a']++;
    }
    int count = 0;
    priority_queue<pair<int,char>,vector<pair<int,char>>,greater<pair<int,char>>> pqMin;
    priority_queue<pair<int,char>,vector<pair<int,char>>,less<pair<int,char>>> pqMax;    
    for(int i=0;i<26;i++){
        char c = i+'a';
        pqMin.push(make_pair(words[i],c));
        pqMax.push(make_pair(words[i],c));
    }

    while(pqMax.top().first>=2){
        auto itMax = pqMax.top();
        pqMax.pop();
        auto itMin = pqMin.top();
        pqMin.pop();
        if(itMax.first <2){
            break;
        }
        pqMax.push(make_pair(itMax.first-2,itMax.second));
        pqMin.push(make_pair(itMax.first-2,itMax.second));
        pqMax.push(make_pair(itMin.first+1,itMin.second));
        pqMin.push(make_pair(itMin.first+1,itMin.second));
        count++;
    }
    return count;
}

int main() {
    std::cout << (solution("abab") == 2) << std::endl;
    std::cout << (solution("aaaa") == 2) << std::endl;
    std::cout << (solution("abcabc") == 3) << std::endl;
}
```

### 题2 统计二叉树中好节点的数目

[题目链接](https://leetcode.cn/problems/count-good-nodes-in-binary-tree/description/?envType=company&envId=microsoft&favoriteSlug=microsoft-thirty-days)


#### 题解
这题是比较简单的，我们只需要记录每次遍历到这个节点为止的最大节点值即可。然后我们就可以进行递归的遍历即可。

`travelTree(TreeNode* root,int max,int& count)`,`count`是一个全局变量，需要记录次数。

#### Code
```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    int goodNodes(TreeNode* root) {
        int max = root->val;
        int count = 0;
        travelTree(root,max,count);
        return count;
    }

private:
    void travelTree(TreeNode* root,int max,int &count){
        if(root == nullptr)
            return;
        
        if(root->val>=max){
            count++;
            max = root->val;
        }
        
        travelTree(root->left,max,count);

        travelTree(root->right,max,count);

    }
};
```