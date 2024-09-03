​    



### 一、动态规划

1.数字三角形

> 给定一个 $r$ 行的数字三角形（$r \leq 1000$），需要找到一条从最高点到底部任意处结束的路径，使路径经过数字的和最大。每一步可以走到当前点左下方的点或右下方的点。

```plain
            7 
          3   8 
        8   1   0 
      2   7   4   4 
    4   5   2   6   5 
```

最大数字和：自下而上选择 左下 还是 右下 的数，直到顶部（同时更新二维数组）；

路径：自上而下，选择减去 左下 和 右下 中较大的，得到 所选择的经过路径中的数

```c++
#include<iostream>
#include<vector>

using namespace std;

//from the bottom to the top
int max_digital_triangle(vector<vector<int>>& triangle, int n){
    for(int i = n - 2; i >= 0; i--){
        for(int j = 0; j <= i; j++){
            triangle[i][j] += max(triangle[i + 1][j], triangle[i + 1][j + 1]);
        }
    }
    return triangle[0][0];
}

int main(){
    // int n;
    // cout << "Please input the number of rows of the triangle: ";
    // cin >> n;
    // vector<vector<int>> triangle(n, vector<int>(n, 0));
    // for(int i = 0; i < n; i++){
    //     for(int j = 0; j <= i; j++){
    //         cin >> triangle[i][j];
    //     }
    // }
    vector<vector<int>> triangle = {{7}, {3, 8}, {8, 1, 0}, {2, 7, 4, 4}, {4, 5, 2, 6, 5}};
    int n = triangle.size();
    int max_sum = max_digital_triangle(triangle, n);
    cout << "The maximum sum of the digital triangle is: " << max_sum << endl;
    cout << "The path is: " << endl;

    // print the path
    int x = max_sum;
    int j = 0;
    //from the top to the bottom
    for(int i = 0; i <= n-2; i++){
        if(triangle[i + 1][j] > triangle[i + 1][j + 1]){
            cout << x - triangle[i + 1][j] << " ";
            x = triangle[i + 1][j];
            j = j;
        }
        else{
            cout << x - triangle[i + 1][j + 1] << " ";
            x = triangle[i + 1][j+1];
            j = j + 1;
        }
    }
    cout << x << endl;

    return 0;
}
```

2. 最长公共子序列

   > 给定一个长度为 $n$ 的序列 $A$ 和一个 长度为 $m$ 的序列 $B$（$n,m \leq 5000$），求出一个最长的序列，使得该序列既是 $A$ 的子序列，也是 $B$ 的子序列。

   **子序列 $\neq$ 字串**（字符串$S$ 的 **子序列** 是从$S$ 中将若干元素提取出来并不改变相对位置形成的序列，字符串 $S$ 的 **子串**  $S[i..j]，i≤j$，表示 $S$ 串中从 $i$ 到 $j$ 这一段，也就是顺次排列 $S[i],S[i+1],\ldots,S[j]$ 形成的字符串)

   一个简要的例子：字符串 `abcde` 与字符串 `acde` 的公共子序列有 `a`、`c`、`d`、`e`、`ac`、`ad`、`ae`、`cd`、`ce`、`de`、`ade`、`ace`、`cde`、`acde`，最长公共子序列的长度是 4。

   设 $f(i,j)$ 表示只考虑 $A$ 的前 $i$ 个元素，$B$ 的前 $j$ 个元素时的最长公共子序列的长度，求这时的最长公共子序列的长度就是 **子问题**。$f(i,j)$ 就是我们所说的 **状态**，则 $f(n,m)$ 是最终要达到的状态，即为所求结果。

   对于每个 $f(i,j)$，存在三种决策：如果 $A_i=B_j$，则可以将它接到公共子序列的末尾；另外两种决策分别是跳过 $A_i$ 或者 $B_j$。状态转移方程如下：

   $$
   f(i,j)=\begin{cases}f(i-1,j-1)+1&A_i=B_j\\\max(f(i-1,j),f(i,j-1))&A_i\ne B_j\end{cases}
   $$

```c++
//最长公共子序列
#include <iostream>
#include <string>
#include <vector>
using namespace std;

int longest_common_subsequence(string s1, string s2){
    int n = s1.size();
    int m = s2.size();
    vector<vector<int>> dp(n+1, vector<int>(m+1, 0)); //初始化为全0
    //从1开始遍历
    for(int i = 1; i <= n; i++){
        for(int j = 1; j <= m; j++){
            if(s1[i-1] == s2[j-1]){
                dp[i][j] = dp[i-1][j-1] + 1;
            }
            else{
                dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
            }
        }
    }
    return dp[n][m];
}

int main(){
    string s1 = "abcde";
    string s2 = "accde";
    cout << longest_common_subsequence(s1, s2) << endl;
    return 0;
}
```

3. 最长不下降子序列

   > 给定一个长度为 𝑛 的序列 𝐴（$n \leq 5000$），求出一个最长的 𝐴 的子序列，满足该子序列的后一个元素不小于前一个元素。

设 $ f(i) $ 表示以 $A_i$ 为结尾的最长不下降子序列的长度，则所求为 $\max_{1 \leq i \leq n} f(i)$

计算 $f(i)$ 时，尝试**将 $A_i$ 接到其他的最长不下降子序列后面**，以更新答案。于是可以写出这样的状态转移方程：
$$
f(i)=\max_{1 \leq j < i, A_j \leq A_i} (f(j)+1)
$$


```c++
//最长不下降子序列
#include <iostream>
#include <vector>
using namespace std;

int longest_non_decreasing_subsequence(vector<int> nums){
    int n = nums.size();
  	int res = 0;
    vector<int> dp(n, 1); //初始化为全1
    for(int i = 0; i < n; i++){
        for(int j = 0; j < i; j++){
            if(nums[i] >= nums[j]){
                dp[i] = max(dp[i], dp[j] + 1);
            }
        }
      res = max(res, dp[i]);
    }
    return res;
}

int main(){
    vector<int> nums = {10, 9, 2, 5, 3, 7, 101, 18};
    cout << longest_non_decreasing_subsequence(nums) << endl;
    return 0;
}
```

4. [爬楼梯](https://leetcode.cn/problems/climbing-stairs/)

> 假设你正在爬楼梯。需要 `n` 阶你才能到达楼顶。
>
> 每次你可以爬 `1` 或 `2` 个台阶。你有多少种不同的方法可以爬到楼顶呢？

```c++
int climbStairs(int n) {
    vector<int> dp(n+1);
    dp[0] = 1;
    dp[1] = 1;
    for(int i = 2; i <=n; i++){
        dp[i] = dp[i-1] + dp[i-2];
    }
    return dp[n];
}
```

5. [不同路径](https://leetcode.cn/problems/unique-paths/)

> 一个机器人位于一个 `m x n` 网格的左上角,机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角。问总共有多少条不同的路径？

```c++
输入：m = 3, n = 7
输出：28
```

```c++
int uniquePaths(int m, int n) {
    vector<vector<int>> dp(m, vector<int>(n, 0));
    for(int j = 0; j < n; j++)
        dp[0][j] = 1;
    for(int i = 0; i < m; i++)
        dp[i][0] = 1;
    for(int i = 1; i < m; i++){
        for(int j = 1; j < n; j++){
            dp[i][j] = dp[i-1][j] + dp[i][j-1];
        }
    }
    return dp[m-1][n-1];
}
```

6. [不同路径II](https://leetcode.cn/problems/unique-paths-ii/)

> 一个机器人位于一个 `m x n` 网格的左上角 。机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角。现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？
>
> 网格中的障碍物和空位置分别用 `1` 和 `0` 来表示。

```c++
输入：obstacleGrid = [[0,0,0],[0,1,0],[0,0,0]]
输出：2
```

```c++
int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
    int m = obstacleGrid.size(), n = obstacleGrid[0].size();
    vector<vector<int>> dp(m, vector<int>(n,0));
    for(int i = 0; i < m && obstacleGrid[i][0] != 1; i++)
        dp[i][0] = 1;
    for(int j = 0; j < n && obstacleGrid[0][j] != 1; j++)
        dp[0][j] = 1;
    for(int i = 1; i < m; i++){
        for(int j = 1; j < n; j++){
            if(obstacleGrid[i][j] == 1) continue;
            dp[i][j] = dp[i-1][j] + dp[i][j-1];
        }
    }
    return dp[m-1][n-1];
}
```

7. [整数拆分](https://leetcode.cn/problems/integer-break/)

> 给定一个正整数 `n` ，将其拆分为 `k` 个 **正整数** 的和（ `k >= 2` ），并使这些整数的乘积最大化。
>
> 返回 *你可以获得的最大乘积* 。

```c++
输入: n = 10
输出: 36
解释: 10 = 3 + 3 + 4, 3 × 3 × 4 = 36。
```

```c++
int integerBreak(int n) {
    vector<int> dp(n+1);
    for(int i=2; i <= n; i++){
        for(int j = 1; j < i; j++){
            dp[i] = max(dp[i], max(j * (i-j), j * dp[i-j]));
        }
    }
    return dp[n];
}
```

8. [不同的二叉搜索树](https://leetcode.cn/problems/unique-binary-search-trees/)

> 给你一个整数 `n` ，求恰由 `n` 个节点组成且节点值从 `1` 到 `n` 互不相同的 **二叉搜索树** 有多少种？返回满足题意的二叉搜索树的种数。

1. $G(n)$: 长度为 $n$ 的序列能构成的不同二叉搜索树的个数。

2. $$F(i,n)$$: 以 $i$为根、序列长度为 $n$ 的不同二叉搜索树个数 (1≤*i*≤*n*)。

   $$G(n)=∑^{n}_{i=1}F(i,n)$$

​	$$F(i,n)=G(i−1)⋅G(n−i)$$
将公式 (1)，(2) 结合，可以得到 G(n) 的递归表达式：

​	$$G(n)= ∑_{i=1}^{n} G(i−1)⋅G(n−i)$$
至此，我们从小到大计算 G 函数即可，因为 G(n) 的值依赖于 G(0)⋯G(n−1)。

```c++
int numTrees(int n) {
    vector<int> dp(n+1, 0);
    dp[0] = 1;
    dp[1] = 1;
    for(int i = 2; i <= n; i++){
        for(int j = 1; j <= i; j++)
            dp[i] = dp[i] + dp[j - 1] * dp[i-j];
    }
    return dp[n];
}
```

### 二、贪心

1. [分发饼干](https://leetcode.cn/problems/assign-cookies/)

> 假设你是一位很棒的家长，想要给你的孩子们一些小饼干。但是，每个孩子最多只能给一块饼干。
>
> 对每个孩子 `i`，都有一个胃口值 `g[i]`，这是能让孩子们满足胃口的饼干的最小尺寸；并且每块饼干 `j`，都有一个尺寸 `s[j]` 。如果 `s[j] >= g[i]`，我们可以将这个饼干 `j` 分配给孩子 `i` ，这个孩子会得到满足。你的目标是尽可能满足越多数量的孩子，并输出这个最大数值。

```c++
输入: g = [1,2,3], s = [1,1]
输出: 1
解释: 
你有三个孩子和两块小饼干，3 个孩子的胃口值分别是：1,2,3。
虽然你有两块小饼干，由于他们的尺寸都是 1，你只能让胃口值是 1 的孩子满足。
所以你应该输出 1。
```

```c++
//对于每个元素 g[i]，找到未被使用的最小的 j 使得 g[i] ≤ s[j]，则 s[j] 可以满足 g[i]。由于 g 和 s 已经排好序，因此整个过程只需要对数组 g 和 s 各遍历一次。当两个数组之一遍历结束时，说明所有的孩子都被分配到了饼干，或者所有的饼干都已经被分配或被尝试分配（可能有些饼干无法分配给任何孩子），此时被分配到饼干的孩子数量即为可以满足的最多数量。

int findContentChildren(vector<int>& g, vector<int>& s) {
    int count = 0;
    sort(g.begin(), g.end());
    sort(s.begin(), s.end());
    for(int i = 0, j = 0; i < g.size() && j < s.size(); i++, j++){
        while(g[i] > s[j]){
            j++;
            if(j >= s.size()) return count;
        }
        count++;
    }
    return count;
}
```

2. [最大子数组和](https://leetcode.cn/problems/maximum-subarray/)

> 给你一个整数数组 `nums` ，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。**子数组**是数组中的一个连续部分。

```c++
输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为 6 。
```

```c++
int maxSubArray(vector<int>& nums) {
    int sum = nums[0], maxSum = nums[0];
    for(int i = 1; i < nums.size(); i++){
        sum = max(sum + nums[i], nums[i]);
        maxSum = max(maxSum, sum);
    }
    return maxSum;
}
```

3. [买卖股票的最佳时机II](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/)

> 给你一个整数数组 `prices` ，其中 `prices[i]` 表示某支股票第 `i` 天的价格。
>
> 在每一天，你可以决定是否购买和/或出售股票。你在任何时候 **最多** 只能持有 **一股** 股票。你也可以先购买，然后在 **同一天** 出售。
>
> 返回 *你能获得的 **最大** 利润* 。

```c++
输入：prices = [7,1,5,3,6,4]
输出：7
```

```c++
int maxProfit(vector<int>& prices) {
    int res = 0;
    for(int i = 1; i < prices.size(); i++){
        res = res + max(0, prices[i] - prices[i-1]);
    }
    return res;
}
```

4. [买卖股票的最佳时机 ](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/)

> 给定一个数组 `prices` ，它的第 `i` 个元素 `prices[i]` 表示一支给定股票第 `i` 天的价格。
>
> 你只能选择 **某一天** 买入这只股票，并选择在 **未来的某一个不同的日子** 卖出该股票。设计一个算法来计算你所能获取的最大利润。
>
> 返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 `0` 。

```c++
输入：[7,1,5,3,6,4]
输出：5
```

```c++
int maxProfit(vector<int>& prices) {
    int min_price = prices[0];
    int res = 0;
    for(int i = 1; i < prices.size(); i++){
        min_price = min(min_price, prices[i]);
        res = max(res, prices[i] - min_price);
    }
    return res;
}
```

5. [跳跃游戏](https://leetcode.cn/problems/jump-game/)

> 给你一个非负整数数组 `nums` ，你最初位于数组的 **第一个下标** 。数组中的每个元素代表你在该位置可以跳跃的最大长度。
>
> 判断你是否能够到达最后一个下标，如果可以，返回 `true` ；否则，返回 `false` 。

```c++
输入：nums = [2,3,1,1,4]
输出：true
解释：可以先跳 1 步，从下标 0 到达下标 1, 然后再从下标 1 跳 3 步到达最后一个下标。
```

```c++
bool canJump(vector<int>& nums) {
    int max_index = 0;
    for(int i = 0; i < nums.size() - 1; i++){  //其实最后一个nums[n-1]是用不到的
        if(i <= max_index){
            max_index = max(max_index, i + nums[i]);
        }
    }
    return max_index >= nums.size()-1 ;
}
```

6. [跳跃游戏II](https://leetcode.cn/problems/jump-game-ii/)

> 给定一个长度为 `n` 的 **0 索引**整数数组 `nums`。初始位置为 `nums[0]`。
>
> 每个元素 `nums[i]` 表示从索引 `i` 向前跳转的最大长度。换句话说，如果你在 `nums[i]` 处，你可以跳转到任意 `nums[i + j]` 处:
>
> - `0 <= j <= nums[i]` 
> - `i + j < n`
>
> 返回到达 `nums[n - 1]` 的最小跳跃次数。生成的测试用例可以到达 `nums[n - 1]`。

```c++
输入: nums = [2,3,1,1,4]
输出: 2
解释: 跳到最后一个位置的最小跳跃数是 2。
     从下标为 0 跳到下标为 1 的位置，跳 1 步，然后跳 3 步到达数组的最后一个位置
```

```c++
int jump(vector<int>& nums) {
    int step = 0;
    int end = 0, max_right = 0;
    for(int i = 0; i < nums.size()-1; i++){
        if(i <= max_right){
            max_right = max(max_right, i + nums[i]);
            if(i == end){
                end = max_right;
                step++;
            }
        }
    }
    return step;
}
```

7. [分发糖果](https://leetcode.cn/problems/candy/)

> `n` 个孩子站成一排。给你一个整数数组 `ratings` 表示每个孩子的评分。
>
> 你需要按照以下要求，给这些孩子分发糖果：
>
> - 每个孩子至少分配到 `1` 个糖果。
> - 相邻两个孩子评分更高的孩子会获得更多的糖果。
>
> 请你给每个孩子分发糖果，计算并返回需要准备的 **最少糖果数目** 。

```c++
输入：ratings = [1,0,2]
输出：5
解释：你可以分别给第一个、第二个、第三个孩子分发 2、1、2 颗糖果
  
  输入：ratings = [1,2,2]
输出：4
解释：你可以分别给第一个、第二个、第三个孩子分发 1、2、1 颗糖果。
     第三个孩子只得到 1 颗糖果，这满足题面中的两个条件。
```

```c++
int candy(vector<int>& ratings) {
    int sum = 0;
    vector<int> res(ratings.size(), 1);
    for(int i = 1; i < ratings.size(); i++){
        if(ratings[i] > ratings[i-1])
            res[i] = res[i-1] + 1;
    }
    for(int i = ratings.size() - 2; i >= 0; i--){
        if(ratings[i] > ratings[i + 1]){
            res[i] = max(res[i], res[i+1] + 1);
        }
    }
    for(auto it: res)
        sum += it;
    return sum;
}
```

8. [合并区间](https://leetcode.cn/problems/merge-intervals/)

> 以数组 `intervals` 表示若干个区间的集合，其中单个区间为 `intervals[i] = [starti, endi]` 。请你合并所有重叠的区间，并返回 *一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间* 。

```c++
输入：intervals = [[1,3],[2,6],[8,10],[15,18]]
输出：[[1,6],[8,10],[15,18]]
解释：区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].
```

```c++
vector<vector<int>> merge(vector<vector<int>>& intervals) {
    vector<vector<int>> res;
    int n = intervals.size();
    sort(intervals.begin(), intervals.end());
    for(int i = 0; i < n; i++){
        ////判断是否越界 两个数组是否有重合部分
        if(i < n - 1 && intervals[i][1] >= intervals[i+1][0]){
           //更新原数组
           //例：[1,2][2,3][5,6] => [1,2][1,3][5,6]
            intervals[i+1][0] = min(intervals[i][0],intervals[i+1][0]);
            intervals[i+1][1] = max(intervals[i][1],intervals[i+1][1]);
        }
        else{
            res.push_back(intervals[i]);
        }
    }
    return res;
}
```

9. [划分字母区间](https://leetcode.cn/problems/partition-labels/)

> 给你一个字符串 `s` 。我们要把这个字符串划分为尽可能多的片段，同一字母最多出现在一个片段中。
>
> 注意，划分结果需要满足：将所有划分结果按顺序连接，得到的字符串仍然是 `s` 。
>
> 返回一个表示每个字符串片段的长度的列表。

```c++
输入：s = "ababcbacadefegdehijhklij"
输出：[9,7,8]
解释：
划分结果为 "ababcbaca"、"defegde"、"hijhklij" 。每个字母最多出现在一个片段中。
像 "ababcbacadefegde", "hijhklij" 这样的划分是错误的，因为划分的片段数较少。
```

```c++
vector<int> partitionLabels(string s) {
    vector<int> res;
    vector<int> last_index(27, 0);  //记录每个字母最后出现的位置
    for(int i = 0; i < s.size(); i++){
        last_index[s[i] - 'a'] = i;
    }
    int start = 0, end = 0;
    for(int i = 0; i < s.size(); i++){
        end = max(end, last_index[s[i] - 'a']);  //迭代更新组内最后位置
        if(i == end){
            res.push_back(end - start + 1);
            start = end + 1;              //新的一组起始位置
        }
    }
    return res;
}
```

10. [监控二叉树](https://leetcode.cn/problems/binary-tree-cameras/)

> 给定一个二叉树，我们在树的节点上安装摄像头。
>
> 节点上的每个摄影头都可以监视**其父对象、自身及其直接子对象。**
>
> 计算监控树的所有节点所需的最小摄像头数量。

```c++
输入：[0,0,null,0,0]
输出：1
解释：如图所示，一台摄像头足以监控所有节点
```

class Solution {
public:
    int dfs(TreeNode* root, int& count){
        if(root == nullptr)
            return 0;
        int left = dfs(root->left, count);
        int right = dfs(root->right, count);
/*

* return 0; 此节点无监控，无需监控
* return 1; 此节点无监控，需监控
* return 2; 此节有监控
*/
        if(left == 0 && right == 0)
            return 1;
        if(left == 1 || right == 1){
            count++;
            return 2;
        }

  ​      return 0;
    }
    int minCameraCover(TreeNode* root) {
  ​      int count = 0;
  ​      return dfs(root, count) == 1 ? count + 1 : count;
    }
};

11. [无重叠区间](https://leetcode.cn/problems/non-overlapping-intervals/)

> 给定一个区间的集合 `intervals` ，其中 `intervals[i] = [starti, endi]` 。返回 *需要移除区间的最小数量，使剩余区间互不重叠* 。

```
输入: intervals = [[1,2],[2,3],[3,4],[1,3]]
输出: 1
解释: 移除 [1,3] 后，剩下的区间没有重叠。
```

```c++
class Solution {
public:
    static bool cmp(vector<int>& a, vector<int>& b){          //记得添加static 
        return a[1] < b[1];
    }
    int eraseOverlapIntervals(vector<vector<int>> intervals) {
        int count = 1;           //记录一定需要的区间
        sort(intervals.begin(), intervals.end(), cmp);     //按照结束时间排序
        int end = intervals[0][1];
        for(int i = 1; i < intervals.size(); i++){
            if(intervals[i][0] >= end){       //一定出现了新的区间
                count++;
                end = intervals[i][1];
            }
        }
        return intervals.size() - count;     
    }
};
```



### 三、队列&&栈

1. 逆波兰表达式

> 给你一个字符串数组 `tokens` ，表示一个根据 [逆波兰表示法](https://baike.baidu.com/item/逆波兰式/128437) 表示的算术表达式。请你计算该表达式。返回一个表示表达式值的整数。
>
> ```yaml
> 输入：tokens = ["4","13","5","/","+"]
> 输出：6
> 解释：该算式转化为常见的中缀算术表达式为：(4 + (13 / 5)) = 6
> ```

注意⚠️：

- 运算符左右的算数是有位置先后的，注意；
- c++中，字符串转整数：stoi(string) ; 整数转字符串：itos(int)

```c++
int evalRPN(vector<string>& tokens) {
    stack<int> st;
    for(int i = 0; i < tokens.size(); i++){
        if(tokens[i] != "+" && tokens[i] != "-" &&
             tokens[i] != "*" && tokens[i] != "/")
        {
            st.push(stoi(tokens[i]));
        }
        else{
            int a = st.top(); st.pop();
            int b = st.top(); st.pop(); //b在前，a在后ß
            if(tokens[i] == "+")
                st.push(b + a);
            if(tokens[i] == "-")
                st.push(b - a);
            if(tokens[i] == "*")
                st.push(b * a);
            if(tokens[i] == "/")
                st.push(b / a);
        }
    }
    return st.top();
}
```

2. 删除字符串中所有相邻重复项
>给出由小写字母组成的字符串 `S`，**重复项删除操作**会选择两个相邻且相同的字母，并删除它们。在 S 上反复执行重复项删除操作，直到无法继续删除。在完成所有重复项删除操作后返回最终的字符串。答案保证唯一。
>
>```c++
>输入："abbaca"
>输出："ca"
>```


```c++
string removeDuplicates(string s) {
    stack<char> st;
    string s1;
    for(int i = 0; i < s.length(); i++){
        if(!st.empty() && s[i] == st.top()){ //访问栈顶元素前需要判空！！！
            st.pop();
        }
        else{
            st.push(s[i]);
        }
    }
    while(!st.empty()){
        s1.push_back(st.top());
        st.pop();
    }
    reverse(s1.begin(),s1.end());
    return s1;
}
```

3. [滑动窗口最大值](https://leetcode.cn/problems/sliding-window-maximum/)
>给你一个整数数组 nums，有一个大小为 k 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 k 个数字。滑动窗口每次只向右移动一位。返回 滑动窗口中的最大值 。
>
>```c++
>输入：nums = [1,3,-1,-3,5,3,6,7], k = 3
>输出：[3,3,5,5,6,7]
>解释：
>滑动窗口的位置                最大值
>---------------               -----
>[1  3  -1] -3  5  3  6  7       3
> 1 [3  -1  -3] 5  3  6  7       3
> 1  3 [-1  -3  5] 3  6  7       5
> 1  3  -1 [-3  5  3] 6  7       5
> 1  3  -1  -3 [5  3  6] 7       6
> 1  3  -1  -3  5 [3  6  7]      7
>```
>
>使用大根堆，逐步向大根堆中增添新的数，使用下标来判断目前堆顶（最大值）是否在当前滑动窗口中。

```c++
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    int n = nums.size();
    vector<int> res;  //如果是res(n-k+1),这样会初始化，后面就不应该使用push_back(),使用下标
    priority_queue<pair<int, int>> q;
    for(int i = 0; i < k; i++){
        q.push({nums[i], i});   //添加pair类型，使用{first, second}
    }
    res.push_back(q.top().first); //别忘了first/second,
    for(int i = k; i< n; i++){
        q.push({nums[i], i});
        while(q.top().second <= i - k){    //需要循环！不只是if，清理优先队列中过期元素
            q.pop();
        }
        res.push_back(q.top().first);
    }
    return res;
}
```

4. [前K个高频元素](https://leetcode.cn/problems/top-k-frequent-elements/)

   > 给你一个整数数组 `nums` 和一个整数 `k` ，请你返回其中出现频率前 `k` 高的元素。你可以按 **任意顺序** 返回答案。
   >
   > ```c++
   > 输入: nums = [1,1,1,2,2,3], k = 2
   > 输出: [1,2]
   > ```

```c++
#include <iostream>
#include <vector>
#include <unordered_map>
#include <queue>

using namespace std;

vector<int> topKFrequent(vector<int>& nums, int k) {
    // 1. 使用 unordered_map 统计每个数字的出现频率
    unordered_map<int, int> frequencyMap;
    for(int num : nums) {
        frequencyMap[num]++;
    }

    // 2. 使用优先队列（最大堆）按频率排序
    priority_queue<pair<int, int>> q; // 默认按 first 降序排列

  	//需要交换顺序，频率放在第一位作为排序指标
    for(auto& it : frequencyMap) {
        q.push({it.second, it.first}); // 将 {频率, 数字} 放入队列中
    }

    // 3. 取出前 k 个频率最高的元素
    vector<int> res;
    for(int i = 0; i < k; ++i) {
        res.push_back(q.top().second); // q.top().second 是频率最高的数字
        q.pop();
    }
    return res;
}

int main() {
    vector<int> nums = {1, 1, 1, 2, 2, 3};
    int k = 2;
    vector<int> result = topKFrequent(nums, k);
    for (int num : result) {
        cout << num << " ";
    }
    return 0;
}
```

### 四、数组

1. 二分查找

   > 给定一个 n 个元素有序的（升序）整型数组 nums 和一个目标值 target  ，写一个函数搜索 nums 中的 target，如果目标值存在返回下标，否则返回 -1。
   >
   > ```c++
   > 输入: nums = [-1,0,3,5,9,12], target = 9     
   > 输出: 4     
   > ```
   >

```c++
int search(vector<int>& nums, int target) {
    int left = 0, right = nums.size() - 1;
    
    while(left <= right){	 	//循环条件
        int mid = left + (right - left) / 2;
        if(nums[mid] == target)
            return mid;
        else if(nums[mid] < target)
            left = mid+1;
        else
            right = mid - 1;
    }
    return -1;
}
```

2. [移除元素](https://leetcode.cn/problems/remove-element/)

   > 给你一个数组 `nums` 和一个值 `val`，你需要 **[原地](https://baike.baidu.com/item/原地算法)** 移除所有数值等于 `val` 的元素。元素的顺序可能发生改变。然后返回 `nums` 中与 `val` 不同的元素的数量。
   >
   > ```c++
   > 输入：nums = [0,1,2,2,3,0,4,2], val = 2
   > 输出：5, nums = [0,1,4,0,3,_,_,_]
   > ```
   >

```c++
int removeElement(vector<int>& nums, int val) {
    int left = 0, right = nums.size() - 1;
    while(left <= right){         //是可以等于的
        if(nums[left] == val){    //这种情况下，left是不动
            nums[left] = nums[right];
            right--;
        }
        else
            left++;  //只有nums[left] != val 的时候，才需要移动
    }
    return left;
}
```

3. [有序数组的平方](https://leetcode.cn/problems/squares-of-a-sorted-array/)

> 给你一个按 **非递减顺序** 排序的整数数组 `nums`，返回 **每个数字的平方** 组成的新数组，要求也按 **非递减顺序** 排序。
>
> ```c++
> 输入：nums = [-4,-1,0,3,10]
> 输出：[0,1,9,16,100]
> ```
>
> ⚠️：因为有正负，小值在中间，因此适合从两边找最大值进行排序，再逆转。

```c++
    vector<int> sortedSquares(vector<int>& nums) {
        vector<int> res;
        int left = 0, right = nums.size()-1;
        while(left<=right){
            if(abs(nums[right]) > abs(nums[left])){
                res.push_back(nums[right] * nums[right]);
                right--;
            }
            else{                
                res.push_back(nums[left] * nums[left]);
                left++;
            }
        }
        reverse(res.begin(), res.end());
        return res;
    }
```

4. [长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/)

> 给定一个含有 `n` 个正整数的数组和一个正整数 `target` **。**找出该数组中满足其**总和大于等于 `target`** 的长度最小的 **子数组**`[numsl, numsl+1, ..., numsr-1, numsr]` ，并返回其长度**。**如果不存在符合条件的子数组，返回 `0` 。
>
> ```c++
> 输入：target = 7, nums = [2,3,1,2,4,3]
> 输出：2
> 
> 输入：target = 11, nums = [1,1,1,1,1,1,1,1]
> 输出：0
> ```

⚠️：**滑动窗口**：定义两个指针 left 和  right 分别表示子数组（滑动窗口窗口）的开始位置和结束位置，维护**变量 sum 存储子数组中的元素和**（即从 nums[left] 到 nums[right] 的元素和）。
初始状态下，left 和 right 都指向下标 0，sum 的值为 0。
每一轮迭代，将 nums[right] 加到 sum，**如果 sum≥s，则更新子数组的最小长度**（此时子数组的长度是 right−left+1），然后将 nums[left] 从 sum 中减去并将 left 右移，直到 sum<s，在此过程中同样更新子数组的最小长度。在每一轮迭代的最后，将 right 右移。

```c++
int minSubArrayLen(int target, vector<int>& nums) {
    int res = INT_MAX;
    int left = 0, right = 0;
    int n = nums.size();
    int sum = 0;
    while(right < n){
        sum = sum + nums[right];
        while(sum >= target){
            res = min(res, right - left + 1);
            sum = sum - nums[left];
            left++;
        }
        right++;
    }
    return res == INT_MAX ? 0 : res;
}
```

5. [螺旋矩阵II](https://leetcode.cn/problems/spiral-matrix-ii/)

> 给你一个正整数 `n` ，生成一个包含 `1` 到 `n2` 所有元素，且元素按顺时针顺序螺旋排列的 `n x n` 正方形矩阵 `matrix` 。
>
> ```c++
> 输入：n = 3
> 输出：[
>   	 		[1,2,3],
>     		[8,9,4],
>   			[7,6,5]
> 		 ]
> ```

```c++
   vector<vector<int>> generateMatrix(int n) {
       vector<vector<int>> res(n, vector<int>(n));
       int num = 1;
       int left = 0, right = n-1, top = 0, bottom = n - 1;
       while(left <= right && top <= bottom){
           for(int j = left; j <= right; j++){ //上
               res[top][j] = num;
               num++;
           }
           for(int i = top + 1; i <= bottom; i++){  //右
               res[i][right] = num;
               num++;
           }
           for(int j = right - 1; j >= left; j--){  //下
               res[bottom][j] = num;
               num++;
           }
           for(int i = bottom - 1; i > top; i--){  //左，注意边界
               res[i][left] = num;
               num++;
           }
           left++;
           right--;
           top++;
           bottom--;
       }
       return res;
   }
```

6. 区间和

> 给定一个整数数组 Array，请计算该数组在每个指定区间内元素的总和。
>
> 输入示例
>
> ```c++
> 5
> 1
> 2
> 3
> 4
> 5
> 0 1
> 1 3
> ```
>
> 输出示例
>
> ```c++
> 3
> 9
> ```

```c++
int main(){
    int sum = 0;
    int n, x, a, b;
    cin >> n;
    vector<int> presum(n);
    // 填充前缀和数组
    for(int i = 0; i < n; i++){
        cin >> x; 
        sum += x;
        presum[i] = sum;
    }
    // 循环查询，直到输入的索引非法为止
    while(cin >> a >> b){
        if(a == 0)  //注意a=0情况
            cout << presum[b]; 
        else
            cout << presum[b] - presum[a - 1]; 
    }
    return 0;
}
```



2. 排序

   - 快速排序
   - 堆排序

### 五、链表

1. [移除链表元素](https://leetcode.cn/problems/remove-linked-list-elements/)

> 给你一个链表的头节点 `head` 和一个整数 `val` ，请你删除链表中所有满足 `Node.val == val` 的节点，并返回 **新的头节点** 。
>
> ```c++
> 输入：head = [1,2,6,3,4,5,6], val = 6
> 输出：[1,2,3,4,5]
> ```

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        if(head == nullptr) 
            return head;
        ListNode *dummy = new ListNode(0, head);
        ListNode *pre = dummy;
        while(head){
            if(head->val == val){
                pre->next = head->next;
            }
            else{
                pre = pre->next;
            }
            head = head->next;
        }
        return dummy->next;
    }
};
```

2. [翻转链表](https://leetcode.cn/problems/reverse-linked-list/)

> 给你单链表的头节点 `head` ，请你反转链表，并返回反转后的链表。
>
> ```c++
> 输入：head = [1,2,3,4,5]
> 输出：[5,4,3,2,1]
> ```
>

```c++
struct ListNode{
  int val;
  ListNode* next;
  ListNode(): val(0), next(nullptr){}
  ListNode(int x): val(x), next(nullptr){}
  ListNode(int x, ListNode *next): vaa(x), next(next){}
}

ListNode* reverseList(ListNode* head) {
     if(head == nullptr || head->next == nullptr)
         return head;
     ListNode *pre = nullptr;
     ListNode *curr = head;
     while(curr){
         ListNode *nextnode = curr->next;
         curr->next = pre;
         pre = curr;
         curr = nextnode;
     }
     return pre;
}
```

3. 两两交换链表中的节点

> 给你一个链表，两两交换其中相邻的节点，并返回交换后链表的头节点。你必须在不修改节点内部的值的情况下完成本题（即，只能进行节点交换）。

```c++
输入：head = [1,2,3,4]
输出：[2,1,4,3]
```

```c++
ListNode* swapPairs(ListNode* head) {
    if(head == nullptr || head->next == nullptr) 
        return head;
    ListNode *first = head;
    ListNode *second = head->next;
    ListNode *third = second->next;
    second->next = first;
    first->next = swapPairs(thir
    return second;
}
```

4. [删除链表的倒数第N个结点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)

> 给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。

```c++
输入：head = [1,2,3,4,5], n = 2
输出：[1,2,3,5]
```

```c++
ListNode* removeNthFromEnd(ListNode* head, int n) {
    ListNode *dummy = new ListNode(0, head);
    ListNode *fast = dummy;
    ListNode *slow = dummy;
    for(int i = 0; i < n; i++)
        fast = fast->next;
    while(fast->next){ //注意位置
        fast = fast->next;
        slow = slow->next;
    }
    slow->next = slow->next->next;
    return dummy->next;
}
```

5. [链表🍌](https://leetcode.cn/problems/intersection-of-two-linked-lists-lcci/)

> 给你两个单链表的头节点 `headA` 和 `headB` ，请你找出并返回两个单链表相交的**起始节点**。如果两个链表没有交点，返回 `null` 。题目数据 **保证** 整个链式结构中不存在环。
>
> **注意**，函数返回结果后，链表必须 **保持其原始结构** 。

```c++
输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,0,1,8,4,5], skipA = 2, skipB = 3
输出：Intersected at '8'(输出结点)
解释：相交节点的值为 8 （注意，如果两个链表相交则不能为 0）。
```

```c++
/*
如链表A+链表B=链表C1
链表B+链表A=链表C2
A -> a1 a2 c1 c2 c3
B -> b1 b2 b3 c1 c2 c3

C1 -> a1 a2 c1 c2 c3 b1 b2 b3 c1 c2 c3
C2 -> b1 b2 b3 c1 c2 c3 a1 a2 c1 c2 c3
此时C1和C2的长度一定相同。 而C1和C2的结尾就一定是相交的链表
*/
ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
    if(headA == nullptr || headB == nullptr)
        return nullptr;
    ListNode *ha = headA;  //不能改变链表的原始结构
    ListNode *hb = headB;
    while(ha != hb){
        ha = (ha == nullptr) ? headB : ha->next;
        hb = (hb == nullptr) ? headA : hb->next;
    }
    return ha;
}
```

```c++
//将A链结点放入集合再遍历B链 查找比较
ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
    unordered_set<ListNode *> visited;  //集合unordered_set<ListNode *>
    ListNode *temp = headA;
    while(temp != nullptr){
        visited.insert(temp);  //insert,集合插入元素
        temp = temp->next;
    }
    temp = headB;
    while(temp != nullptr){
        if(visited.count(temp))  //count() 集合查找元素
            return temp;
        temp = temp->next;
    }
    return nullptr;
}
```

6. [环形链表](https://leetcode.cn/problems/linked-list-cycle-ii/)

> 给定一个链表的头节点  `head` ，返回链表开始入环的第一个节点。 *如果链表无环，则返回 `null`。*

```c++
输入：head = [3,2,0,-4], pos = 1
输出：返回索引为 1 的链表节点
解释：链表中有一个环，其尾部连接到第二个节点。
```

```c++
ListNode *detectCycle(ListNode *head) {
    unordered_set<ListNode *> visited;
    ListNode *temp = head;
    while(temp){
        if(visited.count(temp)){
            return temp;
        }
        visited.insert(temp);
        temp = temp->next;
    }
    return nullptr;
}
```

```c++
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        ListNode* fast = head;
        ListNode* slow = head;
        while(fast != NULL && fast->next != NULL) {
            slow = slow->next;
            fast = fast->next->next;
            // 快慢指针相遇，此时从head 和 相遇点，同时查找直至相遇
            if (slow == fast) {
                ListNode* index1 = fast;
                ListNode* index2 = head;
                while (index1 != index2) {
                    index1 = index1->next;
                    index2 = index2->next;
                }
                return index2; // 返回环的入口
            }
        }
        return NULL;
    }
};

```

### 六、哈希表

1. [快乐数](https://leetcode.cn/problems/happy-number/)

> **快乐数」** 定义为：
>
> - 对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和。
> - 然后重复这个过程直到这个数变为 1，也可能是 **无限循环** 但始终变不到 1。
> - 如果这个过程 **结果为** 1，那么这个数就是快乐数。
>
> 如果 `n` 是 *快乐数* 就返回 `true` ；不是，则返回 `false` 。

```c++
输入：n = 19
输出：true
解释：
12 + 92 = 82
82 + 22 = 68
62 + 82 = 100
12 + 02 + 02 = 1
```

⚠️：最终会得到 1 或者 最终会进入循环。

```c++
class Solution {
public:
    int getNum(int n){  //获取位平方数
        int sum = 0;
        while(n != 0){
            sum += (n % 10) * (n % 10);
            n = n / 10;
        }
        return sum;
    }
    bool isHappy(int n) {
        unordered_set<int> s;
        int sum = getNum(n);
        while(sum != 1){
            if(s.count(sum)){
                return false;
            }
            else{
                s.insert(sum);
                sum = getNum(sum);
            }
        }
        return true;
    }
};
```

2. [两数之和](https://leetcode.cn/problems/two-sum/)

> 给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 **和为目标值** *`target`* 的那 **两个** 整数，并返回它们的数组下标。

```c++
输入：nums = [2,7,11,15], target = 9
输出：[0,1] 或者 [1,0]
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
```

```c++
vector<int> twoSum(vector<int>& nums, int target) {
    unordered_map<int, int> m;
    for(int i = 0; i < nums.size(); i++){
        auto it = m.find(target - nums[i]);
        if(it != m.end())
            return {it->second, i};
        else
            m[nums[i]] = i;
    }
    return {};
}
```

3. [四数相加II](https://leetcode.cn/problems/4sum-ii/)

>给你四个整数数组 `nums1`、`nums2`、`nums3` 和 `nums4` ，数组长度都是 `n` ，请你计算有多少个元组 `(i, j, k, l)` 能满足：
>
>- `0 <= i, j, k, l < n`
>- `nums1[i] + nums2[j] + nums3[k] + nums4[l] == 0`

将四个数组分成两部分，*A* 和 *B* 为一组，*C* 和 *D* 为另外一组。对于 A 和 B，我们使用二重循环对它们进行遍历，得到所有 A[i]+B[j] 的值并存入哈希映射中。对于哈希映射中的每个键值对，每个键表示一种 A[i]+B[j]，**对应的值为 A[i]+B[j] 出现的次数**。

```c++
int fourSumCount(vector<int>& nums1, vector<int>& nums2, vector<int>& nums3, 	     vector<int>& nums4) {
    int res = 0;
    unordered_map<int, int> m1, m2;
    int n = nums1.size();
    for(int i = 0; i < n; i++){
        for(int j = 0; j < n; j++){
            int sum = nums1[i] + nums2[j];
            m1[sum]++;
        }
    }
    for(int i = 0; i < n; i++){
        for(int j = 0; j < n; j++){
            int sum = nums4[i] + nums3[j];
            auto it = m1.find(0-sum);
            if(it != m1.end())
                res += it->second;
        }
    }
    return res;
}
```

4. [三数之和](https://leetcode.cn/problems/3sum/)

> 给你一个整数数组 `nums` ，判断是否存在三元组 `[nums[i], nums[j], nums[k]]` 满足 `i != j`、`i != k` 且 `j != k` ，同时还满足 `nums[i] + nums[j] + nums[k] == 0` 。请你返回所有和为 `0` 且不重复的三元组。
>
> **注意：**答案中不可以包含重复的三元组。

```c++
vector<vector<int>> threeSum(vector<int>& nums) {
    sort(nums.begin(), nums.end());
    vector<vector<int>> res;
    for(int i = 0; i < nums.size() - 2; i++){
        if(i > 0 && nums[i] == nums[i-1]) continue;   //注意不能重复！
        int left = i + 1, right = nums.size() - 1;
        while(left < right){
            if(nums[left] + nums[right] == -nums[i]){
                res.push_back({nums[i], nums[left], nums[right]});
              	//时刻保持left<right ！！！
                while(left < right && nums[left] == nums[left + 1]) left++;   
                while(left < right && nums[right] == nums[right - 1]) right--;
                left++;right--;   //得继续变更！
            }
            else if(nums[left] + nums[right] < -nums[i])
                left++;
            else
                right--;
        }
    }
    return res;
}
```

### 七、二叉树

1. [二叉树的前序遍历](https://leetcode.cn/problems/binary-tree-preorder-traversal/)

> 给你二叉树的根节点 `root` ，返回它节点值的 **前序** 遍历。

```c++
vector<int> preorderTraversal(TreeNode* root) {
    vector<int> res;
    stack<TreeNode *> s;      //栈中存储的是树结点，不是值
    if(root == nullptr) return res;
    s.push(root);             //先存储一个到栈中
    while(!s.empty()){
        TreeNode *curr = s.top();      //当出栈的时候才开始访问！！！
        s.pop();
        res.push_back(curr->val);
        if(curr->right)                //注意入栈的顺序
            s.push(curr->right);
        if(curr->left)
            s.push(curr->left);
    }
    return res;
}
```

2. [二叉树的中序遍历](https://leetcode.cn/problems/binary-tree-inorder-traversal/)

> 给定一个二叉树的根节点 `root` ，返回 *它的 **中序** 遍历* 。

```c++
vector<int> inorderTraversal(TreeNode* root) {
    vector<int> res;
    stack<TreeNode *> s;
    TreeNode *curr = root;
    while(curr || !s.empty()){
        while(curr){
            s.push(curr);
            curr = curr->left;
        }
        curr = s.top();
        res.push_back(curr->val);
        s.pop();
        curr = curr->right;
    }
    return res;
}
```

3. [二叉树的层序遍历](https://leetcode.cn/problems/binary-tree-level-order-traversal/)

> 给你二叉树的根节点 `root` ，返回其节点值的 **层序遍历** 。 （即逐层地，从左到右访问所有节点）。

```c++
输入：root = [3,9,20,null,null,15,7]
输出：[[3],[9,20],[15,7]]
```

```c++
vector<vector<int>> levelOrder(TreeNode* root) {
    vector<vector<int>> res;
    if(root == nullptr) 
        return res;
    queue<TreeNode *> q;
    TreeNode *curr = root;
    q.push(curr);
    while(!q.empty()){ 
        vector<int> temp;   //每层新的vector<int>
        int size = q.size();
        for(int i = 0; i < size; i++){
            curr = q.front();
            q.pop();
            temp.push_back(curr->val);
            if(curr->left)
                q.push(curr->left);
            if(curr->right)
                q.push(curr->right);
        }
        res.push_back(temp);
    }
    return res;
}
```

4. [平衡二叉树](https://leetcode.cn/problems/balanced-binary-tree/)

> 给定一个二叉树，判断它是否是 平衡二叉树

```c++
int height(TreeNode* root){
    if(root == nullptr)
        return 0;
    return 1 + max(height(root->left), height(root->right));
}
bool isBalanced(TreeNode* root) {
    if(root == nullptr)
        return true;
    else
        return abs((height(root->left) - height(root->right))) <= 1 && 	                              isBalanced(root->left) && isBalanced(root->right);  //当前结点+递归左右！
}
```

### 八、回溯

1. [组合](https://leetcode.cn/problems/combinations/)

> 给定两个整数 `n` 和 `k`，返回范围 `[1, n]` 中所有可能的 `k` 个数的组合。你可以按 **任何顺序** 返回答案。

```c++
输入：n = 4, k = 2
输出：
[[2,4],[3,4],[2,3],[1,2],[1,3],[1,4]]
```

```c++
/*
已经选择的元素个数：temp.size();
还需要的元素个数为: k - temp.size();
在集合n中至多要从该起始位置 : n - (k - temp.size()) + 1，开始遍历
*/
class Solution {
public:
    void backtrace(vector<vector<int>>& res, vector<int>& temp, int start, int n, int k){
        if(temp.size() == k){
            res.push_back(temp);
            return;
        }
        for(int i = start; i <= n - (k-temp.size()) + 1; i++){  //剪枝
            temp.push_back(i);
            backtrace(res, temp, i+1, n, k);
            temp.pop_back();
        }
    }

    vector<vector<int>> combine(int n, int k) {
        vector<vector<int>> res;
        vector<int> temp;
        backtrace(res, temp, 1, n, k);
        return res;
    }
};
```

2. [组合总和III](https://leetcode.cn/problems/combination-sum-iii/)

> 找出所有相加之和为 `n` 的 `k` 个数的组合，且满足下列条件：
>
> - 只使用数字1到9
> - 每个数字 **最多使用一次** 
>
> 返回 *所有可能的有效组合的列表* 。该列表不能包含相同的组合两次，组合可以以任何顺序返回。

```c++
输入: k = 3, n = 9
输出: [[1,2,6], [1,3,5], [2,3,4]]
```

```c++
class Solution {
public:
    void backtrace(vector<vector<int>>& res, vector<int>& temp, int curr_sum, int start, int k, int target){
        if(curr_sum > target)   //剪枝
            return;
        if(temp.size() == k){      
            if(curr_sum == target)
                res.push_back(temp);
            return;             //到树底就返回，无论是否满足目标条件
        }
        for(int i = start; i <= 9 - (k-temp.size()) + 1; i++){   //剪枝
            temp.push_back(i);
            curr_sum = curr_sum + i;
            backtrace(res, temp, curr_sum, i + 1, k, target);  //注意i+1
            curr_sum = curr_sum - i;  //回溯
            temp.pop_back();					//回溯
        }
    }
    vector<vector<int>> combinationSum3(int k, int n) {
        vector<vector<int>> res;
        vector<int> temp;
        backtrace(res,temp,0,1,k,n);
        return res;
    }
};
```

3. [电话号码的字母组合](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/)

```c++
class Solution {
public:
    void backtrace(string digits, vector<string>& res, string& s, int index, unordered_map<char, string> phoneMap){
        if(s.size() == digits.size()){
            res.push_back(s);
            return;
        }
        int number = digits[index];           //通过index指明到哪一个数字了
        for(int i = 0; i < phoneMap[number].size(); i++){ //每个number有不同的字母
            s.push_back(phoneMap[number][i]);
            backtrace(digits,res,s,index+1,phoneMap); //index+1 到下一个数字了
            s.pop_back();
        }
    }
    vector<string> letterCombinations(string digits) {
        vector<string> res;
        if(digits.empty())
            return res;
        unordered_map<char, string> phoneMap{
            {'2', "abc"},
            {'3', "def"},
            {'4', "ghi"},
            {'5', "jkl"},
            {'6', "mno"},
            {'7', "pqrs"},
            {'8', "tuv"},
            {'9', "wxyz"}
        };
        string s;
        backtrace(digits,res,s,0,phoneMap);
        return res;
    }
};
```

4. [组合总和](https://leetcode.cn/problems/combination-sum/)

> 给你一个 **无重复元素** 的整数数组 `candidates` 和一个目标整数 `target` ，找出 `candidates` 中可以使数字和为目标数 `target` 的 所有 **不同组合** ，并以列表形式返回。你可以按 **任意顺序** 返回这些组合。
>
> `candidates` 中的 **同一个** 数字可以 **无限制重复被选取** 。如果至少一个数字的被选数量不同，则两种组合是不同的。

```c++
输入: candidates = [2,3,5], target = 8
输出: [[2,2,2,2],[2,3,3],[3,5]]
```

```c++
class Solution {
public:
    void traceback(vector<vector<int>>& res, vector<int>& temp, int target, int sum, vector<int> candidates, int index){
        if(sum > target) return;  //剪枝后可注释
        if(sum == target){
            res.push_back(temp);
            return;
        }
      //剪枝，需要先从小到大排序
        for(int i = index; i < candidates.size() && (sum + candidates[i] <= target); i++){ 
            temp.push_back(candidates[i]);
            sum = sum + candidates[i];
            traceback(res, temp, target, sum, candidates, i);
            sum = sum - candidates[i];
            temp.pop_back();
        }
    }

    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        vector<vector<int>> res;
        vector<int> temp;
        sort(candidates.begin(), candidates.end()); //排序方便剪枝
        traceback(res, temp, target, 0, candidates,0);
        return res;
    }
};
```

5. [组合总和II](https://leetcode.cn/problems/combination-sum-ii/)

> 给定一个候选人编号的集合 `candidates` 和一个目标数 `target` ，找出 `candidates` 中所有可以使数字和为 `target` 的组合。
>
> `candidates` 中的每个数字在每个组合中只能使用 **一次** 。
>
> **注意：**解集不能包含重复的组合。 

```c++
输入: candidates = [10,1,2,7,6,1,5], target = 8,
输出:
[[1,1,6],[1,2,5],[1,7],[2,6]]
```

```c++
class Solution {
public:
    void traceback(vector<vector<int>>& res, vector<int>& temp, int target, int sum, vector<int> candidates, int index){
        if(sum > target) return;  //剪枝后可注释
        if(sum == target){
            res.push_back(temp);
            return;
        }
      //剪枝，需要先从小到大排序
        for(int i = index; i < candidates.size() && (sum + candidates[i] <= target); i++){
            if(i > index && candidates[i] == candidates[i-1]) continue;  //非常重要！！
            temp.push_back(candidates[i]);
            sum = sum + candidates[i];
            traceback(res, temp, target, sum, candidates, i+1);
            sum = sum - candidates[i];
            temp.pop_back();
        }
    }

    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        vector<vector<int>> res;
        vector<int> temp;
        sort(candidates.begin(), candidates.end()); //排序方便剪枝
        traceback(res, temp, target, 0, candidates,0);
        return res;
    }
};
```

6. [子集](https://leetcode.cn/problems/subsets/)

> 给你一个整数数组 `nums` ，数组中的元素 **互不相同** 。返回该数组所有可能的子集（幂集）。
>
> 解集 **不能** 包含重复的子集。你可以按 **任意顺序** 返回解集。

```c++
输入：nums = [1,2,3]
输出：[[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

```c++
class Solution {
public:
    void backtrace(vector<vector<int>>& res, vector<int>& temp, vector<int> nums, int index){
        res.push_back(temp);       //直接先收录，第一次就是[]空集
        if(index >= nums.size())
            return;
        for(int i = index; i < nums.size(); i++){
            temp.push_back(nums[i]);
            backtrace(res, temp, nums, i+1);
            temp.pop_back();
        }
    }
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> res;
        vector<int> temp;
        backtrace(res, temp, nums, 0);
        return res;

    }
};
```

7. [子集II](https://leetcode.cn/problems/subsets-ii/)

> 给你一个整数数组 `nums` ，其中可能包含重复元素，请你返回该数组所有可能的 子集（幂集）。
>
> 解集 **不能** 包含重复的子集。返回的解集中，子集可以按 **任意顺序** 排列。

```c++
输入：nums = [1,2,2]
输出：[[],[1],[1,2],[1,2,2],[2],[2,2]]
```

```c++
class Solution {
public:
    void backtrack(vector<vector<int>>& res, vector<int>& temp, vector<int> nums, int index) {
        res.push_back(temp);
        if(index >= nums.size()) return;
        unordered_set<int> used_set; //用于记录 同一层 是否有相同值已被访问，不参与递归无需回溯
        for(int i = index; i < nums.size(); i++){
            if(used_set.find(nums[i]) != used_set.end())      //存在即有相同值被访问过
                continue;
            temp.push_back(nums[i]);
            used_set.insert(nums[i]);          //标记被访问
            backtrack(res, temp, nums, i+1);
            temp.pop_back();    //由于used_set只用于标记同一层，不参与迭代，不需要回溯！！！
        }
    }
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        vector<vector<int>> res;
        vector<int> temp;
        sort(nums.begin(), nums.end());
        backtrack(res, temp, nums, 0);
        return res;
    }
};
```

8. [非递减子序列](https://leetcode.cn/problems/non-decreasing-subsequences/)

> 给你一个整数数组 `nums` ，找出并返回所有该数组中不同的递增子序列，递增子序列中 **至少有两个元素** 。你可以按 **任意顺序** 返回答案。
>
> 数组中可能含有重复元素，如出现两个整数相等，也可以视作递增序列的一种特殊情况。

```c++
输入：nums = [4,6,7,7]
输出：[[4,6],[4,6,7],[4,6,7,7],[4,7],[4,7,7],[6,7],[6,7,7],[7,7]]
```

```c++
class Solution {
public:
    void backtrace(vector<vector<int>>& res, vector<int>& temp, vector<int> nums, int index){
        if(temp.size() >= 2){     //至少有两个元素
            if(temp[temp.size()-1] >= temp[temp.size()-2])   //提前剪枝可省略判断，加速
                res.push_back(temp);
            else
                return;
        }
        unordered_set<int> used_set;
        for(int i = index; i < nums.size(); i++){
            if(used_set.find(nums[i]) != used_set.end())  //同上题，同层过滤
                continue;
            if(!temp.empty() && nums[i] < temp.back()) continue;  //提前剪枝，注意判空
            temp.push_back(nums[i]);
            used_set.insert(nums[i]);
            backtrace(res, temp, nums, i + 1);
            temp.pop_back();
        }

    }
    vector<vector<int>> findSubsequences(vector<int>& nums) {
        vector<vector<int>> res;
        vector<int> temp;
        backtrace(res, temp, nums, 0);
        return res;
    }
};
```

9. [全排列](https://leetcode.cn/problems/permutations/)

> 给定一个不含重复数字的数组 `nums` ，返回其 *所有可能的全排列* 。你可以 **按任意顺序** 返回答案。

```c++
输入：nums = [1,2,3]
输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

```c++
class Solution {
public:
    void backtrack(vector<vector<int>>& res, vector<int>& temp, vector<int> nums, unordered_set<int>& used_set){
        if(temp.size() == nums.size()){
            res.push_back(temp);
            return;
        }
        for(int i = 0; i < nums.size(); i++){    //每次从0开始遍历
            if(used_set.find(i) != used_set.end()) continue;  //跳过用过的点
            temp.push_back(nums[i]);
            used_set.insert(i);
            backtrack(res, temp, nums, used_set);
            used_set.erase(i);
            temp.pop_back();
        }
    }
    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int>> res;
        vector<int> temp;
        unordered_set<int> used_set;   /标记一条链路上的某数是否被用过
        backtrack(res, temp, nums, used_set);
        return res;
    }
};
```

10. [全排列II](https://leetcode.cn/problems/permutations-ii/)

> 给定一个可包含重复数字的序列 `nums` ，***按任意顺序*** 返回所有不重复的全排列。

```c++
输入：nums = [1,1,2]
输出：[[1,1,2],[1,2,1],[2,1,1]]
```

```c++
class Solution {
public:
    void backtrack(vector<vector<int>>& res, vector<int>& temp, vector<int> nums, vector <int>& vis){
        if(temp.size() == nums.size()){
            res.push_back(temp);
            return;
        }
        for(int i = 0; i < nums.size(); i++){
            if(vis[i] == 1) continue;
          //对原数组排序，保证相同的数字都相邻，然后每次填入的数一定是这个数所在重复数集合中「从左往右第一个未被填过的数字」。如若相邻相同，只能从左往右访问一遍。!vis[i-1]筛掉其他顺序
            else if(i > 0 && nums[i] == nums[i - 1] && !vis[i-1]) continue;
            temp.push_back(nums[i]);
            vis[i] = 1;
            backtrack(res, temp, nums, vis);
            vis[i] = 0;
            temp.pop_back();
        }

    }
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        vector<vector<int>> res;
        vector<int> temp;
        vector<int> vis(nums.size(), 0);
        sort(nums.begin(), nums.end());
        backtrack(res, temp, nums, vis);
        return res;
    }
};
```

### 九、图论

图的构造：

邻接矩阵

```

```

邻接表

### 十、数论

1. 质数的判断

```c++
bool is_prime(int x){
  if(x < 2) return false;
  for(int i = 2; i <= x / i; i++){
		  if(x % i == 0)
          return false;
  }
  return true;
}
```

```c++
尽量不要写成：
1. for(int i = 2; i * i <= n; i ++) // i 较大时,i * i 可能超出int的最大表示范围,导致结果出错.
2. for(int i = 2; i <= sqrt(n); i ++) // 反复调用sqrt()函数, 运行效率比较低
```

2. 分解质因数

```c++
void devide(unordered_map<int,int>& m, int x){
  for(int i = 2; i < x/i; i++){
    if(x % i == 0){
      while(x % i == 0){
        m[i]++;
        x = x / i;
      }
    }
  }
  if(x > 1) m[x]++;
}

int main(){
  int x;
  cin >> x;
  unordered_map<int,int> m;
  devide(m,x);
  for(auto p : m){
    cout << p.first << " " << p.second << endl;
  }
  return 0;
}
```

3. 筛质数

> 给定一个正整数n，请你求出1~n中质数的个数。

```c++
vector<int> get_prime(int n){
    vector<int> res;
    vector<int> used(n+1, 0);   //n+1位
    for(int i = 2; i <= n; i++){ //2开始
        if(used[i] != 1){
            res.push_back(i);
            for(int j = i; j <= n; j=j+i){
                used[j] = 1;   //下标j
            }
        }
    }
    return res;
}
```

4. 求约数

```c++
vector<int> get_divisors(int n){
    vector<int> res;
    for(int i = 1; i <= n / i; i++){
        if(n % i == 0){
            res.push_back(i);
            if(n / i != i){
                res.push_back(n / i);
            }
        }
    }
    sort(res.begin(), res.end());
    return res;
}
```

5. 约数个数

```c++
int divisor_sum(int n, int MOD){
    int res = 1;
    //首先分解质因数
    unordered_map<int, int> m;
    for(int i = 2; i <= n / i; i++){
        if(n % i == 0){
            while(n % i == 0){
                m[i]++;
                n = n / i;
            }
        }
    }
    if(n != 1) m[n]++;      //别忘了！！！
  //求约数个数
    for(auto it : m){
        res = res * (it.second + 1) % MOD;
    }
    return res;
}
```

6. 约数之和

```c++
int divisor_mul(int n, int MOD){
    int res = 1;
    //首先分解质因数
    unordered_map<int, int> m;
    for(int i = 2; i <= n / i; i++){
        if(n % i == 0){
            while(n % i == 0){
                m[i]++;
                n = n / i;
            }
        }
    }
    if(n != 1) m[n]++;
    //约数之和
    for(auto it : m){
        int p = it.first, c = it.second;
        int temp = 1;
        for(int i = 1; i <= c; i++){
            temp = (p * temp + 1) % MOD;  //注意！！！
        }
        res = res * temp % MOD;
    }
    return res;
}
```

7. 最大公约数

```c++
int gcd(int a, int b) {
    while (b != 0) {
        int temp = b;
        b = a % b;
        a = temp;
    }
    return a;
}
int gcd(int a, int b){
  return b ? gcd(b, a % b) : a;
}
```

8. 快速幂

```c++
int pow(int n, int k, int MOD){
    int res=1;
    while(k > 0){
        if(k & 1 != 0) res = res * n % MOD;  //二进制位为1则相乘
        n = n * n % MOD;  //二进制位无论是否为0都自乘，
        k = k >> 1;
    }
    return res;
}
```

