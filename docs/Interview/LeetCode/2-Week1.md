# <center>2-Week1</center>
> 以 2月1日 - 2月7日 为周期

由于参加了字节的青训营，所以这一个月需要每天坚持打卡1题+LeetCode1题，并且写题解.


**Summary:** 这周主要是在做有关树和回溯算法的题目。其实本质离不开 **DFS**, 然后自己应该也熟练起来了。


## 2月1日 
### 题 1 : 求矩形的最大面积

<a href = "https://www.marscode.cn/practice/vkjvr8kynj72ek?problem_id=7414004855077617708">题目链接</a>

#### 题解
简单的 DP 问题。我们设置了一个 `visited[][]` 二维数组，用来存放`i,j` 之间的最大高度,然后维护一个 `maxSquare` 最大面积变量。


#### Code
```java
public class Main {
    public static int solution(int n, int[] array) {
        // Edit your code here
        int [][] visited = new int[n][n];
        // visited[i][j] represents that from i to j highth;
        int maxSquare = 0;
        // initialization;
        for(int i=0;i<n;i++){
            visited[i][i] = array[i];
            if(array[i]>maxSquare)
                maxSquare = array[i];
        }
        // DP;
        for(int i=0;i<n;i++){
            for(int j=i+1;j<n;j++){
                visited[i][j] = Math.min(array[j],visited[i][j-1]);
                int tempSquare = (j-i+1) * visited[i][j];
                if(tempSquare > maxSquare)
                    maxSquare = tempSquare;
            }
        }
        return maxSquare;
    }

    public static void main(String[] args) {
        // Add your test cases here
        
        System.out.println(solution(5, new int[]{1, 2, 3, 4, 5}) == 9);
    }
}

```

### 题 2:
<a href = "https://leetcode.cn/problems/longest-valid-parentheses/description/?envType=company&envId=bytedance&favoriteSlug=bytedance-thirty-days">题目链接</a>

#### 题解
这题是需要我们进行求解最大的有效括号长度，我们就把它转化为最大的连续子区间长度问题。

1. 首先利用栈来记录下合法的括号索引对
2. 找到最大的连续子区间，并且维护一个 `maxLen` 变量即可

#### Code
```cpp
class Solution {
public:
    int longestValidParentheses(string s) {
        if(s.empty())
            return 0;
        int len = s.length();
        int maxLen = 0;
        stack<int> charStack;
        set<pair<int,int>> charSet;
        for(int i=0;i<len;i++){
            if(s[i] == '('){
                charStack.push(i);
            }else{
                if(charStack.empty()){
                    continue;
                }else{
                    int index = charStack.top();
                    charStack.pop();
                    charSet.insert(make_pair(index,i));
                }
            }
        }
        
        int prevL = -2;
        int prevR = -2;
        for(pair<int,int> p : charSet){
            int l = p.first;
            int r = p.second;

            int distance = r-l+1;

            if(l>prevL&&r<prevR)
                continue;
            else if(l == prevR+1){
                prevR = r;
                int temp = (r-prevL+1);
                if(temp > maxLen)
                    maxLen = temp;
            }else{
                prevL = l;
                prevR = r;
                maxLen = (maxLen < distance) ? distance : maxLen;
            }
        }


        return maxLen;
    }
};
```


## 2月2日
### 题1 最佳人选
<a href = "https://www.marscode.cn/practice/vkjvr8kynj72ek?problem_id=7414004855076962348">题目链接</a>

#### 题解
这题很简单，就是简单的遍历即可。我们需要注意的是，如果没有找到合适的人选，我们就返回`"None"`即可。这个是题目里没有说明的，但是测试用例中出现的了的，需要注意。


#### Code
```java

public class Main {

    public static String solution(int m, int n, String target, String[] array) {
        // Edit your code here
        int [] totalSum = new int[n];
        int min = 1000;
        for(int i = 0; i < n; i ++){
            int diff = PersonalityMatching(target, array[i]);
            if(diff == -1){
                totalSum[i] = -1;
            }else{
                if(diff < min){
                    min = diff;
                }
                totalSum[i] = diff;
            }
        }

        StringBuilder stringBuilder = new StringBuilder("");
        int count = 0;
        for(int i=0;i<n;i++){
            if(totalSum[i] == min){
                if(count ==0){
                    stringBuilder.append(array[i]);
                    count ++;
                }else{
                    stringBuilder.append(" ").append(array[i]);
            }
            }
        }
        if(count == 0)
            return "None";

        return stringBuilder.toString();
    }

    /**
     * Return the difference value of 2 personality;
     * @param l
     * @param r
     * @return
     */
    private static int PersonalityMatching(String l,String r){
        int len = l.length();
        int diff = 0;
        for(int i=0;i<len;i++){
            char lc = l.charAt(i);
            char rc = r.charAt(i);
            if(checkUnmatch(lc, rc)==false){
                diff += Math.abs(lc - rc);
            }else{
                return -1;
            }
        }
        return diff;
    }

    private static boolean checkUnmatch(char c1,char c2){
        if(c2 == 'E' && (c1=='A'||c1 =='B'||c1 =='C'))
            return true;
        else if(c1 == 'E' && (c2=='A'||c2 =='B'||c2 =='C'))
            return true;
        else if(c1 == 'B' && c2 == 'D')
            return true;
        else if(c2 == 'B' && c1 == 'D')
            return true;
        return false;
    }

    public static void main(String[] args) {
        // Add your test cases here
        String[] matrix = {
            "AAAAAA", "BBBBBB", "ABDDEB"
        };
        System.out.println(solution(6,3,"ABCDEA",matrix));
        System.out.println(solution(6, 3, "ABCDEA", matrix).equals("ABDDEB"));
    }
}

```


### 题2 二叉树展开为链表
<a href = "https://leetcode.cn/problems/flatten-binary-tree-to-linked-list/description/?envType=study-plan-v2&envId=top-interview-150">题目链接</a>


#### 题解
这题很简单，就是树的前序遍历，这里我用一个`queue`队列来记录遍历的顺序，然后按照顺序输出即可。

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
    void flatten(TreeNode* root) {  
        if(root == nullptr)
            return;
        queue<TreeNode*> q;
        traversalQueue(root,q);
        TreeNode* ptr = root;
        q.pop();
        while(!q.empty()){
            auto node = q.front();
            q.pop();
            ptr->right = node;
            ptr->left = nullptr;
            ptr = node;
        }
    }

private :
    void traversalQueue(TreeNode* node, queue<TreeNode*> &q){
        q.push(node);
        if(node->left!=nullptr)
            traversalQueue(node->left,q);
        if(node->right!=nullptr)
            traversalQueue(node->right,q);
        return ;
    }
};
```

## 2月3日
### 题1 游戏队友搜索


<a href = "https://www.marscode.cn/practice/vkjvr8kynj72ek?problem_id=7414004855077453868">题目链接</a>

#### 题解
这题没什么好讲的，我的思路就是记录下 **target id**的所有参与过的比赛场次，然后看有没有其他的队友参与过这个比赛场次即可。

#### Code
```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        // Add your test cases here
        System.out.println(
                Arrays.equals(
                        solution(1, 10,
                                new int[][] {
                                        { 1, 1 }, { 1, 2 }, { 1, 3 }, { 2, 1 }, { 2, 4 }, { 3, 2 },
                                        { 4, 1 }, { 4, 2 }, { 5, 2 }, { 5, 3 }
                                }),
                        new int[] { 4, 5 }));
    }

    public static int[] solution(int id, int num, int[][] array) {
        // Edit your code here
        int len = array.length;
        // we need to orders;
        Set<Integer> set = new TreeSet<>();
        Set<Integer> idSet = new HashSet<>();
        Map<Integer,Integer> mp = new HashMap<>();
        // Here we record all the pair of the id;
        for(int i=0;i<len;i++){
            if(array[i][0] == id){
                idSet.add(array[i][1]);
            }        
        }

        for(int i=0;i<len;i++){
             if(array[i][0]!=id && idSet.contains(array[i][1])){
                if(mp.containsKey(array[i][0])){
                     set.add(array[i][0]);       
                }else{
                        mp.put(array[i][0], 1);
                }
             }
        }

        int [] temp = new int[set.size()];
        int index = 0;
        for(Integer it : set){
           temp[index++] = it;
        }
        return temp;
    }
}

```

### 题2 把二叉搜索树转化为累加树

<a href = "https://leetcode.cn/problems/convert-bst-to-greater-tree/description/?envType=company&envId=bytedance&favoriteSlug=bytedance-thirty-days">题目链接</a>


#### 题解
这题我用两种思路来实现，虽然都是递归，一种是从最右边的节点开始逐步累加，还有一种思路就是用整个树的节点之和，减去左子树的节点之和即可。总的思路都是维护一个 `sum` 全局变量。

#### Code 1 
```cpp
class Solution {
public:
    TreeNode* convertBST(TreeNode* root) { 
        if(root == nullptr)
            return nullptr;
        int sum = sumBST(root);   
        travelBST(root, sum);
        return root;
    }

private:
    void travelBST(TreeNode* root,int &sum){
        if(root->left == nullptr && root->right == nullptr){
            int temp = root->val;
            root->val = sum;
            sum -= temp;
            return ; 
        }
        if(root->left!=nullptr){
            travelBST(root->left,sum);
        }
        int temp = root->val;
        root->val = sum;
        sum -= temp;
        if(root->right !=nullptr){
            travelBST(root->right,sum);
        }
        return ;
    }

    /**
     * Return the right sum of the node;
     */
    int sumBST(TreeNode* root){
        if(root->left == nullptr && root->right == nullptr)
            return root->val;
        int sum = root->val;
        if(root->right !=nullptr)
            sum += sumBST(root->right);
        if(root->left !=nullptr)
            sum += sumBST(root->left);
        return sum; 
    }
};
```

我这个解法是维护一个 `sum` 变量，然后递归的去遍历整个树，然后逐步累加即可。类似已经有一个所有节点的集合，然后在利用前序遍历的思路，逐步删去已经遍历的节点即可。但还是不够简洁，因为遍历了两次树。

#### Code 2
```cpp
class Solution {
public:
    TreeNode* convertBST(TreeNode* root) { 
        dfs(root);
        return root;
    }

private:
    int sum = 0;
    void dfs(TreeNode* root){
        if(root == nullptr)
            return ;
        if(root->right!=nullptr){
            dfs(root->right);
        }
        
        sum += root->val;
        root->val = sum;

        if(root->left!=nullptr){
            dfs(root->left);
        }
    }
};
```

这个思路就更简单了，而且只需要遍历一次树即可。我们要做的就是从最右边的最下面的节点开始累加，然后逐步向上累加即可。


## 2月4日
### 题1 二叉树供暖器
> 这题比较困难，困住我比较久,主要是自己一开始把问题想的过简单了

[题目链接](https://www.marscode.cn/practice/vkjvr8kynj72ek?problem_id=7414004855075815468) 

#### 题解
这题的思路主要是分为两部分:

- 建树，通过给的层序遍历的数组进行二叉树的建立
- 利用贪心来进行递归的供暖器的放置

##### 建树

首先是层序遍历来进行建树，我们使用 `BFS` 来模拟层序遍历，根据提供的数组来建立二叉树。只需要一个`index` 来作为数组的索引即可。然后一个队列来存放当前访问到的节点即可。我们都让默认的节点值为 `0`。因为后续节点值是有用的，会进行统一的设置。


##### 供暖器的放置

首先，我们要知道的是，供暖期的性质 **可以让自己的节点，自己的父节点，以及两个自己的子节点都能接收到供暖**。那么我们首先对 `val` 值进行设置

- `val == 0` 表示的是这个节点，我们进行了布置供暖器
- `val == 1` 表示的是这个节点，我们没有布置供暖器 , 但是供暖享受到了
- `val == 2` 表示的是这个节点，没有享受到供暖，也没有布置供暖器


然后我们要考虑到是最小的数目，其实就是一个贪心的思想。我们首先知道，一个节点如果安装了供暖，那么它可以让与自己链接有关的最直接的三层都能享受到供暖。那么我们就可以利用这个性质来进行递归的判断。首先，我们的想法就是 叶子 节点不提供供暖。反而让父节点来提供。如果让叶节点来提供供暖的话，那么我们就会牺牲了一层的供暖。所以我们的思路就是，从叶节点开始，逐步向上进行递归的判断。 **值得注意的是，我们这里选择后序遍历树，最后再判断 根节点**

接下来的就是简单的 case 判断:

- Step1: 调用 `postorderTree` 函数来获取 左右子树的供暖情况，如果是空的节点，我们都默认它们已经被供暖了。
- Step2:  

    - Case 1: `left == 1 && right == 1` 表示的是左右子树都被供暖了，但是没有供暖器。因为我们是从叶节点开始的遍历，所以这里就不可能是由父节点来提供的供暖，也就是说这两个节点享受到的供暖都是由自己的子节点来提供的。所以我们就设置 `root.val = 2`;表示的是这个节点没有被供暖。
    - Case 2: `left == 2 || right == 2` 表示的是左右子树有一个没有被供暖，那么我们就需要在这个节点上安装供暖器。所以我们就设置 `root.val = 0` 表示的是这个节点被供暖了。同时需要 `result++` 表示的是我们安装了一个供暖器。
    - Case 3: `left == 0 || right == 0` 表示的是左右子树有一个节点被供暖了，那么我们就不需要在这个节点上安装供暖器。所以我们就设置 `root.val = 1` 表示的是这个节点没有被供暖，但是享受到了供暖。

- Step3 : 返回值。我们在对 `postorderTree(root)` 的时候，也要最后判断一下我们的`root`的受到供暖的情况，如果是 `2` 的话，那么我们就需要安装一个供暖器。所以我们就需要 `result++` 表示的是我们安装了一个供暖器。 

其实逻辑是简单的，但是自己好像把问题想的另一种情况，导致了自己的思路不够清晰。

#### Code
```java
import java.util.LinkedList;
import java.util.Queue;
import java.util.Stack;

class Node{
        int val;
        Node left;
        Node right;
        public Node(int val){
            this.val = val;
            left = null;
            right = null;
        }
    }

public class Main {
    
    public static int solution(int[] nodes) {
        // Please write your code here
        result = 0;
        Node root = buildTree(nodes);

        if(postorderTree(root) == 2){
            result++;
        }
        return result;
    }

    static int result = 0;
    public static Node buildTree(int[] nodes){
        // Base case
        if(nodes == null || nodes[0] == 0)
            return null;

        Node root = new Node(0);
        Queue<Node> queue = new LinkedList<>();
        queue.offer(root);

        int i = 1;    
        int len = nodes.length;
        while(!queue.isEmpty()&& i < len){
            Node current = queue.peek();
            queue.poll();

            // we find the node has left node;
            if(i<len && nodes[i] == 1){
                current.left = new Node(0);
                queue.offer(current.left);
            }
            i++;

            // we find the node has right node;
            if(i<len&&nodes[i] ==1){
                current.right = new Node(0);
                queue.offer(current.right);
            }
            i++;
        }
        return root;
    }

    /**
     * 3 state:
     * 0 -> 已经安装上供暖;
     * 1 -> 蹭到了，未安装;
     * 2 -> 未供暖;
     * @param root
     * @return
     */
    public static int postorderTree(Node root){
        // The null node represents it has been offered warm;
        if(root == null)
            return 1;
        
        int left = postorderTree(root.left);

        int right = postorderTree(root.right);

        // it turns to the root;
        if(left == 1 && right == 1){
            return 2;
        }

        if(left == 2 || right == 2){
            result++;
            return 0;
        }

        if(left == 0 || right == 0 )
            return 1;

        return 1;
    }



    public static void main(String[] args) {
        //  You can add more test cases here
        int[] nodes1 = {1, 1, 0, 1, 1};
        int[] nodes2 = {1, 0, 1, 1, 0, 1, 0, 1, 0, 1, 0, 0, 1, 1};
        int[] nodes3 = {1, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0, 0, 1};

        System.out.println(solution(nodes1) == 1);
        System.out.println(solution(nodes2) == 3);
        System.out.println(solution(nodes3) == 3);
    }
}
```



## 2月5日 
### 题1 最少前缀操作问题

<a href = "https://www.marscode.cn/practice/vkjvr8kynj72ek?problem_id=7424418560931168300">题目链接</a>

#### 题解 
这题很简单，一开始想复杂了，以为需要动态规划来做，因为有一题比较经典的就是 `编辑距离` 问题。但是这题其实很简单，我们只需要两步 :

- 判断 `String S` 和`String T` 的长度大小，因为只有两种操作，一种是删除最后的字符，还有一种就是进行替换。
- 因为是要找到前缀操作最小的次数，我们进行的操作就是直接比较两个字符串的前缀，如果已经有对应位置相同的字符，那么就直接忽略不需要替换即可。然后这样统计字符个数即可。

#### Code
```java
public class Main {
    public static int solution(String S, String T) {
        // PLEASE DO NOT MODIFY THE FUNCTION SIGNATURE
        // write code here
        int len1 = S.length();
        int len2 =T.length();

        int count = 0;
        // we must do the operation
        if(len2 < len1){
            count += len1 - len2;
            len1 = len2;
        }

        for(int i=0;i<len1;i++){
            if(S.charAt(i)!=T.charAt(i)){
                count ++; 
            }
        }


        return count;
    }

    public static void main(String[] args) {
        System.out.println(solution("aba", "abb") == 1);
        System.out.println(solution("abcd", "efg") == 4);
        System.out.println(solution("xyz", "xy") == 1);
        System.out.println(solution("hello", "helloworld") == 0);
        System.out.println(solution("same", "same") == 0);
    }
}
```


### 题2 子集

[题目链接](https://leetcode.cn/problems/subsets/)

#### 题解
这题是老生常谈的经典回溯算法了。我们要做的是用 `DFS` 来遍历所有的可能性。所以我们可以想象一颗树，树的每个节点有两种选择，一种是选择当前遍历到的节点，另一种是不选择当前遍历到的节点。然后我们有一个 `index` 来记录的是下一个需要判断是否选择的节点即可。直到我们遍历完整棵树，也就是 `len == index` 的时候，我们就将已经遍历到的结果放入到结果集中即可。
#### cpp
```java
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        array = nums;    
        int index = 0;
        vector<int> turn;
        dfs(turn,index);
        return res;
    }

private:
    vector<vector<int>> res;
    vector<int> array;

    void dfs(vector<int> turn,int index){
        int len = array.size();
        // The case that turn-array is empty || we have over the traversal;
        if(len == 0 || index==len){
            res.push_back(turn);
            return ;
        }
        
        dfs(turn,index+1);
        turn.push_back(array[index]);
        dfs(turn,index+1);
        turn.pop_back();
    }

};
```


## 2月6日

### 题1 比较版本号

[题目链接](https://www.marscode.cn/practice/vkjvr8kynj72ek?problem_id=7414004855077421100) 

#### 题解
这题比较简单，主要就是 利用 `string.split` 的方法，把字符串进行分割成字符串数组。然后就利用`Integer.parseInt`


#### Code
```java

public class Main {
    public static void main(String[] args) {
        System.out.println(solution("0.1", "1.1") == -1);
        System.out.println(solution("1.0.1", "1") == 1);
        System.out.println(solution("7.5.2.4", "7.5.3") == -1);
        System.out.println(solution("1.0", "1.0.0") == 0);
    }

    public static int solution(String version1, String version2) {
        // Edit your code here
        String[] splitVersion1 = version1.split("\\.");
        String[] splitVersion2 = version2.split("\\.");
        int len1 = splitVersion1.length;
        int len2 = splitVersion2.length;
        int index = 0;
        while(index < len1 && index < len2){
            int s1 = Integer.parseInt(splitVersion1[index]);
            int s2 = Integer.parseInt(splitVersion2[index]);

            if(s1 > s2)
                return 1;
            else if(s1 < s2)
                return -1;
            index++;
        }

        if(len1 > len2){
            if(checkZero(splitVersion1,index))
                return 0;
            else return 1;
        }else if(len1 < len2){
            if(checkZero(splitVersion2,index))
                return 0;
            else 
                return -1;
        }

        return 0;
    }

    private static boolean checkZero(String[] parseArray,int index){
        int len = parseArray.length;
        for(int i = index;i<len;i++){
            int temp = Integer.parseInt(parseArray[i]);
            if(temp!=0)
                return false; 
        }
        return true;
    }
}

```

### 题2  完全二叉树的节点个数

[题目链接](https://leetcode.cn/problems/count-complete-tree-nodes/description/?envType=study-plan-v2&envId=top-interview-150)

#### 题解
这题是比较经典的 `DFS` 的题目，我们需要做的就是进行递归遍历然后得到结果即可。


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
    int countNodes(TreeNode* root) {
        return TraversalTree(root);
    }


private:
    int TraversalTree(TreeNode* root){
        if(root == nullptr)
            return 0;
        
        if(root->left == nullptr&&root->right == nullptr)
            return 1;
        
        return 1 + TraversalTree(root->left) + TraversalTree(root->right);
    }
};
```


## 2月7日

### 题1 贪心猫的鱼干大分配

[题目链接](https://www.marscode.cn/practice/vkjvr8kynj72ek?problem_id=7414004855075029036)

#### 题解

我们看这道题，其实就是找连续的递增序列和递减序列。我们在遍历整个数组，只需要不停的找到递减序列即可。其实也就是找到数组中的最低点，因为我们有一个条件就是 **相邻的鱼干凹槽，最小值** 我们把它设置为1。 

还有就是我们有一个要注意的是 **如果出现冲突了怎么办？也就是说当左边为递增序列，右边为递减序列，此时我们的鱼干峰值需要设置为 max。否则不能保证右边的谷值为1**

#### Code
```java
import java.util.ArrayList;
import java.util.List;

public class Main {
    public static int solution(int n, List<Integer> cats_levels) {
        // Please write your code here
        // base case:
        if(n==0)
            return 0;
        int[] visited = new int[n];
        int sum = 0;
        int index = 0;
        while(index<n){
            // find the next index of the non-increased of the intervals;
            int ptr = nextIncreasedIndex(index, cats_levels);
            if(ptr == index){
                int temp = 1;
                int i = 0;
                for(;index+i<n;i++){
                    visited[index+i] = temp; 
                    if(index+i+1 == n)
                        break;
                    if(cats_levels.get(index+i)==cats_levels.get(index+i+1))
                        temp = 1;
                    else if(cats_levels.get(index+i)<cats_levels.get(index+i+1))
                        temp++;
                    else 
                        break;
                }
                index += i;
            }else{
                int temp = 1;
                for(int p = ptr;p>=index;p--){
                    if(p == index){
                        visited[p] = Math.max(visited[p],temp);
                        continue;
                    }
                    visited[p] = temp;
                    temp ++;
                }
                index = ptr;
            }
            if(index == n-1)
                break;
        }

        for(int j=0;j<n;j++){
            sum += visited[j];
            System.out.print(visited[j]+" ");
        }
        System.out.println();
        return sum;
    }


    /**
     * get the right index of the non-increased intervals;
     * @param start
     * @param array
     * @return
     */
    private static int nextIncreasedIndex(int start,List<Integer>array){
        int ptr = 1;
        int n = array.size();
        while(start+ptr<n&&array.get(start+ptr)<array.get(start+ptr-1))
                ptr++;
        return start + ptr - 1;
    }

    public static void main(String[] args) {
        List<Integer> catsLevels1 = new ArrayList<>();
        catsLevels1.add(1);
        catsLevels1.add(2);
        catsLevels1.add(2);

        List<Integer> catsLevels2 = new ArrayList<>();
        catsLevels2.add(6);
        catsLevels2.add(5);
        catsLevels2.add(4);
        catsLevels2.add(3);
        catsLevels2.add(2);
        catsLevels2.add(16);

        List<Integer> catsLevels3 = new ArrayList<>();
        catsLevels3.add(2);
        catsLevels3.add(8);
        catsLevels3.add(7);
        catsLevels3.add(25);
        catsLevels3.add(1);
        catsLevels3.add(2);
        catsLevels3.add(1);
        catsLevels3.add(13);
        catsLevels3.add(22);
        catsLevels3.add(20);
        catsLevels3.add(10);
        catsLevels3.add(24);
        catsLevels3.add(11);
        catsLevels3.add(8);
        catsLevels3.add(20);
        catsLevels3.add(23);
        catsLevels3.add(9);
        catsLevels3.add(16);
        //catsLevels3.add(7);
        //catsLevels3.add(4);

        // System.out.println(solution(3, catsLevels1) == 4);
        // System.out.println(solution(6, catsLevels2) == 17);
        System.out.println(solution(18, catsLevels3) == 32);
    }
}
```

### 题2 恢复BST

[题目链接](https://leetcode.cn/problems/recover-binary-search-tree/description/)

#### 题解

这题其实很简单，就是字面的意思，很自然的想法就是先找出那个逆序对的位置，然后通过重新遍历树即可。

- 我们先遍历二叉树，得到存放节点 `val` 的数组，然后就可以找到对应的位置。因为我嫌麻烦了，我就直接重新开了一个数组，然后进行排序比对两个位置。当然其实也可以只在原来的数组上比对，但是我们需要更多的边界条件的判断，所以我就直接利用 `sort` 函数来进行比对了。

- 重新遍历二叉树，同时维护一个 `index` 来表示我们已经遍历到的位置，然后和我们出问题的位置进行比对更正，那么就得到了我们正常的二叉树了。



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
    void recoverTree(TreeNode* root) {
        vector<int> array;
        travelTree(root,array);
        int len = array.size();

        int a =0;
        int b =0;

        int count =0;

        vector<int> temp = array;
        sort(temp.begin(),temp.end());

        for(int i=0;i<len;i++){
            if(temp[i]!=array[i]){
                if(count == 0){
                    count++;
                    a = i;
                }else if(count == 1){
                    count ++;
                    b = i;
                }
            }
        }

        cout << count <<endl;

        

        cout << a <<" "<< b<<endl;

        int index = 0;        
        travelTree(root,array[a],a,array[b],b,index);
    }

private:
    void travelTree(TreeNode* root,vector<int> &array){
        if(root == nullptr)
            return ;
        
        travelTree(root->left, array);
        array.push_back(root->val);
        travelTree(root->right,array);
    }

    void travelTree(TreeNode* root,int a,int ia,int b,int ib,int &index){
        if(root == nullptr)
            return;
        
        travelTree(root->left,a,ia,b,ib,index);

        if(index == ia){
            root->val = b;
        }

        if(index == ib){
            root->val = a;
        }
        index ++;
        
        travelTree(root->right,a,ia,b,ib,index);
    }
};
```