# <center>回溯算法</center>

> 其实我觉得回溯的本质其实就是 DFS 遍历决策树，在遍历的过程中，根据题给条件进行剪枝即可。

### T1 小红的正整数

[题目链接](https://www.nowcoder.com/exam/test/86594095/detail?pid=47393601&testCallback=https%3A%2F%2Fwww.nowcoder.com%2Fexam%2Fcompany%3FquestionJobId%3D10%26subTabName%3Dwritten_page)

#### 题目描述
简单的讲，就是给你一个正整数`n`，你需要做的是找到最大的那个数，它的每一位的数字之和是 `n`,并且具有以下的两个特征:

- 不能重复
- 数字是 `1-9`

#### 题解
其实就很简单，我们的一个想法就是DFS直接回溯，我们把所有的排列都找出来，然后维护了一个 `value`，来和我们的全局变量`res`进行比较，当 `rest == 0` 的时候，就表明我们已经得到了一个结果了。接下来要做的就是及时的将 `value` 和 `res` 进行比较，然后更新 `res` 即可。


#### Code
```cpp
#include <iostream>
#include <bits/stdc++.h>
using namespace std;

class Solution {
  public:
    int getNumber() {
        // special case 
        if (n > 45)
            return -1;
        // initialization
        // visited.assign(10,false);
        res = -1;
        int value = 0;
        DFS(n,9,value);
        return res;
    }

    void DFS(int rest,int index, int value) {
        // we have travel for the last element;
        if(rest==0){
            if(value > res)
                res = value;
            return ;
        }

        if(index <=0)
            return ;
        
        if(index > rest)
            DFS(rest,index-1,value);

        DFS(rest,index-1,value);
        value = 10*value + index;
        DFS(rest-index,index-1,value);
        value /= 10;
    }

    int n;
  private:
    int res;
    // vector<bool> visited;
};


int main() {
    int n;
    cin >> n;

    // There is no the valid result;
    // The max is 987654321
    Solution solution;
    solution.n = n ;
    cout << solution.getNumber();


}
```


