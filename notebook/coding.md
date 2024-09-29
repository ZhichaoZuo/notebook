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

9. [零钱兑换](https://leetcode.cn/problems/coin-change/)

> 给你一个整数数组 `coins` ，表示不同面额的硬币；以及一个整数 `amount` ，表示总金额。
>
> 计算并返回可以凑成总金额所需的 **最少的硬币个数** 。如果没有任何一种硬币组合能组成总金额，返回 `-1` 。
>
> 你可以认为每种硬币的数量是无限的。

```c++
输入：coins = [1, 2, 5], amount = 11
输出：3 
```

```c++
// dp[i] 表示凑成金额 i 所需要的最少硬币数量。
int coinChange(vector<int>& coins, int amount) {
    int MAX  = amount + 1; //初值使用amount + 1是因为最多也就是amount个1相加，再多就不可能了 使用INT_MAX也可以做到，
    //但是一旦遇到 dp[i - coin] + 1 就会导致int范围溢出，需要额外判断，
    vector<int> dp(amount + 1, MAX);
    dp[0] = 0;
    for(int i = 1; i <= amount; i++){
        for(auto& coin : coins){
            if(coin <= i){
                dp[i] = min(dp[i], dp[i-coin] + 1);
            }
        }
    }
    //如果 dp[amount] 的值依然是初始设定的很大值，则说明无法凑出该金额，返回 -1
    return dp[amount] > amount ? -1 : dp[amount];
}
```

10. [ 零钱兑换 II](https://leetcode.cn/problems/coin-change-ii/)

> 给你一个整数数组 `coins` 表示不同面额的硬币，另给一个整数 `amount` 表示总金额。
>
> 请你计算并返回可以凑成总金额的硬币组合数。如果任何硬币组合都无法凑出总金额，返回 `0` 。
>
> 假设每一种面额的硬币有无限个。 
>
> 题目数据保证结果符合 32 位带符号整数。

```c++
输入：amount = 5, coins = [1, 2, 5]
输出：4
解释：有四种方式可以凑成总金额：
5=5
5=2+2+1
5=2+1+1+1
5=1+1+1+1+1
```

```c++
int change(int amount, vector<int>& coins) {
    // dp[i] 表示凑成金额 i 的组合数
    vector<int> dp(amount + 1, 0);
    dp[0] = 1; // 凑成金额 0 的组合数为 1（不选任何硬币）
    // 遍历每一个硬币
    for (int coin : coins) {
        // 更新所有金额的组合数
        for (int i = coin; i <= amount; i++) {
            dp[i] += dp[i - coin]; // 通过 dp[i - coin] 来更新 dp[i]
        }
    }
    return dp[amount]; // 返回凑成 amount 的组合数
}
```

11. [完全平方数](https://leetcode.cn/problems/perfect-squares/)

> 给你一个整数 `n` ，返回 *和为 `n` 的完全平方数的最少数量* 。
>
> **完全平方数** 是一个整数，其值等于另一个整数的平方；换句话说，其值等于一个整数自乘的积。例如，`1`、`4`、`9` 和 `16` 都是完全平方数，而 `3` 和 `11` 不是。

```c++
输入：n = 13
输出：2
解释：13 = 4 + 9
```

```c++
int numSquares(int n) {
    // dp数组，dp[i]表示组成数字i所需的最少完全平方数的个数
    vector<int> dp(n+1, n+1);    // 初始化为n+1, 因为最多用n个1来表示
    dp[0] = 0;    // 初始化dp[0]为0，因为0不需要任何数
    // 遍历1到n，逐步求出dp[i
    for(int i = 0; i <= n; i++){
        for(int j = 1; j * j <= i; j++){
            dp[i] = min(dp[i], dp[i - j*j] + 1);
        }
    }
    return dp[n];
}
```

12. [打家劫舍](https://leetcode.cn/problems/house-robber/)

> 你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，**如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警**。
>
> 给定一个代表每个房屋存放金额的非负整数数组，计算你 **不触动警报装置的情况下** ，一夜之内能够偷窃到的最高金额。

```c++
输入：[2,7,9,3,1]
输出：12
```

```c++
/*
dp[i] 表示到第 i 间房屋为止，能够偷取到的最大现金数量。
- 不偷取第 i 间房屋，则 dp[i] = dp[i-1]。
- 偷取第 i 间房屋，则我们不能偷取第 i-1 间房屋，这样 dp[i] = nums[i] + dp[i-2]。
dp[i] = max(dp[i−1], nums[i] + dp[i−2])
*/
int rob(vector<int>& nums) {
    if(nums.size() == 1) return nums[0];
    if(nums.size() == 2) return max(nums[0],nums[1]);
    vector<int> dp(nums.size(), 0);
    dp[0] = nums[0];
    dp[1] = max(nums[0],nums[1]);    //切记！dp[i] 表示到第 i 间房屋为止，能够偷取到的最大现金数量。
    for(int i = 2; i < nums.size(); i++){
        dp[i] = max(dp[i-1], dp[i-2] + nums[i]);   //比较不偷取当前房屋的情况和偷取当前房屋的情况。
    }
    return dp[nums.size()-1];
}
```

13. [打家劫舍 II](https://leetcode.cn/problems/house-robber-ii/)

> 你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都 **围成一圈** ，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，**如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警** 。
>
> 给定一个代表每个房屋存放金额的非负整数数组，计算你 **在不触动警报装置的情况下** ，今晚能够偷窃到的最高金额。

```c++
输入：nums = [2,3,2]
输出：3
解释：你不能先偷窃 1 号房屋（金额 = 2），然后偷窃 3 号房屋（金额 = 2）, 因为他们是相邻的
```

```c++
/*
- 不偷第一间房屋，考虑第2间到最后一间房屋。
- 不偷最后一间房屋，考虑第一间到倒数第二间房屋。
*/
int robfrom(vector<int>& nums, int start, int end){
    int n = end - start + 1;
    if(n == 1) return nums[start];
    vector<int> dp(n, 0);
    dp[0] = nums[start];
    dp[1] = max(nums[start], nums[start + 1]);
    for(int i = 2; i < n; i++){
        dp[i] = max(dp[i-1], dp[i-2] + nums[start + i]);
    }
    return dp[n-1];
}
int rob(vector<int>& nums) {
    int n = nums.size();
    if(n == 1) return nums[0];
    if(n == 2) return max(nums[0], nums[1]);
    int case1 = robfrom(nums, 0, n-2);   // 情况1: 不偷最后一间，偷第1到倒数第二间
    int case2 = robfrom(nums, 1, n-1);   // 情况2: 不偷第一间，偷第2到最后一间
    return max(case1, case2);
}
```

14. 0-1背包问题

> 有 $n$ 个物品和一个容量为 $W$ 的背包，每个物品有重量 $W_i$ 和价值 $V_i$ 两种属性，要求选若干物品放入背包使背包中物品的总价值最大且背包中物品的总重量不超过背包的容量。

```c++
int main() {
    int n, bagweight;// bagweight代表行李箱空间

    cin >> n >> bagweight;

    vector<int> weight(n, 0); // 存储每件物品所占空间
    vector<int> value(n, 0);  // 存储每件物品价值

    for(int i = 0; i < n; ++i) {
        cin >> weight[i];
    }
    for(int j = 0; j < n; ++j) {
        cin >> value[j];
    }
    // dp数组, dp[i][j]代表行李箱空间为j的情况下,从下标为[0, i]的物品里面任意取,能达到的最大价值
    vector<vector<int>> dp(weight.size(), vector<int>(bagweight + 1, 0));

    // 初始化, 因为需要用到dp[i - 1]的值
    // j < weight[0]已在上方被初始化为0
    // j >= weight[0]的值就初始化为value[0]
    for (int j = weight[0]; j <= bagweight; j++) {
        dp[0][j] = value[0];
    }
    
    for(int i = 1; i < weight.size(); i++) { // 遍历科研物品
        for(int j = 0; j <= bagweight; j++) { // 遍历行李箱容量
            if (j < weight[i]) dp[i][j] = dp[i - 1][j]; // 如果装不下这个物品,那么就继承dp[i - 1][j]的值
            else {
                dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - weight[i]] + value[i]);
            }
        }
    }
    
    cout << dp[n - 1][bagweight] << endl;
    return 0;
}
```

```c++
//一维dp
int main() {
    int n, bagweight;// bagweight代表行李箱空间

    cin >> n >> bagweight;

    vector<int> weight(n, 0); // 存储每件物品所占空间
    vector<int> value(n, 0);  // 存储每件物品价值

    for(int i = 0; i < n; ++i) {
        cin >> weight[i];
    }
    for(int j = 0; j < n; ++j) {
        cin >> value[j];
    }
    // dp数组, dp[j]代表行李箱空间为j的情况下,能达到的最大价值
    vector<int> dp(bagweight + 1, 0);

    for(int i = 1; i < weight.size(); i++) { // 遍历科研物品
        for(int j = bagweight; j >= weight[i]; j--) { // 遍历行李箱容量
            dp[i][j] = max(dp[j], dp[j - weight[i]] + value[i]);
        }
    }
    
    cout << dp[bagweight] << endl;
    return 0;
}
```

15. [分割等和子集](https://leetcode.cn/problems/partition-equal-subset-sum/)

> 给你一个 **只包含正整数** 的 **非空** 数组 `nums` 。请你判断是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。

```c++
输入：nums = [1,5,11,5]
输出：true
```

```c++
bool canPartition(vector<int>& nums) {
    int n = nums.size();
    if (n < 2)
        return false;
    int sum = 0, maxnum = 0;
    for(int num : nums){
        sum += num;
        maxnum = max(maxnum, num);
    }
    if(sum % 2 == 1)    // 总和为奇数时无法平分
        return false;
    int target = sum / 2;
    if(maxnum > target)  //最大值大于sum/2时无法平分
        return false;
    vector<vector<bool>> dp(n, vector<bool>(target + 1, false)); //i表示使用前i个数，j表示是否可以凑出和为j的子集
    for(int i = 0; i < n; i++)
        dp[i][0] = true;
    dp[0][nums[0]] = true;    
    for(int i = 1; i < n; i++){       //对n个数进行遍历
        for(int j = 1; j <= target; j++){
            if(j >= nums[i])
                dp[i][j] = dp[i-1][j] || dp[i-1][j-nums[i]];    //如果选择 nums[i]，是否能通过前 i-1 个元素凑出 j - nums[i]
            else
                dp[i][j] = dp[i-1][j];      //表示不选 nums[i] 时，能否通过前 i-1 个元素凑出和 j。
        }
    }
    return dp[n-1][target];
}
```

```c++
bool canPartition(vector<int>& nums) {
    int n = nums.size();
    if (n < 2)
        return false;
    int sum = 0, maxnum = 0;
    for(int num : nums){
        sum += num;
        maxnum = max(maxnum, num);
    }
    if(sum % 2 == 1)    // 总和为奇数时无法平分
        return false;
    int target = sum / 2;
    if(maxnum > target)  //最大值大于sum/2时无法平分
        return false;
    vector<bool> dp(target + 1, false); //j表示是否可以凑出和为j的子集
    dp[0] = true;
    for(int& num : nums){       //对n个数进行遍历
        for(int j = target; j >=num ; j--){       //注意j>=num
            dp[j] = dp[j] || dp[j-num];    //如果选择 nums[i]，是否能通过前 i-1 个元素凑出 j - nums[i]
        }
    }
    return dp[target];
}
```

16. [目标和](https://leetcode.cn/problems/target-sum/)

> 给你一个非负整数数组 `nums` 和一个整数 `target` 。
>
> 向数组中的每个整数前添加 `'+'` 或 `'-'` ，然后串联起所有整数，可以构造一个 **表达式** ：
>
> - 例如，`nums = [2, 1]` ，可以在 `2` 之前添加 `'+'` ，在 `1` 之前添加 `'-'` ，然后串联起来得到表达式 `"+2-1"` 。
>
> 返回可以通过上述方法构造的、运算结果等于 `target` 的不同 **表达式** 的数目。

```c++
输入：nums = [1,1,1,1,1], target = 3
输出：5
解释：一共有 5 种方法让最终目标和为 3 。
-1 + 1 + 1 + 1 + 1 = 3
+1 - 1 + 1 + 1 + 1 = 3
+1 + 1 - 1 + 1 + 1 = 3
+1 + 1 + 1 - 1 + 1 = 3
+1 + 1 + 1 + 1 - 1 = 3
```

```c++
/*
分为两个背包，一个存放符号为+的值，一个存放符号为-的值，pos+neg=sum,pos-neg=target;则neg=(sum-target)/2;
dp[i][j] 表示使用前 i 个元素，凑成和为 j 的方案数

*/
int findTargetSumWays(vector<int>& nums, int target) {
    int n = nums.size();
    int sum = 0;
    for(int& num : nums){
        sum += num;
    }
    // 如果目标和大于总和，或 (sum - target) 不是偶数，则无解
    if(sum < target) return 0;
    if((sum - target) % 2 != 0) return 0;
    int neg = (sum - target) / 2;
    vector<vector<int>> dp(n+1, vector<int>(neg + 1, 0));
    // 初始化：dp[0][0] = 1 表示不使用任何数时，和为 0 的情况; 不是选择第一个数；当没有任何元素可以选取时，元素和只能是 0，对应的方案数是 1
    dp[0][0] = 1;
    for(int i = 1; i <= n; i++){
        for(int j = 0; j <= neg; j++){
            if(j >= nums[i-1])
                dp[i][j] = dp[i-1][j] + dp[i-1][j-nums[i-1]];
            else
                dp[i][j] = dp[i-1][j];
        }
    }
    return dp[n][neg];
}
```

```c++
//一维dp
int findTargetSumWays(vector<int>& nums, int target) {
    int n = nums.size();
    int sum = 0;
    for(int& num : nums){
        sum = sum + num;
    }
    if(sum < target) return 0;
    if((sum - target) % 2 == 1) return 0;
    int neg = (sum - target) / 2;
    vector<int> dp(neg + 1, 0);
    dp[0] = 1;
    for(int& num : nums){
        for(int j = neg; j >=num; j--){
            if(j >= num)
                dp[j] = dp[j] + dp[j-num];
        }
    }
    return dp[neg];
}
```

17. [最小路径和](https://leetcode.cn/problems/minimum-path-sum/)

> 给定一个包含非负整数的 `*m* x *n*` 网格 `grid` ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。
>
> **说明：**每次只能向下或者向右移动一步。

```c++
输入：grid = 
[
    [1,3,1],
    [1,5,1],
    [4,2,1]
]
输出：7
解释：因为路径 1→3→1→1→1 的总和最小。
```

```c++
int minPathSum(vector<vector<int>>& grid) {
    int m = grid.size(), n = grid[0].size();
    vector<vector<int>> dp(m, vector<int>(n, INT_MAX));
    dp[0][0] = grid[0][0];
    for(int i = 1; i < m; i++)
        dp[i][0] = dp[i-1][0] + grid[i][0];
    for(int j = 1; j < n; j++){
        dp[0][j] = dp[0][j-1] + grid[0][j];
    }
    for(int i = 1; i < m; i++){
        for(int j = 1; j < n; j++){
            dp[i][j] = min(dp[i-1][j], dp[i][j-1]) + grid[i][j];
        }
    } 
    return dp[m-1][n-1];
}
```

18. [乘积最大子数组](https://leetcode.cn/problems/maximum-product-subarray/)

> 给你一个整数数组 `nums` ，请你找出数组中乘积最大的非空连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。
>
> 测试用例的答案是一个 **32-位** 整数。

```c++
输入: nums = [2,3,-2,4]
输出: 6
```

```c++
/*
因为存在负数的原因，当前位置的最优解未必是由前一个位置的最优解转移得到的. 用白话来解释就是: 两个负数相乘有可能会得到更大的结果, 当前的最大值并不一定是通过上个状态的最大值决定的, 也可能是上个状态的最小值决定的. 
需要同时维护上个状态的最大值和最小值，需要维护两个dp数组, 而且这两个数组是完全独立的。
        fmax[i] = max(fmax[i-1] * nums[i], fmin[i-1] * nums[i], nums[i]); 三者取最大
        fmin[i] = min(fmax[i-1] * nums[i], fmin[i-1] * nums[i], nums[i]); 三者取最小
*/
int maxProduct(vector<int>& nums) {
    int ans = nums[0];
    int n = nums.size();
    vector<int> fmax(n, INT_MIN);
    vector<int> fmin(n, INT_MAX);
    fmax[0] = nums[0], fmin[0] = nums[0];
    for(int i = 1; i < n; i++){
        fmax[i] = max(max(fmax[i-1] * nums[i], fmin[i-1] * nums[i]), nums[i]);
        fmin[i] = min(min(fmax[i-1] * nums[i], fmin[i-1] * nums[i]), nums[i]);
        ans = max(ans, fmax[i]);
    }
    return ans;
}
```

19. [单词拆分](https://leetcode.cn/problems/word-break/)

> 给你一个字符串 `s` 和一个字符串列表 `wordDict` 作为字典。如果可以利用字典中出现的一个或多个单词拼接出 `s` 则返回 `true`。
>
> **注意：**不要求字典中出现的单词全部都使用，并且字典中的单词可以重复使用。

```c++
输入: s = "leetcode", wordDict = ["leet", "code"]
输出: true
输入: s = "catsandog", wordDict = ["cats", "dog", "sand", "and", "cat"]
输出: false
```

```c++
// dp[j] = true 且 j~i组成的字符串属于wordDict，则dp[i] = true
bool wordBreak(string s, vector<string>& wordDict) {
    // 将字典转换为unordered_set，便于查找是否存在某个单词
    unordered_set<string> dict;      
    for(string& word : wordDict)
        dict.insert(word);
    int n = s.size();
    // DP数组，dp[i]表示s的前i个字符是否可以由字典中的单词拼接而成
    vector<bool> dp(n+1, false);
    dp[0] = true;  // 空字符串可以被认为是由字典单词拼接而成
    for(int i = 1; i <= n; i++){
        // 对于每个i，查看它之前的所有子串是否可以被字典中的单词组成
        for(int j = 0; j < i; j++){
            // 如果dp[j]为true，且从j到i的子串在字典中存在
            if(dp[j] && dict.find(s.substr(j, i - j)) != dict.end()){
                dp[i] = true;
                break;  // 一旦找到一个匹配的子串，就可以结束当前i的循环
            }    
        }
    }
    // 最终返回dp[s.size()]，表示整个字符串是否可以由字典中的单词组成
    return dp[n];
}
```

20. [编辑距离](https://leetcode.cn/problems/edit-distance/)

> 给你两个单词 `word1` 和 `word2`， *请返回将 `word1` 转换成 `word2` 所使用的最少操作数* 。
>
> 你可以对一个单词进行如下三种操作：
>
> - 插入一个字符
> - 删除一个字符
> - 替换一个字符

```c++
输入：word1 = "horse", word2 = "ros"
输出：3
```

```markdown
动态规划思路
我们使用一个二维的 DP 数组 dp，其中 dp[i][j] 表示将 word1 的前 i 个字符转换为 word2 的前 j 个字符所需的最小操作数。

初始状态：
dp[0][0] = 0，因为将空字符串转换为空字符串需要 0 次操作。
对于 dp[i][0]，表示将 word1 的前 i 个字符转换为空字符串，所需的操作数是删除 i 次字符，因此 dp[i][0] = i。
对于 dp[0][j]，表示将空字符串转换为 word2 的前 j 个字符，所需的操作数是插入 j 次字符，因此 dp[0][j] = j。
转移方程：
对于每个 dp[i][j]，我们有三种情况可以选择：
- （如果 word1[i-1] != word2[j-1]）
删除操作：如果删除 word1 的一个字符，那么 dp[i][j] = dp[i-1][j] + 1。
插入操作：如果在 word1 中插入一个字符**(相当于word2删除一个字符)**，那么 dp[i][j] = dp[i][j-1] + 1。
替换操作：如果替换 word1 的一个字符为 word2 的字符，那么 dp[i][j] = dp[i-1][j-1] + 1。
- 如果两个字符相同，则不需要替换，即 dp[i][j] = dp[i-1][j-1]。
最终的答案就是 dp[m][n]，其中 m 是 word1 的长度，n 是 word2 的长度
```

```c++
int minDistance(string word1, string word2) {
    int n1 = word1.size(), n2 = word2.size();
    vector<vector<int>> dp(n1+1, vector<int>(n2+1, 0));
    // word1 的前 i 个字符变成空字符串，删除 i 个字符
    for(int i = 0; i <= n1; i++)
        dp[i][0] = i;
    // 空字符串变成 word2 的前 j 个字符，插入 j 个字符
    for(int j = 0; j <= n2; j++)
        dp[0][j] = j;
    for(int i = 1; i <= n1; i++){
        for(int j = 1; j <= n2; j++){
            if(word1[i-1] == word2[j-1]){ // 字符相同，不需要操作
                dp[i][j] = dp[i-1][j-1];
            }
            else{
                dp[i][j] = min({
                    dp[i-1][j] + 1,    //删除word1[i-1]
                    dp[i][j-1] + 1,    //插入word2[j-1]
                    dp[i-1][j-1] + 1,  //替换word1[i-1]为word2[j-1]
                });
            }
        }
    }
    return dp[n1][n2];
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

9. [插入区间](https://leetcode.cn/problems/insert-interval/)

> 给你一个 **无重叠的** *，*按照区间起始端点排序的区间列表 `intervals`，其中 `intervals[i] = [starti, endi]` 表示第 `i` 个区间的开始和结束，并且 `intervals` 按照 `starti` 升序排列。同样给定一个区间 `newInterval = [start, end]` 表示另一个区间的开始和结束。
> 在 `intervals` 中插入区间 `newInterval`，使得 `intervals` 依然按照 `starti` 升序排列，且区间之间不重叠（如果有必要的话，可以合并区间）。返回插入之后的 `intervals`。
> **注意** 你不需要原地修改 `intervals`。你可以创建一个新数组然后返回它。
```c++
输入：intervals = [[1,3],[6,9]], newInterval = [2,5]
输出：[[1,5],[6,9]]
    
输入：intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
输出：[[1,2],[3,10],[12,16]]
```

```c++
//只会插入一次
vector<vector<int>> insert(vector<vector<int>>& intervals, vector<int>& newInterval) {
    vector<vector<int>> res;
    int left = newInterval[0];     
    int right = newInterval[1];
    bool inplace = false;       //只会插入一次
    for(const auto& interval : intervals){
        if(interval[0] > right){       //插入的条件，
            if(!inplace){			   //之前没有被插入过	
                res.push_back({left, right});
                inplace = true;
            }
            res.push_back(interval);
        }
        else if(interval[1] < left)
            res.push_back(interval);
        //说明当前的interval和待插入的{left，right}有交集，更新插入区间
        else{
            left = min(left, interval[0]);    //更新左端
            right = max(right, interval[1]);  //更新右端
        }
    }
    // 如果 newInterval 还没有被插入到结果中，说明它在遍历结束后仍未被处理
    if(!inplace)
        res.push_back({left, right});
    return res;
}
```

10. [划分字母区间](https://leetcode.cn/problems/partition-labels/)

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

11. [监控二叉树](https://leetcode.cn/problems/binary-tree-cameras/)

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

```c++
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

        return 0;
    }
    int minCameraCover(TreeNode* root) {
        int count = 0;
        return dfs(root, count) == 1 ? count + 1 : count;
    }
  };
```

12. [无重叠区间](https://leetcode.cn/problems/non-overlapping-intervals/)

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



### 三、队列&&栈&&堆

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
>[1  3  -1] -3  5  3  6  7      3
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
        while(q.top().second + k<= i){    //需要循环！不只是if，从大到小清理优先队列中过期元素
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

5. [每日温度](https://leetcode.cn/problems/daily-temperatures/)

> 给定一个整数数组 `temperatures` ，表示每天的温度，返回一个数组 `answer` ，其中 `answer[i]` 是指对于第 `i` 天，下一个更高温度出现在几天后。如果气温在这之后都不会升高，请在该位置用 `0` 来代替。

```c++
输入: temperatures = [73,74,75,71,69,72,76,73]
输出: [1,1,4,2,1,1,0,0]
```

```c++
//利用栈存储 尚未遇到更大值 的元素值的下标，这样遇到更大值，则弹出求取差值即可。（栈中存储下标的温度值一定是递减，自栈底向上）
vector<int> dailyTemperatures(vector<int>& temperatures) {
    int n = temperatures.size();
    vector<int> res(n, 0);
    stack<int> s;
    for(int i = 0; i < n; i++){
        while(!s.empty() && temperatures[i] > temperatures[s.top()]){     //满足条件则一直循环！
            int temp = s.top(); s.pop();
            res[temp] = i - temp;       //求取temp的差值
        }
        s.push(i);
    }
    return res;
}
```

6. [数据流的中位数](https://leetcode.cn/problems/find-median-from-data-stream/)

> **中位数**是有序整数列表中的中间值。如果列表的大小是偶数，则没有中间值，中位数是两个中间值的平均值。
>
> - 例如 `arr = [2,3,4]` 的中位数是 `3` 。
> - 例如 `arr = [2,3]` 的中位数是 `(2 + 3) / 2 = 2.5` 。
>
> 实现 MedianFinder 类:
>
> - `MedianFinder() `初始化 `MedianFinder` 对象。
> - `void addNum(int num)` 将数据流中的整数 `num` 添加到数据结构中。
> - `double findMedian()` 返回到目前为止所有元素的中位数。与实际答案相差 `10-5` 以内的答案将被接受。

```c++
/*
过使用两个堆来高效地实现，一个大顶堆（存放较小一半的数）和一个小顶堆（存放较大一半的数）。
通过维护这两个堆，确保大顶堆和小顶堆的大小平衡（两者的大小之差不超过 1）
每次插入数字时，将其放入合适的堆，并且在堆的大小不平衡时进行调整。
每次查询中位数时，如果堆大小相同，取两个堆顶的平均值；否则，取较大堆的堆顶作为中位数。
2,3,4,5,6
大顶堆：[4, 3, 2] （存放较小的数）
小顶堆：[5, 6] （存放较大的数）
*/
class MedianFinder {
public:
    priority_queue<int, vector<int>, less<int>> qmin;       //大根堆
    priority_queue<int, vector<int>, greater<int>> qmax;    //小根堆
    MedianFinder() {
        
    }
    
    void addNum(int num) {
        if(qmin.empty() || num <= qmin.top()){    //因为先进入小根堆
            qmin.push(num);
            if(qmin.size() - qmax.size() > 1){   //小根堆最多比大根堆数量多1
                qmax.push(qmin.top());
                qmin.pop();
            }
        }
        else{
            qmax.push(num);
            if(qmax.size() - qmin.size() > 0){    //大根堆不会比小根堆多
                qmin.push(qmax.top());
                qmax.pop();
            }
        }
    }
    
    double findMedian() {
        if(qmin.size() > qmax.size())
            return qmin.top();
        else
            return (qmin.top() + qmax.top()) / 2.0;
    }
};
```

7. [最长有效括号](https://leetcode.cn/problems/longest-valid-parentheses/)

> 给你一个只包含 `'('` 和 `')'` 的字符串，找出最长有效（格式正确且连续）括号子串的长度。

```c++
输入：s = ")()())"
输出：4
```

```c++
//栈里存放的是括号的下标，栈底存放前一个无效匹配括号的下标，初始时栈中放 -1，表示前一个无效括号的索引。
int longestValidParentheses(string s) {
    int res = 0;
    stack<int> stk;
    stk.push(-1);
    for(int i = 0; i < s.size(); i++){
        if(s[i] == '('){    // 遇到'('，把下标存入栈中
            stk.push(i);
        }
        else{
            stk.pop();      // 遇到')'，弹出栈顶
            //pop()后需要先判空，后面会用到top()
            if(stk.empty()) 	
                stk.push(i);   // 如果栈为空，说明无匹配，存入当前')'的下标
            else
                res = max(res, i - stk.top());    // 计算有效括号长度
        }
    }
    return res;
}
```



### 四、数组

1. 二分查找
>
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
>
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
>   	 	[1,2,3],
>     		[8,9,4],
>   		[7,6,5]
> 	 ]
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

7. [和为k的子数组](https://leetcode.cn/problems/subarray-sum-equals-k/)

> 给你一个整数数组 `nums` 和一个整数 `k` ，请你统计并返回 *该数组中和为 `k` 的子数组的个数* 。
>
> 子数组是数组中元素的连续非空序列。

```c++
输入：nums = [1,2,3], k = 3
输出：2
```

```c++
int subarraySum(vector<int>& nums, int k) {
    int count = 0, pre = 0;         //pre前缀和，
    unordered_map<int,int> m;      //前缀和出现的次数,数组可能存在0、负数
    m[0] = 1;                     // 初始化前缀和为 0 , 方便sum[i] = k
    for(int num : nums){
        pre += num;
        if(m.find(pre - k) != m.end()){     //sum[i] - sum[j-1] = k(存在从j到i子数组和为k)
            count += m[pre - k];            //加针对当前num，加上前缀和为pre-k的次数
        }
        m[pre]++;                         //最后更新map
    }
    return count;
}
```

8. [最小覆盖子串](https://leetcode.cn/problems/minimum-window-substring/)

> 给你一个字符串 `s` 、一个字符串 `t` 。返回 `s` 中涵盖 `t` 所有字符的最小子串。如果 `s` 中不存在涵盖 `t` 所有字符的子串，则返回空字符串 `""` 。
>
> **注意：**
>
> - 对于 `t` 中重复字符，我们寻找的子字符串中该字符数量必须不少于 `t` 中该字符数量。
> - 如果 `s` 中存在这样的子串，我们保证它是唯一的答案。

```c++
输入：s = "ADOBECODEBANC", t = "ABC"
输出："BANC"
解释：最小覆盖子串 "BANC" 包含来自字符串 t 的 'A'、'B' 和 'C'。
```

```c++
string minWindow(string s, string t) {
    if(s.size() < t.size()) return "";
    
    string res = s + " ";         //增添 " ", 方便最后比较
    unordered_map<char, int> smap, tmap;     //分别用于存储字符出现的次数
    int correct = 0, left = 0;
    for(char c : t){
        tmap[c]++;
    }
    for(int i = 0; i < s.size();  i++){
        smap[s[i]]++;
        if(tmap[s[i]] >= smap[s[i]]) correct++;
        //当起始点重复，起始点开始右移，直到新的覆盖子串最左端
        while(left < i && smap[s[left]] > tmap[s[left]]){
            smap[s[left]]--;
            left++;
        }
        if(correct == t.size()){
            if(i - left + 1 <= res.size())     //当出现更短的覆盖子串
            	res = s.substr(left, i - left + 1);
        }
    }
    return res == s  + " " ? "" : res;
}
```

9. [移动零](https://leetcode.cn/problems/move-zeroes/)

> 给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。
>
> **请注意** ，必须在不复制数组的情况下原地对数组进行操作。

```c++
输入: nums = [0,1,0,3,12]
输出: [1,3,12,0,0]
```

```c++
//直接覆盖，不足的补0
void moveZeroes(vector<int>& nums) {
    int index = 0;
    for(int i = 0; i < nums.size(); i++){
        if(nums[i] != 0){
            nums[index] = nums[i];
            index++;
        }
    }
    for(int i = index; i < nums.size(); i++)
        nums[i] = 0;
}
```

```c++
//双指针，left要么和right指向同一个值； 要么一直指向0
void moveZeroes(vector<int>& nums) {
    int left = 0, right = 0;
    while(right < nums.size()){
        if(nums[right]){
            swap(nums[right], nums[left]);
            left++;
        }
        right++;
    }
}
```

10. [盛最多水的容器](https://leetcode.cn/problems/container-with-most-water/)

> 给定一个长度为 `n` 的整数数组 `height` 。有 `n` 条垂线，第 `i` 条线的两个端点是 `(i, 0)` 和 `(i, height[i])` 。
>
> 找出其中的两条线，使得它们与 `x` 轴共同构成的容器可以容纳最多的水。返回容器可以储存的最大水量。

```c++
输入：[1,8,6,2,5,4,8,3,7]
输出：49 
解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。
```

```c++
int maxArea(vector<int>& height) {
    int left = 0, right = height.size() - 1;
    int temp = 0, res = 0;
    while(left < right){
        temp = min(height[right], height[left]) * (right - left);
        res = max(res, temp);
        if(height[left] < height[right]) left++;
        else if(height[right] <= height[left]) right--;
    }
    return res;
}
```

11. [接雨水](https://leetcode.cn/problems/trapping-rain-water/)

> 给定 `n` 个非负整数表示每个宽度为 `1` 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

```c++
输入：height = [0,1,0,2,1,0,1,3,2,1,2,1]
输出：6
```

```c++
int trap(vector<int>& height) {
    int res = 0;
    int left = 0, right = height.size() - 1;
    int leftmax = 0, rightmax = 0;
    while(left < right){
        leftmax = max(leftmax, height[left]); //更新
        rightmax = max(rightmax, height[right]);
        if(height[left] < height[right]){
            res = res + (leftmax - height[left]);
            left++;
        } 
        else{
            res = res + (rightmax - height[right]);
            right--;
        }
    }
    return res;
}
```

12. [轮转数组](https://leetcode.cn/problems/rotate-array/)

> 给定一个整数数组 `nums`，将数组中的元素向右轮转 `k` 个位置，其中 `k` 是非负数。

```c++
输入: nums = [1,2,3,4,5,6,7], k = 3
输出: [5,6,7,1,2,3,4]
```

```c++
//空间复杂度为 O(1) 的 原地 算法

/*数组翻转
该方法基于如下的事实：当我们将数组的元素向右移动 k 次后，尾部 k mod n 个元素会移动至数组头部，其余元素向后移动 k mod n 个位置。

该方法为数组的翻转：我们可以先将所有元素翻转，这样尾部的 k mod n 个元素就被移至数组头部，然后我们再翻转 [0,k mod n −1] 区间的元素和 [k mod n, n−1] 区间的元素即能得到最后的答案。
*/
void reverse1(vector<int>& nums, int start, int end){
    while(start < end){
        swap(nums[start++], nums[end--]);
    }
}
void rotate(vector<int>& nums, int k) {
    int m = k % nums.size();
    reverse(nums.begin(), nums.end());
    reverse1(nums, 0, m-1);
    reverse1(nums, m, nums.size()-1);
}
```

13. [除自身以外数组的乘积](https://leetcode.cn/problems/product-of-array-except-self/)

> 给你一个整数数组 `nums`，返回 数组 `answer` ，其中 `answer[i]` 等于 `nums` 中除 `nums[i]` 之外其余各元素的乘积 。
>
> 题目数据 **保证** 数组 `nums`之中任意元素的全部前缀元素和后缀的乘积都在 **32 位** 整数范围内。
>
> 请 **不要使用除法，**且在 `O(n)` 时间复杂度内完成此题

```c++
输入: nums = [1,2,3,4]
输出: [24,12,8,6]
```

```c++
vector<int> productExceptSelf(vector<int>& nums) {
    int n = nums.size();
    vector<int> l(n, 0), r(n, 0);  //l 用于存储每个位置左边元素的乘积，r 用于存储每个位置右边元素的乘积
    vector<int> res(n);
    l[0] = 1, r[n - 1] = 1;
    for(int i = 1; i < n; i++)
        l[i] = l[i-1] * nums[i-1];    // 当前元素的左乘积 = 前一个元素的左乘积 * 前一个元素的值
    for(int j = n-2; j >= 0; j--)
        r[j] = r[j+1] * nums[j+1];   // 当前元素的右乘积 = 后一个元素的右乘积 * 后一个元素的值
    for(int i = 0; i < n; i++){
        res[i] = l[i] * r[i];     //最终结果：res[i] = l[i] * r[i]，即 res[i] 是除去自身元素后左边和右边的乘积
    } 
    return res;
}
```

13. [最长回文子串](https://leetcode.cn/problems/longest-palindromic-substring/)

> 给你一个字符串 `s`，找到 `s` 中最长的 回文子串。

```c++
输入：s = "babad"
输出："bab"
```

```c++
pair<int, int> palindromeHelper(string s, int left, int right){
    while(left >= 0 && right < s.size()){
        if(s[left] == s[right]){
            left--;
            right++;
        }
        else
            break;
    }
    return {left+1, right-1};  //注意，此时的left和right已经是不满足条件的，需要移动
}
string longestPalindrome(string s) {
    int start = 0, end = 0;
    for(int i = 1; i < s.size(); i++){
        pair<int, int> a1 = palindromeHelper(s, i, i);
        pair<int, int> a2 = palindromeHelper(s, i, i-1);
        if(a1.second - a1.first > end - start){
            start = a1.first;
            end = a1.second;
        } 
        if(a2.second - a2.first > end - start){
            start = a2.first;
            end = a2.second;
        } 
    }
    return s.substr(start, end - start + 1);
}
```

14. [最大数](https://leetcode.cn/problems/largest-number/)

> 给定一组非负整数 `nums`，重新排列每个数的顺序（每个数不可拆分）使之组成一个最大的整数。
>
> **注意：**输出结果可能非常大，所以你需要返回一个字符串而不是整数。

```c++
输入：nums = [10,2]
输出："210"
输入：nums = [3,30,34,5,9]
输出："9534330" 
```

```c++
class Solution {
public:
    //定义新的排序规则，
    static bool comp(const int& a, const int& b){ //将成员函数设为 static,static 函数与类实例无关，因此可以直接通过类名或直接传递给 sort。(非静态成员函数是与具体对象绑定的，因此必须通过对象来调用它们。)
        return to_string(a) + to_string(b) > to_string(b) + to_string(a);
    }
    string largestNumber(vector<int>& nums) {
        string res;
        sort(nums.begin(), nums.end(), comp);
        if(nums[0] == 0) return "0";
        for(int& num : nums)
            res = res + to_string(num);
        return res;
    }
};
```

15. [缺失的第一个正数](https://leetcode.cn/problems/first-missing-positive/)

> 给你一个未排序的整数数组 `nums` ，请你找出其中没有出现的最小的正整数。
>
> 请你实现时间复杂度为 `O(n)` 并且只使用常数级别额外空间的解决方案。

```c++
输入：nums = [3,4,-1,1]
输出：2
输入：nums = [7,8,9,11,12]
输出：1
```

注：使用原地哈希的方法，将每个正整数 `x` 放到索引 `x-1` 的位置上（放到正确位置上）

1. 遍历数组，将每个正整数 `nums[i]` 移动到索引 `nums[i] - 1` 的位置。

2. 在移动的过程中，忽略掉不在范围内的数（例如小于 1 或大于数组长度的数）。

3. 再次遍历数组，找到第一个索引 `i`，使得 `nums[i]` 不等于 `i + 1`，则 `i + 1` 就是缺失的最小正整数。

4. 如果所有的索引都符合条件，返回 `n + 1`，其中 `n` 是数组的长度。

```c++
int firstMissingPositive(vector<int>& nums) {
    int n = nums.size();
    for(int i = 0; i < n; i++){
        //nums[nums[i] - 1] != nums[i],出现重复数字，已有在正确位置上则无需重排
        while(nums[i] > 0  && nums[i] <= n && nums[nums[i] - 1] != nums[i])  //使用while循环遍历！
            swap(nums[i], nums[nums[i] - 1]);
    }
    for(int i = 0; i < n; i++){
        if(nums[i] != i + 1)
            return i+1;
    }
    return n+1;  //如果所有的索引都符合条件，返回 n + 1
}
```



2. 排序

   - 快速排序
   
     ```c++
     // 分区函数，返回基准值的正确下标; 每次能把基准值放到正确位置
     int partition(vector<int>& arr, int left, int right){
         int pivot = arr[left]; //选择最左边数作为基准
         while(left < right){
             // 注意先从右开始！！！选择的最左边数作为基准
             // 从右往左寻找一个比 pivot 小的数，找到后将这个数放在 left 位置
             while(left < right && arr[right] >= pivot) right--;
             arr[left] = arr[right];
             // 从左往右寻找一个比 pivot 大的数，找到后将这个数放在 right 位置
             while(left < right && arr[left] <= pivot) left++;
             arr[right] = arr[left];
         }
         //把 pivot 放回正确位置
         arr[left] = pivot;
         return left;    //返回基准值的正确下标
     }
     
     //递归
     void quickSort(vector<int> arr, left, right){
         if(left >= right) return;
         int pivot = partition(arr, left, right);    // 获取基准位置
         quickSort(arr, left, pivot-1);    // 递归排序基准左侧
         quickSort(arr, pivot+1, right);   // 递归排序基准右侧
     }
     
     //非递归，使用栈
     void quickSort(vector<int> arr, left, right){
         stack<pair<int, int>> stk;   // 创建一个栈，用来存储子数组的左右边界
         stk.push({left, right});     // 初始压入整个数组的边界
         while(!stk.empty()){
             auto [l,r] = stk.top(); stk.pop();  // 取出当前子数组的左右边界
             if(l < r){
                 int pivot = partition(arr, l, r);   // 进行分区操作，得到基准元素的位置
                 stk.push(arr, l, pivot-1);  // 将左边子数组的边界压入栈
                 stk.push(arr, pivot+1, r);  // 将右边子数组的边界压入栈
             }
         }
     }
     ```
   
     
   
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
    first->next = swapPairs(third)
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
    while(ha != hb){  //ha结点==hb结点 或者 均为空（同时到达尾部 或 拼接后的尾部）
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

6. [环形链表 II](https://leetcode.cn/problems/linked-list-cycle-ii/)

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
        while(fast && fast->next) {
            slow = slow->next;
            fast = fast->next->next;
            // 快慢指针相遇，此时从head 和 相遇点，同时查找直至相遇
            if (slow == fast) {
                ListNode* temp = head;
                while (temp != slow) {
                    temp = temp->next;
                    slow = slow->next;
                }
                return temp; // 返回环的入口
            }
        }
        return nullptr;
    }
};

```

7. [两数相加](https://leetcode.cn/problems/add-two-numbers/)

> 给你两个 **非空** 的链表，表示两个非负的整数。它们每位数字都是按照 **逆序** 的方式存储的，并且每个节点只能存储 **一位** 数字。
>
> 请你将两个数相加，并以相同形式返回一个表示和的链表。
>
> 你可以假设除了数字 0 之外，这两个数都不会以 0 开头

```c++
输入：l1 = [2,4,3], l2 = [5,6,4]
输出：[7,0,8]
解释：342 + 465 = 807.
```

```c++
ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
    int carry= 0;
    ListNode *dummy = new ListNode(0),*curr = dummy;
    while(l1 || l2){
        int a = l1 ? l1->val : 0;
        int b = l2 ? l2->val : 0;
        ListNode *node = new ListNode((a + b + carry) % 10);
        curr->next = node;
        curr = curr->next;
        carry = (a + b + carry) / 10 ;
        if(l1) l1 = l1->next;
        if(l2) l2 = l2->next;
    }
    if(carry) curr->next = new ListNode(carry);      //注意最后的进位
    return dummy->next;
}
```

8. [排序链表](https://leetcode.cn/problems/sort-list/)

> 给你链表的头结点 `head` ，请将其按 **升序** 排列并返回 **排序后的链表** .
>
> 你可以在 `O(n log n)` 时间复杂度和常数级空间复杂度下，对链表进行排序吗？

```c++
输入：head = [-1,5,3,4,0]
输出：[-1,0,3,4,5]
```

```c++
//合并两个有序链表
ListNode* merge(ListNode* l1, ListNode* l2){
    ListNode *dummy = new ListNode(0);
    ListNode *curr = dummy;
    while(l1 && l2){
        if(l1->val < l2->val){
            curr->next = l1;
            l1 = l1->next;
        }
        else{
            curr->next = l2;
            l2 = l2->next;
        }
        curr = curr->next;
    }
    curr->next = l1 ? l1 : l2;
    return dummy->next;
}
//归并排序
ListNode* sortList(ListNode* head) {
    if(head == nullptr || head->next == nullptr)
        return head;
    ListNode *slow = head;
    ListNode *fast = head->next;
    while(fast && fast->next){
        slow = slow->next;
        fast = fast->next->next;
    }
    ListNode *head2 = slow->next; //分割链表，第二个链表的头结点
    slow->next = nullptr;  //分割链表！第一个链表尾指针指向nullptr
    return merge(sortList(head), sortList(head2));
}
```

9. [合并K个升序链表](https://leetcode.cn/problems/merge-k-sorted-lists/)

> 给你一个链表数组，每个链表都已经按升序排列。
>
> 请你将所有链表合并到一个升序链表中，返回合并后的链表。

```c++
输入：lists = [[1,4,5],[1,3,4],[2,6]]
输出：[1,1,2,3,4,4,5,6]
```

```c++
class Solution {
public:
    //合并两个有序链表
    ListNode* mergeTwoList(ListNode* l1, ListNode* l2){
        ListNode *dummy = new ListNode(0);
        ListNode *curr = dummy;
        while(l1 && l2){
            if(l1->val < l2->val){
                curr->next = l1;
                l1 = l1->next;
            }
            else{
                curr->next = l2;
                l2 = l2->next;
            }
            curr = curr->next;
        }
        curr->next = l1 ? l1 : l2;
        return dummy->next;
    }
    // 辅助递归函数，分治法合并链表
    ListNode* mergeListsHelper(vector<ListNode*>& lists, int left, int right){
        if(left == right) return lists[left];
        int mid = left + (right - left) / 2;
        ListNode* l1 = mergeListsHelper(lists, left, mid);
        ListNode* l2 = mergeListsHelper(lists, mid+1, right);
        return mergeTwoList(l1, l2);
    }
    // 递归分治法合并K个有序链表
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        if (lists.empty()) return nullptr;
        return mergeListsHelper(lists, 0, lists.size() - 1);
    }
};
```

10. K个一组翻转链表

> 给你链表的头节点 `head` ，每 `k` 个节点一组进行翻转，请你返回修改后的链表。
>
> `k` 是一个正整数，它的值小于或等于链表的长度。如果节点总数不是 `k` 的整数倍，那么请将最后剩余的节点保持原有顺序。
>
> 你不能只是单纯的改变节点内部的值，而是需要实际进行节点交换。

```c++
输入：head = [1,2,3,4,5], k = 2
输出：[2,1,4,3,5]
```

```c++
ListNode* reverseKGroup(ListNode* head, int k) {
    ListNode *temp = head;

    // Step 1: 检查链表剩余的节点是否少于 k 个
    for (int i = 0; i < k; i++) {
        if (temp == nullptr) {
            return head; // 如果剩余的节点不足 k 个，直接返回 head（不翻转）
        }
        temp = temp->next; // temp 向后移动到第 k+1 个节点
    }

    // Step 2: 递归调用 reverseKGroup(temp, k) 翻转后续的链表
    // `pre` 是下一个翻转组的头结点（已经翻转好）
    ListNode *pre = reverseKGroup(temp, k);

    // Step 3: 翻转当前 k 个节点
    // 当前 head 是要翻转的这组的第一个节点
    // `pre` 是翻转后剩余部分的头结点
    for (int i = 0; i < k; i++) {
        ListNode *nextnode = head->next;   // 临时保存下一个节点
        head->next = pre;    // 翻转，将当前节点指向上一个节点（pre）
        pre = head;          // 移动 pre，使其指向当前节点
        head = nextnode;         // 移动 head，处理下一个节点
    }

    // Step 4: 返回翻转后的头节点
    return pre;
}
```

11. [回文链表](https://leetcode.cn/problems/palindrome-linked-list/)

> 给你一个单链表的头节点 `head` ，请你判断该链表是否为回文链表。如果是，返回 `true` ；否则，返回 `false` 。

```c++
输入：head = [1,2,2,1]
输出：true
```

```c++
//遍历链表使用数组存储，再双指针遍历验证 O(n) O(n)
// bool isPalindrome(ListNode* head) {
//     vector<int> nums;
//     while(head){
//         nums.push_back(head->val);
//         head = head->next;
//     }
//     for(int left = 0, right = nums.size() - 1; left < right; left++, right--){
//         if(nums[left]!=nums[right]) return false;
//     }
//     return true;
// }

//翻转后半截链表，再遍历验证 O(n) O(1)
bool isPalindrome(ListNode* head) {
    if(head == nullptr || head->next == nullptr) return true;
    ListNode *interNode= findInterNode(head);
    ListNode *secondList = reverseList(interNode->next);
    ListNode *l1 = head, *l2 = secondList;
    while(l2){      //l2可能会比l1少一个结点（中间节点），使用l2判空即可，忽略中间节点
        if(l1->val != l2->val) return false;
        l1 = l1->next;
        l2 = l2->next;
    }
    return true;
}

ListNode* findInterNode(ListNode* head){
    ListNode* slow = head;
    ListNode* fast = head->next;
    while(fast && fast->next){
        slow = slow->next;
        fast = fast->next->next;
    }
    return slow;
}
ListNode* reverseList(ListNode* head){
    ListNode* pre = nullptr;
    ListNode* curr = head;
    while(curr){
        ListNode *nextnode = curr->next;
        curr->next = pre;
        pre = curr;
        curr = nextnode;
    }
    return pre;
}
```

12. [二叉树展开为链表](https://leetcode.cn/problems/flatten-binary-tree-to-linked-list/)

> 给你二叉树的根结点 `root` ，请你将它展开为一个单链表：
>
> - 展开后的单链表应该同样使用 `TreeNode` ，其中 `right` 子指针指向链表中下一个结点，而左子指针始终为 `null` 。
> - 展开后的单链表应该与二叉树 [**先序遍历**](https://baike.baidu.com/item/先序遍历/6442839?fr=aladdin) 顺序相同。

```c++
输入：root = [1,2,5,3,4,null,6]
输出：[1,null,2,null,3,null,4,null,5,null,6]
```

```c++
//O(n) O(1)
//对当前节点，如果存在左孩子，那么根据先序遍历后，右孩子会被替换为左孩子，那么右孩子的父节点变成了 原先左孩子中最后访问的（最右端）
void flatten(TreeNode* root) {
    TreeNode *curr = root;
    while(curr){
        if(curr->left){
            TreeNode *preRight = curr->right;
            TreeNode *preLeft = curr->left;
            TreeNode *temp = preLeft;
            while(temp->right){
                temp = temp->right;  //找到当前右孩子的前驱结点
            }
            temp->right = preRight;  //放好右孩子位置，腾出右子节点位置
            curr->right = preLeft;   //左孩子替换右孩子
            curr->left = nullptr;    //左子节点置空
        }
        curr = curr->right;  //下一个节点，也就是先序遍历的下一个节点
    }
}
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
int fourSumCount(vector<int>& nums1, vector<int>& nums2, vector<int>& nums3, vector<int>& nums4) {
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
                res += it->second;    //很重要，不是加一
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

5. [字母异位词分组](https://leetcode.cn/problems/group-anagrams/)

> 给你一个字符串数组，请你将 **字母异位词** 组合在一起。可以按任意顺序返回结果列表。**字母异位词** 是由重新排列源单词的所有字母得到的一个新单词。

```c++
输入: strs = ["eat", "tea", "tan", "ate", "nat", "bat"]
输出: [["bat"],["nat","tan"],["ate","eat","tea"]]
```

```c++
vector<vector<string>> groupAnagrams(vector<string>& strs) {
    unordered_map<string, vector<string>> m;    //有序串为键，同属一个有序串为值
    for(auto it : strs){
        string key = it;
        sort(key.begin(), key.end());         //构建有序串
        m[key].push_back(it);
    }
    vector<vector<string>> res;
    for(auto it : m){
        res.push_back(it.second);
    }
    return res;
}
```

6. [最长连续序列](https://leetcode.cn/problems/longest-consecutive-sequence/)

> 给定一个未排序的整数数组 `nums` ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。
>
> 请你设计并实现时间复杂度为 **`O(n)`** 的算法解决此问题。

```c++
输入：nums = [0,3,7,2,5,8,4,6,0,1]
输出：9
```

```c++
//考虑枚举数组中的每个数 x，考虑以其为起点，不断尝试匹配 x+1,x+2,⋯ 是否存在
//重点是确定 起点！即num-1不在nums中。
int longestConsecutive(vector<int>& nums) {
    unordered_set<int> s;          //使用集合存储，用于去重
    int longest = 0;
    int curr = 0;
    for(int num : nums){
        s.insert(num);
    }
    for(int num : s){
        if(s.count(num-1)){     //说明不是开头的
            curr = 1;
        }
        else{
            curr = 1;
            int temp = num;         //从开头开始数
            while(s.count(temp + 1)){
                curr++;
                temp++;
            }
        }
        longest = max(longest, curr);     //更新
    }
    return longest;
}
```

7. [无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)

> 给定一个字符串 `s` ，请你找出其中不含有重复字符的最长子串的长度。

```c++
输入: s = "abcabcbb"
输出: 3
```

```c++
int lengthOfLongestSubstring(string s) {
    int res = 0;
    unordered_map<char, int> m;       //每个字符上次出现的位置
    int start = 0;
    for(int i = 0; i < s.size(); i++){
        if(m.count(s[i]) && m[s[i]] >= start){    //必须注意m[s[i]] >= start，start已经更新！只在乎start之后的字符
            start = m[s[i]] + 1;
        }
        m[s[i]] = i;
        res = max(res, i - start + 1);
    }
    return res;
}
```

8. [找到字符串中所有字母异位词](https://leetcode.cn/problems/find-all-anagrams-in-a-string/)

> 给定两个字符串 `s` 和 `p`，找到 `s` 中所有 `p` 的 **异位词** 的子串，返回这些子串的起始索引。不考虑答案输出的顺序。
>
> **异位词** 指由相同字母重排列形成的字符串（包括相同的字符串）。

```c++
输入: s = "cbaebabacd", p = "abc"
输出: [0,6]
解释:
起始索引等于 0 的子串是 "cba", 它是 "abc" 的异位词。
起始索引等于 6 的子串是 "bac", 它是 "abc" 的异位词。
```

```c++
//通过使用滑动窗口和字符频率计数来优化异位词检测。
vector<int> findAnagrams(string s, string p) {
    vector<int> res;
    int n = s.size(), ps = p.size();
    
    if(n < ps) return {};
    vector<int> target(26,0), temp(26, 0);  //存储字符频率
    // 初始化 p 的字符计数
    for(char c : p)
        target[c - 'a']++;
    // 初始化 s 的前 ps 个字符的计数
    for(int i = 0;  i < ps; i++)
        temp[s[i] - 'a']++;
    if(target == temp) res.push_back(0);

    for(int i = ps; i < n; i++){
        // 移动窗口，更新计数
        temp[s[i] - 'a']++;    // 加入新的字符
        temp[s[i-ps] - 'a']--;  // 移除左边界的字符
        if(temp == target) res.push_back(i-ps+1); // 如果当前窗口的计数与 p 的计数相同，则记录结果
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
        //当前结点+递归左右子节点！
        return abs((height(root->left) - height(root->right))) <= 1 && isBalanced(root->left) && isBalanced(root->right);  
}
```

5. [验证二叉搜索树](https://leetcode.cn/problems/validate-binary-search-tree/)

> 给你一个二叉树的根节点 `root` ，判断其是否是一个有效的二叉搜索树。
>
> **有效** 二叉搜索树定义如下：
>
> - 节点的左子树只包含 小于 当前节点的数。
> - 节点的右子树只包含 **大于** 当前节点的数。
> - 所有左子树和右子树自身必须也是二叉搜索树。

```c++
//中序遍历是由小到大
bool isValidBST(TreeNode* root) {
    stack<TreeNode*> s;
    TreeNode *curr = root;
    long long pre = LLONG_MIN;      //记录前一个值
    while(!s.empty() || curr){
        while(curr){
            s.push(curr);
            curr = curr->left;
        }
        curr = s.top(); s.pop();
        
        if(curr->val <= pre) return false;   //每次和前一个值进行比较
        pre = curr->val;                     //更新
        curr = curr->right;
    }
    return true;
}
```

6. [从前序与中序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

> 给定两个整数数组 `preorder` 和 `inorder` ，其中 `preorder` 是二叉树的**先序遍历**， `inorder` 是同一棵树的**中序遍历**，请构造二叉树并返回其根节点。

```c++
输入: preorder = [3,9,20,15,7], inorder = [9,3,15,20,7]
输出: [3,9,20,null,null,15,7]
```

```c++
unordered_map<int, int> inorder_map; //使用哈希表来帮助我们快速地定位根节点,键表示中序序列值，值表示其在中序序列中的位置，
TreeNode* helper(vector<int>& preorder, vector<int>& inorder, int pre_left, int pre_right, int in_left, int in_right){
    if(pre_left > pre_right || in_left > in_right) return nullptr;
    // 先序遍历的第一个元素是当前子树的根节点
    int root_val = preorder[pre_left];
    TreeNode* root = new TreeNode(root_val);
     // 找到根节点在中序遍历中的位置
    int root_index_inorder = inorder_map[root_val];
    // 计算左子树的大小
    int left_size = root_index_inorder - in_left;
    // 递归构造左子树
    root->left = helper(preorder, inorder, pre_left+1, pre_left+left_size, in_left, root_index_inorder-1);
    // 递归构造右子树
    root->right = helper(preorder, inorder, pre_left+left_size+1, pre_right, root_index_inorder+1, in_right);
    return root;
}
TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
    int n = preorder.size();
    for(int i = 0; i < n; i++){
        inorder_map[inorder[i]] = i;   //构建哈希表，快速定位中序序列中根节点位置，无需顺序遍历
    }
    return helper(preorder, inorder, 0, n-1, 0, n-1);
}
```

7. [路径总和 III](https://leetcode.cn/problems/path-sum-iii/)

> 给定一个二叉树的根节点 `root` ，和一个整数 `targetSum` ，求该二叉树里节点值之和等于 `targetSum` 的 **路径** 的数目。
>
> **路径** 不需要从根节点开始，也不需要在叶子节点结束，但是路径方向必须是向下的（只能从父节点到子节点）。

```c++
输入：root = [10,5,-3,3,2,null,11,3,-2,null,1], targetSum = 8
输出：3
```

```c++
/*
如果在某条路径上，当前节点的前缀和为 currSum，那么路径上任何连续一段节点的和为 targetSum，即 currSum - targetSum 出现的次数就能表明当前节点之前的某个位置到当前节点的路径和等于 targetSum。
通过记录每一个前缀和出现的次数来快速计算有多少个符合条件的路径<哈希表>。
*/

int dfs(TreeNode* root, int targetSum, unordered_map<long long,int>& prefix, long long currsum){
    if(root == nullptr) return 0;
    currsum += root->val;  // 更新当前的前缀和
    int res = prefix[currsum - targetSum]; // 计算从当前节点到前面的某个节点的路径和是否等于 targetSum
     // 更新前缀和哈希表
    prefix[currsum]++;
    // 递归遍历左右子树
    res += dfs(root->left, targetSum, prefix, currsum);
    res += dfs(root->right, targetSum, prefix, currsum);
    // 回溯：撤销当前节点对前缀和的影响
    prefix[currsum]--;
    return res;
}
int pathSum(TreeNode* root, int targetSum) {
    // 定义一个哈希表 prefixSumCount，其中键表示前缀和，值表示该前缀和出现的次数。
    unordered_map<long long, int> prefix;
    // 初始情况下，前缀和为 0 的情况出现 1 次，即空路径。
    prefix[0] = 1;
    return dfs(root, targetSum, prefix, 0);
}
```

8. [二叉树中的最大路径和](https://leetcode.cn/problems/binary-tree-maximum-path-sum/)

> 二叉树中的 **路径** 被定义为一条节点序列，序列中每对相邻节点之间都存在一条边。同一个节点在一条路径序列中 **至多出现一次** 。该路径 **至少包含一个** 节点，且不一定经过根节点。**路径和** 是路径中各节点值的总和。
>
> 给你一个二叉树的根节点 `root` ，返回其 **最大路径和** 。

```c++
输入：root = [-10,9,20,null,null,15,7]
输出：42
```

```c++
/*
对于每个节点，路径有几种可能：
- 仅包含当前节点。
- 包含当前节点和左子树的最大路径。
- 包含当前节点和右子树的最大路径。
- 包含当前节点、左子树和右子树的路径。
我们的目标是找到包含任意节点的路径和的最大值。递归函数的返回值需要计算当前节点的最大贡献值，即：该节点加上它的左子树或右子树的最大路径和，而不允许同时包含左右子树（因为这样路径就分叉了，不再是一条路径）。在递归过程中，我们还需要记录全局的最大路径和。
*/
int maxpoint(TreeNode* root, int& res){
    if(root == nullptr) return 0;
    
    // 递归计算左右子树的最大贡献值，负数的贡献值取0
    int leftpoint = max(maxpoint(root->left, res), 0);
    int rightpoint = max(maxpoint(root->right, res), 0);
    
    // 计算当前节点的路径和（包括当前节点、左子树和右子树）
    int currpath = root->val + leftpoint + rightpoint;
    // 更新全局最大路径和!!!
    res = max(res, currpath);
    
    // 返回当前节点的最大贡献值（只能选择左子树或右子树的一条路径）
    return root->val + max(leftpoint, rightpoint);
}
int maxPathSum(TreeNode* root) {
    int res = INT_MIN;
    maxpoint(root, res);
    return res;
}
```

9. [ 二叉树的直径](https://leetcode.cn/problems/diameter-of-binary-tree/)

> 给你一棵二叉树的根节点，返回该树的 **直径** 。
>
> 二叉树的 **直径** 是指树中任意两个节点之间最长路径的 **长度** 。这条路径可能经过也可能不经过根节点 `root` 。
>
> 两节点之间路径的 **长度** 由它们之间边数表示。

```c++
输入：root = [1,2,3,4,5]
输出：3
解释：3 ，取路径 [4,2,1,3] 或 [5,2,1,3] 的长度。
```

```c++
int lenPoint(TreeNode* node, int& res){
    if(node == nullptr) return 0;
    int leftpoint = lenPoint(node->left, res);
    int rightpoint = lenPoint(node->right, res);
    int currlen = leftpoint + rightpoint + 1;
    res = max(res, currlen);
    return max(leftpoint, rightpoint) + 1;
}
int diameterOfBinaryTree(TreeNode* root) {
    int res = 1;
    lenPoint(root, res);
    return res-1;
}
```

10. [二叉树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/)

> 给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

```c++
输入：root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
输出：5
```

```c++
TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
    // 基本情况：如果当前节点为空，或者当前节点等于p或q（只要找到一个就立即返回当前节点，不再搜索）
    if(root == nullptr || root == p || root == q)
        return root;
    // 在左子树和右子树中查找p和q
    TreeNode *leftnode = lowestCommonAncestor(root->left, p, q);
    TreeNode *rightnode = lowestCommonAncestor(root->right, p, q);
    // 如果左子树和右子树都找到（只可能各有一个），当前节点是最近公共祖先
    if(leftnode && rightnode)
        return root;
    // 如果只在一侧找到，返回该侧的节点
    return leftnode ? leftnode : rightnode;
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

11. [括号生成](https://leetcode.cn/problems/generate-parentheses/)

> 数字 `n` 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 **有效的** 括号组合。

```c++
输入：n = 3
输出：["((()))","(()())","(())()","()(())","()()()"]
```

```c++
//使用回溯法来生成所有有效的括号组合。核心思想是通过递归尝试在不同位置添加左括号 ( 和右括号 )，并且保持生成的括号序列有效。
void backtrack(vector<string>& res, string& temp, int left, int right, int n){ //left,right记录左右括号的数量
    if(temp.size() == 2 * n)
        res.push_back(temp);
    // 如果左括号数量小于 n，则可以继续添加左括号
    if(left < n){
        temp.push_back('(');
        backtrack(res, temp, left + 1, right, n);
        temp.pop_back();
    }
    // 如果右括号数量小于左括号数量，则可以添加右括号!!!
    if(right < left){
        temp.push_back(')');
        backtrack(res, temp, left, right + 1, n);
        temp.pop_back();
    }
}
vector<string> generateParenthesis(int n) {
    vector<string> res;
    string temp;
    backtrack(res, temp, 0, 0, n);
    return res;
}
```

12. [单词搜索](https://leetcode.cn/problems/word-search/)

> 给定一个 `m x n` 二维字符网格 `board` 和一个字符串单词 `word` 。如果 `word` 存在于网格中，返回 `true` ；否则，返回 `false` 。
>
> 单词必须按照字母顺序，通过相邻单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。

```c++
输入：board = 
[
    ["A","B","C","E"],
    ["S","F","C","S"],
    ["A","D","E","E"]
], word = "ABCCED"
输出：true
```

```c++

class Solution {
public:
    vector<vector<int>> dirs = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
    bool backtrack(vector<vector<char>>& board, string& word, vector<vector<bool>>& visited, int i, int j, int index){
        if(index == word.size()) // 如果当前字符的索引已经等于 word 的长度，说明找到了整个单词
            return true;
        if(i < 0 || i >= board.size() || j < 0 || j >= board[0].size() || board[i][j] != word[index] || visited[i][j])
            return false;
        visited[i][j] = true;
        for(const auto& dir : dirs){ //const：不能修改 dir，提高安全性。&：避免拷贝，提高性能，直接访问元素
            int x = i + dir[0];
            int y = j + dir[1];
            if(backtrack(board, word, visited, x, y, index+1))
                return true;  
        }
        visited[i][j] = false;
        return false;
    }
    bool exist(vector<vector<char>>& board, string word) {
        int m = board.size(), n = board[0].size();
        vector<vector<bool>> visited(m, vector<bool>(n, false));
         // 遍历每个格子，作为 DFS 的起点
        for(int i = 0; i < m; i++){
            for(int j = 0; j < n; j++){
                if(backtrack(board, word, visited, i, j, 0)){
                    return true;
                } 
            }
        }
        return false;
    }
};
```



### 九、矩阵

1. 岛屿数量

> 给你一个由 `'1'`（陆地）和 `'0'`（水）组成的的二维网格，请你计算网格中岛屿的数量。岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。此外，你可以假设该网格的四条边均被水包围。

```c++
输入：grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
输出：3
```

```c++
void dfs(vector<vector<char>>& matrix, int i, int j){
	if(i < 0 || i >=matrix.size() || j < 0 || j >= matrix[0].size()) //返回条件一：超出边界
		return;
	if(matrix[i][j] == '0')     //返回条件二：非岛屿，跳出递归
		return;
	matrix[i][j] = '0';        //表示岛屿已被访问
	dfs(matrix, i+1, j);
	dfs(matrix, i-1, j);
	dfs(matrix, i, j+1);
	dfs(matrix, i, j-1);
}

int main(){
	int n, m;
	int count = 0;
	cin >> n >> m;
	vector<vector<char>> matrix(n, vector<char>(m, '0')); 
	for(int i = 0; i < n; i++){
		for(int j = 0; j < n; j++){
			cin >> matrix[i][j];
		}
	}
	
	for(int i = 0; i < matrix.size(); i++){
		for(int j = 0; j < matrix[0].size(); j++){
			if(matrix[i][j] == '1'){
				dfs(matrix, i , j);
				count++;        //每次新的深搜表示找到新的岛屿
			}
		}
	}
	cout << count << endl;
	return 0;
}  //返回条件一：超出边界
```

2. [封闭岛屿的数量](https://leetcode.cn/problems/number-of-closed-islands/)

> 二维矩阵 `grid` 由 `0` （土地）和 `1` （水）组成。岛是由最大的4个方向连通的 `0` 组成的群，封闭岛是一个 `完全` 由1包围（左、上、右、下）的岛。
>
> 请返回 *封闭岛屿* 的数目。

```c++
输入：grid = [
    [0,0,1,0,0],
    [0,1,0,1,0],
    [0,1,1,1,0]
]
输出：1
```

```c++
//先从四条边开始，将所有非封闭的陆地排除（设为水），再重新遍历剩下的陆地（就是孤岛）
class Solution {
public:
    void dfs(vector<vector<int>>& matrix, int i, int j) {
        if (i < 0 || i >= matrix.size() || j < 0 || j >= matrix[0].size())
            return;
        if (matrix[i][j] == 1)
            return;
        matrix[i][j] = 1;
        dfs(matrix, i + 1, j);
        dfs(matrix, i - 1, j);
        dfs(matrix, i, j + 1);
        dfs(matrix, i, j - 1);
    }
    int closedIsland(vector<vector<int>>& matrix) {
        int m = matrix.size(), n = matrix[0].size();
        int count = 0;
        for (int i = 0; i < m; i++) {
            if (matrix[i][0] == 0)
                dfs(matrix, i, 0);
        }
        for (int i = 0; i < m; i++) {
            if (matrix[i][n - 1] == 0)
                dfs(matrix, i, n - 1);
        }
        for (int j = 0; j < n; j++) {
            if (matrix[0][j] == 0)
                dfs(matrix, 0, j);
        }
        for (int j = 0; j < n; j++) {
            if (matrix[m - 1][j] == 0)
                dfs(matrix, m - 1, j);
        }
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == 0) {
                    dfs(matrix, i, j);
                    count++;
                }
            }
        }
        return count;
    }
};

```

3. [岛屿的周长](https://leetcode.cn/problems/island-perimeter/)

> 给定一个 `row x col` 的二维网格地图 `grid` ，其中：`grid[i][j] = 1` 表示陆地， `grid[i][j] = 0` 表示水域。
>
> 网格中的格子 **水平和垂直** 方向相连（对角线方向不相连）。整个网格被水完全包围，但其中恰好有一个岛屿（或者说，一个或多个表示陆地的格子相连组成的岛屿）。

```c++
输入：
grid = [
    [0,1,0,0],
    [1,1,1,0],
    [0,1,0,0],
    [1,1,0,0]
]
输出：16

输入：grid = [[1]]
输出：4
```

```c++
int islandPerimeter(vector<vector<int>>& grid) {
    int m = grid.size(), n = grid[0].size();
    int count = 0;
    for(int i = 0; i < m; i++){
        for(int j = 0; j < n; j++){
            if(grid[i][j] == 1){
                int origin = 4;      //默认每个岛初始周长都为4，但凡在某个方向和另一个岛屿有接触，那么边长就减一
                if(i > 0 && grid[i-1][j] == 1) origin--;
                if(i < m-1 && grid[i+1][j] == 1) origin--;
                if(j > 0 && grid[i][j-1] == 1) origin--;
                if(j < n-1 && grid[i][j+1] == 1) origin--;
                count = count + origin;
            }
        }
    }
    return count;
}
```

3. [最大正方形](https://leetcode.cn/problems/maximal-square/)

> 在一个由 `'0'` 和 `'1'` 组成的二维矩阵内，找到只包含 `'1'` 的最大正方形，并返回其面积。

```c++
输入：matrix = [
    ["1","0","1","0","0"],
    ["1","0","1","1","1"],
    ["1","1","1","1","1"],
    ["1","0","0","1","0"]
]
输出：4
```

可以使用动态规划降低时间复杂度。我们用 dp(i,j) 表示以 (i,j) 为右下角，且只包含 1 的正方形的边长最大值。如果我们能计算出所有 dp(i,j) 的值，那么其中的最大值即为矩阵中只包含 1 的正方形的边长最大值，其平方即为最大正方形的面积。

那么如何计算 dp 中的每个元素值呢？对于每个位置 (i,j)，检查在矩阵中该位置的值：

- 如果该位置的值是 0，则 dp(i,j)=0，因为当前位置不可能在由 1 组成的正方形中；

- 如果该位置的值是 1，则 dp(i,j) 的值由其上方、左方和左上方的三个相邻位置的 dp 值决定。具体而言，当前位置的元素值等于三个相邻位置的元素中的最小值加 1，状态转移方程如下：

​												dp(i,j)=min(dp(i−1,j),dp(i−1,j−1),dp(i,j−1))+1

此外，还需要考虑边界条件。如果 *i* 和 *j* 中至少有一个为 0，则以位置 (*i*,*j*) 为右下角的最大正方形的边长只能是 1，因此 *dp*(*i*,*j*)=1。

```c++
int maximalSquare(vector<vector<char>>& matrix) {
    int res = 0;
    int m = matrix.size(), n = matrix[0].size();
    vector<vector<int>> dp(m+1, vector<int>(n+1, 0));
    for(int i = 0; i < m; i++){
        for(int j = 0; j < n; j++){
            if(matrix[i][j] == '1'){
                if(i == 0 || j == 0){
                    dp[i][j] = 1;
                }
                else{
                    dp[i][j] = min(min(dp[i-1][j], dp[i-1][j-1]),dp[i][j-1]) + 1;
                }
            }
            res = max(res, dp[i][j]);
        }
    }
    return res * res;
}
```

4. [旋转矩阵](https://leetcode.cn/problems/rotate-image/)

> 给定一个 *n* × *n* 的二维矩阵 `matrix` 表示一个图像。请你将图像顺时针旋转 90 度。
>
> 你必须在**[ 原地](https://baike.baidu.com/item/原地算法)** 旋转图像，这意味着你需要直接修改输入的二维矩阵。**请不要** 使用另一个矩阵来旋转图像。

```c++
输入：matrix = [
    [1,2,3],
    [4,5,6],
    [7,8,9]
]
输出：[
    [7,4,1],
    [8,5,2],
    [9,6,3]
]
```

```c++
void rotate(vector<vector<int>>& matrix) {
    int n = matrix.size();
    for(int i = 0; i < n/2; i++){     //先水平翻转
        swap(matrix[i], matrix[n-i-1]);
    }
    for(int i = 0; i < n; i++){
        for(int j = 0; j < i; j++){
            swap(matrix[i][j], matrix[j][i]);    //再转置，沿主对角线翻转
        }
    }
    return;
}
```

5. [搜索二维矩阵II](https://leetcode.cn/problems/search-a-2d-matrix-ii/)

> 编写一个高效的算法来搜索 `*m* x *n*` 矩阵 `matrix` 中的一个目标值 `target` 。该矩阵具有以下特性：
>
> - 每行的元素从左到右升序排列。
> - 每列的元素从上到下升序排列。

```c++
输入：matrix = [
    [1,4,7,11,15],
    [2,5,8,12,19],
    [3,6,9,16,22],
    [10,13,14,17,24],
    [18,21,23,26,30]], 
target = 5
输出：true
```

```c++
bool searchMatrix(vector<vector<int>>& matrix, int target) {
    int m = matrix.size(), n = matrix[0].size();
    int i = 0, j = n - 1;  //从右上角开始搜索
    while(i < m && j >=0){
        if(matrix[i][j] == target) 
            return true;
        else if(matrix[i][j] < target)
            i++;
        else if(matrix[i][j] > target)
            j--;
    }
    return false;
}
```

6. [腐烂的橘子](https://leetcode.cn/problems/rotting-oranges/)

> 在给定的 `m x n` 网格 `grid` 中，每个单元格可以有以下三个值之一：
>
> - 值 `0` 代表空单元格；
> - 值 `1` 代表新鲜橘子；
> - 值 `2` 代表腐烂的橘子。
>
> 每分钟，腐烂的橘子 **周围 4 个方向上相邻** 的新鲜橘子都会腐烂。
>
> 返回 *直到单元格中没有新鲜橘子为止所必须经过的最小分钟数。如果不可能，返回 `-1`* 。

```c++
输入：grid =  
[
    [2,1,1],
    [1,1,0],
    [0,1,1]
]
输出：4
```

```c++
int orangesRotting(vector<vector<int>>& grid){
    int m = grid.size(), n = grid[0].size();
    queue<pair<int, int>> q;
    int fresh = 0;   //记录新鲜橘子的数量
    for(int i = 0; i < m; i++){
        for(int j = 0; j < n; j++){
            if(grid[i][j] == 2){
                q.push({i, j});   //将所有腐烂橘子的坐标加入队列
            }
            else if(grid[i][j] == 1){
                fresh++;    //统计新鲜橘子的数量
            }
        }
    }
    if(fresh == 0) return 0; //如果新鲜橘子的数量为0，直接返回0!!!
    int res = 0;  //记录分钟数
    vector<vector<int>> dirs = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}}; //四个方向
    while(!q.empty()){   //队列不为空，即还有腐烂橘子
        int size = q.size();  //记录当前分钟的腐烂橘子的数量
        for(int i = 0; i < size; i++){   
            auto cur = q.front();   //当前腐烂橘子的坐标
            q.pop();
            for(auto dir : dirs){
                int x = cur.first + dir[0], y = cur.second + dir[1];
                if(x < 0 || x >= m || y < 0 || y >= n || grid[x][y] != 1) continue;
                grid[x][y] = 2;
                q.push({x, y});     //将新腐烂的橘子坐标加入队列
                fresh--;        //新鲜橘子的数量减1
            }
        }
        res++;
    }
    return fresh == 0 ? res - 1 : -1; //如果新鲜橘子的数量为0，返回res-1!!!否则返回-1
}

```

7. [最短的桥](https://leetcode.cn/problems/shortest-bridge/)

> 给你一个大小为 `n x n` 的二元矩阵 `grid` ，其中 `1` 表示陆地，`0` 表示水域。
>
> **岛** 是由四面相连的 `1` 形成的一个最大组，即不会与非组内的任何其他 `1` 相连。`grid` 中 **恰好存在两座岛** 。
>
> 你可以将任意数量的 `0` 变为 `1` ，以使两座岛连接起来，变成 **一座岛** 。
>
> 返回必须翻转的 `0` 的最小数目。

```c++
输入：grid = [
    [0,1],
    [1,0]
]
输出：1
输入：grid = [
    [0,1,0],
    [0,0,0],
    [0,0,1]
]
输出：2
```

```c++
//深度优先搜索出第一座岛的集合；对第一座岛所有陆地进行广搜，向外延伸的圈数就是最短距离。
class Solution {
public:
    queue<pair<int,int>> q;  
    vector<vector<int>> dirs = {{0,1}, {0, -1}, {1, 0}, {-1, 0}};
    void dfs(vector<vector<int>>& grid, int i, int j){
        if(i < 0 || i >= grid.size() || j < 0 || j >= grid[0].size() || grid[i][j] != 1) 
            return;
        q.push({i,j});
        grid[i][j] = 2;  //标记已被搜过
        for(auto& dir : dirs){
            int x = i + dir[0];
            int y = j + dir[1];
            dfs(grid, x, y);
        }
    }

    int shortestBridge(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        int flag = 0;
        for(int i = 0; i < m; i++){
            for(int j = 0; j < n; j++){
                if(flag) break;      // 只需要找到第一座岛
                if(grid[i][j] == 1){
                    dfs(grid, i, j);
                    flag = 1;
                    break;
                }
            }
         
        }
        int count = 0;
        while(!q.empty()){
            int size = q.size();
            while(size--){
                auto [i,j] = q.front(); q.pop();
                for(auto& dir : dirs){
                    int x = i + dir[0];
                    int y = j + dir[1];
                    if(x < 0 || x >= m || y < 0 || y >= n) continue;
                    else{
                        if(grid[x][y] == 1){   //连接到第二座岛
                            return count;
                        }
                        else if(grid[x][y] == 0){
                            q.push({x,y});
                            grid[x][y] = 2;
                        }
                    }
                }
            }
            count++;
        }
        return 0;
    }
};
```



### 十、图论

图的构造：

```c++
int main(){
	int n, m, s, t;
	cin >> m >> n;
	vector<vector<int>> graph(n+1, vector<int>(n+1, 0));  //邻接矩阵
	for(int i = 0; i < m; i++){
		cin >> s >> t;
		graph[s][t] = 1;
	}
	
	vector<list<int>> graph(n+1);  //邻接表, 使用list 双向链表
	for(int i = 0; i < m; i++){
		cin >> s >> t;
		graph[s].push_back(t);
	}
	return 0;
} 
```

1. 图的深度优先搜索

```c++

```



1. 图的广度优先搜索

1. [课程表](https://leetcode.cn/problems/course-schedule/)

> 你这个学期必须选修 `numCourses` 门课程，记为 `0` 到 `numCourses - 1` 。
>
> 在选修某些课程之前需要一些先修课程。 先修课程按数组 `prerequisites` 给出，其中 `prerequisites[i] = [ai, bi]` ，表示如果要学习课程 `ai` 则 **必须** 先学习课程 `bi` 。
>
> - 例如，先修课程对 `[0, 1]` 表示：想要学习课程 `0` ，你需要先完成课程 `1` 。
>
> 请你判断是否可能完成所有课程的学习？如果可以，返回 `true` ；否则，返回 `false`

```c++
输入：numCourses = 2, prerequisites = [[1,0],[0,1]]
输出：false
```

```c++
//判断有无环，拓扑排序
bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
    // 构建图的邻接表和入度表
    vector<vector<int>> graph(numCourses);
    vector<int> indegree(numCourses, 0);
    // 构建邻接表并计算每个节点的入度
    for(const auto& pair : prerequisites){
        graph[pair[1]].push_back(pair[0]);
        indegree[pair[0]]++;
    }
    // 找出所有入度为0的节点，加入队列
    queue<int> q;
    for(int i = 0; i < numCourses; i++){
        if(indegree[i] == 0)
            q.push(i);
    }
    int visited = 0;
    while(!q.empty()){
        int temp = q.front(); 
        q.pop();
        visited++;
        // 对于该课程的每个后续课程，减少其入度
        for(int next: graph[temp]){
            indegree[next]--;
            if(indegree[next] == 0)  //入度为0的节点，加入队列
                q.push(next);
        }
    }
    // 如果所有课程都访问过，返回true；否则，返回false
    return visited == numCourses;
}
```

3. [阈值距离内邻居最少的城市](https://leetcode.cn/problems/find-the-city-with-the-smallest-number-of-neighbors-at-a-threshold-distance/)

> 有 `n` 个城市，按从 `0` 到 `n-1` 编号。给你一个边数组 `edges`，其中 `edges[i] = [fromi, toi, weighti]` 代表 `fromi` 和 `toi` 两个城市之间的双向加权边，距离阈值是一个整数 `distanceThreshold`。
>
> 返回在路径距离限制为 `distanceThreshold` 以内可到达城市最少的城市。如果有多个这样的城市，则返回编号最大的城市。
>
> 注意，连接城市 ***i*** 和 ***j*** 的路径的距离等于沿该路径的所有边的权重之和。

```c++
输入：n = 4, edges = [[0,1,3],[1,2,1],[1,3,4],[2,3,1]], distanceThreshold = 4
输出：3
解释：城市分布图如上。
每个城市阈值距离 distanceThreshold = 4 内的邻居城市分别是：
城市 0 -> [城市 1, 城市 2] 
城市 1 -> [城市 0, 城市 2, 城市 3] 
城市 2 -> [城市 0, 城市 1, 城市 3] 
城市 3 -> [城市 1, 城市 2] 
城市 0 和 3 在阈值距离 4 以内都有 2 个邻居城市，但是我们必须返回城市 3，因为它的编号最大。
```

```c++
//Floyd算法，计算任意两点之间的最短路径，使用动态规划来更新任意两点间的最短距离。 O(n^3) O(n^2)
int findTheCity(int n, vector<vector<int>>& edges, int distanceThreshold) {
    // 初始化距离矩阵，初始值为无穷大，自己到自己为 0
    vector<vector<int>> dist(n, vector<int>(n, INT_MAX));
    for(int i = 0; i < n; i++)
        dist[i][i] = 0;
    // 填充初始的距离信息
    for(const auto& edge : edges){
        int from = edge[0], to = edge[1], weight = edge[2];
        dist[from][to] = weight;
        dist[to][from] = weight;
    }
    // Floyd-Warshall 算法核心
    for(int k = 0; k < n; k++){
        for(int i = 0; i < n; i++){
            for(int j = 0; j < n; j++){
                if(dist[i][k] != INT_MAX && dist[k][j] != INT_MAX){  //防止溢出，但凡有一个通过中间节点的距离是INT_MAX，不用更新
                    dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);
                }
            }
        }
    }
    pair<int,int> res(INT_MAX, -1); //记录{结点，结点能到达的城市数量}
    for(int i = 0; i < n; i++){
        int dist_i = 0;     // 计算当前城市能在阈值内到达的城市数
        for(int j = 0; j < n; j++){
            if(dist[i][j] <= distanceThreshold)
                dist_i++;
        }
        if(dist_i <= res.first)  // 更新记录，"="表示选择编号更大的城市
            res = {dist_i, i};
    }
    return res.second;
}
```



### 十一、数论

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

### 十二、 其他

1. 矩形面积

> 给你 **二维** 平面上两个 **由直线构成且边与坐标轴平行/垂直** 的矩形，请你计算并返回两个矩形覆盖的总面积。
>
> 每个矩形由其 **左下** 顶点和 **右上** 顶点坐标表示：
>
> - 第一个矩形由其左下顶点 `(ax1, ay1)` 和右上顶点 `(ax2, ay2)` 定义。
> - 第二个矩形由其左下顶点 `(bx1, by1)` 和右上顶点 `(bx2, by2)` 定义。

```c++
输入：ax1 = -3, ay1 = 0, ax2 = 3, ay2 = 4, bx1 = 0, by1 = -1, bx2 = 9, by2 = 2
输出：45
```

```c++
int computeArea(int ax1, int ay1, int ax2, int ay2, int bx1, int by1, int bx2, int by2) {
    int area1 = abs(ax1 - ax2) * abs(ay1 - ay2);
    int area2 = abs(bx1 - bx2) * abs(by1 - by2);
    int overlap_width = min(ax2, bx2) - max(ax1, bx1); 	//X轴投影的重叠线段
    int overlap_height = min(ay2, by2) - max(ay1, by1);	//Y轴投影的重叠线段
    int overlap_area = max(overlap_width, 0) * max(overlap_height, 0); //判断是否有重叠，有一个为负则无重叠。
    int res = area1 + area2 - overlap_area;
    return res;
}
```

2. [用 Rand7() 实现 Rand10()](https://leetcode.cn/problems/implement-rand10-using-rand7/)

> 给定方法 `rand7` 可生成 `[1,7]` 范围内的均匀随机整数，试写一个方法 `rand10` 生成 `[1,10]` 范围内的均匀随机整数。
>
> 你只能调用 `rand7()` 且不能调用其他方法。请不要使用系统的 `Math.random()` 方法。
>
> 每个测试用例将有一个内部参数 `n`，即你实现的函数 `rand10()` 在测试时将被调用的次数。请注意，这不是传递给 `rand10()` 的参数。

```c++
输入: 2
输出: [2,8]
输入: 3
输出: [3,8,10]
```

```c++
int rand10() {
    int num = (rand7() - 1) * 7 + rand7();
    while(num > 40){
        num = (rand7() - 1) * 7 + rand7();
    }
    return num % 10 + 1;
}
```

