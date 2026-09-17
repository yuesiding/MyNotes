## 爬楼梯系列
**T70 爬楼梯**
> 为要解决的问题都是「从 0 爬到 i」，所以定义 dfs(i) 表示从 0 爬到 i 有多少种不同的方法（或者说爬 i 个台阶的方案数）。
>分类讨论：
如果最后一步爬了 1 个台阶，那么我们得先爬到 i−1，要解决的问题缩小成：从 0 爬到 i−1 有多少种不同的方法。
如果最后一步爬了 2 个台阶，那么我们得先爬到 i−2，要解决的问题缩小成：从 0 爬到 i−2 有多少种不同的方法。
由于这两种方法是互相独立的（爬的台阶个数不同），所以根据加法原理，从 0 爬到 i 的方法数等于这两种方法数之和，即
**dfs(i)=dfs(i−1)+dfs(i−2)**
- 直接写：
    ```C++
    // 会超时的递归代码
    class Solution {
       int dfs(int i) {
          if (i <= 1) { // 递归边界
             return 1;
            }
            return dfs(i - 1) + dfs(i - 2);
        }
    public:
      int climbStairs(int n) {
          return dfs(n);
     }
    };
    ```
- 优化：如果一个状态（递归入参）是第一次遇到，那么可以在返回前，把状态及其结果记到一个 memo 数组中；如果一个状态不是第一次遇到（memo 中保存的结果不等于 memo 的初始值），那么可以直接返回 memo 中保存的结果。
*注意：**memo 数组的初始值一定不能等于要记忆化的值！** 例如初始值设置为 0，并且要记忆化的 dfs(i) 也等于 0，那就没法判断 0 到底表示第一次遇到这个状态，还是表示之前遇到过了，从而导致记忆化失效。一般把初始值设置为 −1。本题由于方案数均为正数，所以可以初始化成 0*
  ```C++
  class Solution {
    vector<int> memo;
    int dfs(int i) {
        if (i <= 1) { // 递归边界
            return 1;
        }
        int& res = memo[i]; // 注意这里是引用
        if (res) { // 之前计算过
            return res;
        }
        return res = dfs(i - 1) + dfs(i - 2); // 记忆化
    }
    public:
    int climbStairs(int n) {
        memo.resize(n + 1);
        return dfs(n);
    }
  };
  ```
**T377 组合总数IV**
```C++
  class Solution {
    public:
    int combinationSum4(vector<int>& nums, int target) {
        // dp[i] 表示凑成总和为 i 的不同排列方案数
        vector<unsigned long long> dp(target + 1, 0);
        // 基础情况：凑成总和为 0 的方案数只有 1 种（什么都不选）
        dp[0] = 1;
        // 【关键点】：外层遍历 target（从 1 到 target），内层遍历 nums
        // 这样可以确保包含了所有不同的选择顺序（即求排列数）
        for (int i = 1; i <= target; ++i) {
            for (int num : nums) {
                // 如果当前目标值 i 大于等于数字 num
                if (i >= num) {
                    // 状态转移：到达 i 的方案数 += 先凑出 (i - num) 的方案数
                    dp[i] += dp[i - num];
                }
            }
        }
        return dp[target];
        }
    };
```
- 外层遍历 nums，内层遍历 target → 算出来的是**组合数**（不考虑顺序，比如 [1,2] 和 [2,1] 只会被算 1 次）。
- 外层遍历 target，内层遍历 nums → 算出来的是**排列数**（考虑顺序，[1,2] 和 [2,1] 会被算 2 次）。
> 外层循环遍历物品的话，一个物品遍历过就不会再遍历到，所以强调的是物品的个数而不是位置，类似组合问题。
内层循环遍历物品的话，同一个物品会被多次遍历到，可以是上一轮循环选了物品 A，当前这轮循环选了物品 B，也可以是上一轮循环选了物品 B，当前这轮循环选了物品 A，这是不同的排列。  

## 打家劫舍
**T198 打家劫舍**
> dp[i]：表示考虑到第i间房子（下标 0∼i）时，能偷到的最高总金额。
不偷第i间房子：既然不偷第i间，那最大收益就完全等于前 i−1 间房子的最高收益。→ dp[i - 1]
偷第i间房子：既然偷了第i间，第i−1间就绝对不能偷，收益只能来自前 i−2 间房子的最高收益，加上第i间房子的钱 → dp[i - 2] + nums[i]

> dp[i] = max(dp[i - 1], dp[i - 2] + nums[i])
```C++
 int rob(vector<int>& nums) {
        int n = nums.size();
        if (n == 0) return 0;
        if (n == 1) return nums[0];
        // dp[i] 表示前 i 间房子能偷到的最大金额
        vector<int> dp(n, 0);
        // 1. 初始化基础情况
        dp[0] = nums[0];                     // 只有第 0 间房，只能偷它
        dp[1] = max(nums[0], nums[1]);       // 有 2 间房，偷钱较多的那间
        // 2. 自底向上递推
        for (int i = 2; i < n; ++i) {
            dp[i] = max(dp[i - 1], dp[i - 2] + nums[i]);
        }
        // 3. 最后一间房计算出的就是全局最大值
        return dp[n - 1];
    }
```
*变形：T740 删除并获得点数*

## 最大子数组和：
> 定义状态 f[i] 表示以 a[i] 结尾的最大子数组和，不和 i 左边拼起来就是 f[i]=a[i]，和 i 左边拼起来就是 f[i]=f[i−1]+a[i]，取最大值就得到了状态转移方程 f[i]=max(f[i−1],0)+a[i]，答案为 max(f)。这个做法也叫做 **Kadane 算法**

T53 最大子数组和
```C++
int maxSubArray(vector<int>& nums) {
        int n=nums.size();
        vector<int>dp(n,0);
        dp[0]=nums[0];
        for(int i=1;i<n;i++){
            dp[i]=max(dp[i-1]+nums[i],nums[i]);
        }
        int x=*max_element(dp.begin(),dp.end());
        return x;
    }
```
*优化：由于计算 f[i] 只会用到 f[i−1]，不会用到更早的状态，所以可以用一个变量滚动计算。*
```C++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int ans = INT_MIN; // 注意答案可以是负数，不能初始化成 0
        int f = 0;
        for (int x : nums) {
            f = max(f, 0) + x;
            ans = max(ans, f);
        }
        return ans;
    }
};
```
变形 T1749 任意子数组和的绝对值的最大值
```C++
class Solution {
public:
    int maxAbsoluteSum(vector<int>& nums) {
        int ans = 0, f_max = 0, f_min = 0;
        for (int x: nums) {
            f_max = max(f_max, 0) + x;//求最大子数组和
            f_min = min(f_min, 0) + x;//（反向）求最小子数组和
            ans = max({ans, f_max, -f_min});
        }
        return ans;
    }
};
```

T1191 k次串联最大子数组和
```C++
class Solution {
public:
    int kConcatenationMaxSum(vector<int>& arr, int k) {
        const int MOD = 1e9 + 7;
        int n = arr.size();
        // 1. 计算数组总和、最大子数组和（普通 Kadane）
        long long total = 0;
        long long max_kadane = 0;   // 一个数组内的最大子数组和
        long long cur = 0;
        for (int x : arr) {
            total += x;
            cur = max(cur + x, (long long)x);
            max_kadane = max(max_kadane, cur);
        }
         // 如果 k == 1，直接返回
        if (k == 1) {
            return max_kadane % MOD;
        }
        // 2. 计算两个数组拼接后的最大子数组和
        // 方法：再跑一遍 Kadane，但数组重复两次
        long long max_two = 0;
        cur = 0;
        for (int t = 0; t < 2; t++) {
            for (int x : arr) {
                cur = max(cur + x, (long long)x);
                max_two = max(max_two, cur);
            }
        }
        // 3. 分类讨论
        long long ans = max_kadane;          // 至少不差于一个数组的答案
        if (k >= 2) {
            ans = max(ans, max_two);         // 考虑跨两个数组的情况
        }
        // 如果总和 > 0，中间可以再加 (k-2) 个完整数组
        if (total > 0 && k >= 2) {
            ans = max(ans, max_two + (k - 2) * total);
        }
        // 题目允许空子数组，和为 0
        if (ans < 0) ans = 0;
        return ans % MOD;
    }
};
```

## 网格图dp

T64
> 具体来说，f[i+1][j+1]表示从左上角到第 i 行第 j 列这个格子（记作 (i,j)）的最小价值和

> 状态转移方程:f[i+1][j+1]=min(f[i+1][j],f[i][j+1])+grid[i][j]

>问：为什么 grid[i][j] 的下标不用变？
答：既然是在 f 的最左边和最上边插入一排状态，那么就只需要修改和 f 有关的下标，其余任何逻辑都无需修改。或者说，如果把 grid[i][j] 也改成 grid[i+1][j+1]，那么当 i=m−1 或者 j=n−1 时 grid[i+1][j+1] 会下标越界，这显然是错误的。

```C++
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        vector f(m + 1, vector<int>(n + 1, INT_MAX));
        f[0][1] = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                f[i + 1][j + 1] = min(f[i + 1][j], f[i][j + 1]) + grid[i][j];
            }
        }
        return f[m][n];
    }
};
```

T2304 网格中的最小路径代价
> 问：如何思考循环顺序？什么时候要正序枚举，什么时候要倒序枚举？

>答：这里有一个通用的做法：盯着状态转移方程，想一想，要计算 dp[i][j]，必须先把 dp[i+1][⋅] 算出来，那么只有 i 从大到小枚举才能做到。对于 j 来说，由于在计算 dp[i][j] 的时候，dp[i+1][⋅] 已经全部计算完毕，所以 j 无论是正序还是倒序枚举都可以

**倒序法**
```C++
class Solution {
public:
    int minPathCost(vector<vector<int>>& grid, vector<vector<int>>& moveCost) {
        int m=grid.size(),n=grid[0].size();
        //dp[i][j] = 从 (i,j) 出发，到达最后一行的最小路径代价
        vector<vector<int>>dp(m,vector<int>(n,INT_MAX));
        dp[m-1]=grid[m-1];
        for(int i=m-2;i>=0;i--){
            for(int j=0;j<n;j++){
                for(int k=0;k<n;k++){
                    //到达下一行的第k列
                    dp[i][j]=min(dp[i][j],dp[i+1][k]+moveCost[grid[i][j]][k]);
                }
                dp[i][j]+=grid[i][j];
            }
        }
        int ans=*min_element(dp[0].begin(),dp[0].end());
        return ans;
    }
};
```
**正序法**
```C++
class Solution {
public:
    int minPathCost(vector<vector<int>>& grid, vector<vector<int>>& moveCost) {
        int m = grid.size(), n = grid[0].size();
        vector<vector<int>> dp(m, vector<int>(n, INT_MAX));
        // 初始化第一行
        for (int j = 0; j < n; j++) {
            dp[0][j] = grid[0][j];
        }
        //dp[i][j]表示从
        // 从上往下遍历
        for (int i = 1; i < m; i++) {
            for (int j = 0; j < n; j++) {
                for (int k = 0; k < n; k++) {
                    // 从 (i-1, k) 移动到 (i, j)
                    dp[i][j] = min(dp[i][j], dp[i-1][k] + moveCost[grid[i-1][k]][j] + grid[i][j]);
                }
            }
        }
        
        return *min_element(dp[m-1].begin(), dp[m-1].end());
    }
};
```

T3418 机器人可获得的最大金币数
**多一个约束，就多一个参数**
> 用「选或不选」分类讨论：
选：dfs(i,j,k)=max(dfs(i−1,j,k),dfs(i,j−1,k))+coins[i][j]。
不选（感化）：如果 k>0 且 coins[i][j]<0，则可以不选，dfs(i,j,k)=max(dfs(i−1,j,k−1),dfs(i,j−1,k−1))。


```C++
class Solution {
public:
    int maximumAmount(vector<vector<int>>& coins) {
        int m = coins.size(), n = coins[0].size();
        // 1. 定义一个三维 DP 数组，其中第三维是 array<int, 3>
        // 含义：f[i][j][k] 表示到达 (i,j) 时，已使用 k 次感化机会的最大金币数
        vector f(m + 1, vector(n + 1, array<int, 3>{INT_MIN / 2, INT_MIN / 2, INT_MIN / 2}));
        f[0][1] = {0, 0, 0};
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                int x = coins[i][j];
                f[i + 1][j + 1][0] = max(f[i + 1][j][0], f[i][j + 1][0]) + x;
                f[i + 1][j + 1][1] = max({f[i + 1][j][1] + x, f[i][j + 1][1] + x,
                                          f[i + 1][j][0], f[i][j + 1][0]});
                f[i + 1][j + 1][2] = max({f[i + 1][j][2] + x, f[i][j + 1][2] + x,
                                          f[i + 1][j][1], f[i][j + 1][1]});
            }
        }
        return f[m][n][2];
    }
};
```

## 0-1背包

### 方案型
> 经典问题描述
你有一个容量为 W 的背包，和 n 个物品。第 i 个物品的重量是 w[i]，价值是 v[i]。
每个物品只能选一次（选或不选，所以叫 0-1 背包）。
问：在不超过背包容量的前提下，能获得的最大总价值是多少？

> 为什么叫"0-1"？
因为每个物品只有两种状态：0：不选 ; 1：选

> 面对第 i 个物品：
├─ 不选它 → 问题变成：前 i-1 个物品，容量还是 W
└─ 选它   → 问题变成：前 i-1 个物品，容量变成 W - w[i]，价值加上 v[i]

> dp[i][j] = 从前 i 个物品中选，背包容量为 j 时，能获得的最大价值
> 状态转移方程：dp[i][j] = max(dp[i-1][j], dp[i-1][j-w[i]] + v[i])

T416 分割等和子集
```C++
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int s = reduce(nums.begin(), nums.end());
        if (s % 2) {
            return false;
        }
        s /= 2; // 注意这里把 s 减半了
        int n = nums.size();
        //f[i][j] = 能否从 nums 的前 i 个数中，选出一个子集，使得它们的和恰好等于 j
        vector f(n + 1, vector<int>(s + 1));
        f[0][0] = true;
        for (int i = 0; i < n; i++) {
            int x = nums[i];
            for (int j = 0; j <= s; j++) {
                f[i + 1][j] = j >= x && f[i][j - x] || f[i][j];
            }
        }
        return f[n][s];
    }
};
```

T494 目标和
> **问题转化**
给每个数添加 + 或 -，使得总和为 target。
设所有加 + 的数的和为 P，加 - 的数的绝对值和为 N，则：P - N = target  ;  P + N = sum(nums)
两式相加：2P = sum + target，所以 **P = (sum + target) / 2**
因此，问题等价于**从 nums 中选出若干个数，使它们的和恰好为 P 的方案数**。

> **前提条件**：
sum + target 必须是偶数（否则无解）。
target 的绝对值不能大于 sum（否则无解）。
P 必须是非负整数。

```C++
class Solution {
public:
    int findTargetSumWays(vector<int>& nums, int target) {
        int sum = 0;
        for (int x : nums) sum += x;
        // 检查无解情况
        if (sum < abs(target) || (sum + target) % 2 != 0) return 0;
        int P = (sum + target) / 2;  // 正数部分的目标和
        int n = nums.size();
        // dp[i][j] = 前 i 个数凑出和 j 的方案数
        vector<vector<int>> dp(n + 1, vector<int>(P + 1, 0));
        dp[0][0] = 1;  // 空集凑出 0，方案数为 1
        
        for (int i = 0; i < n; i++) {
            int x = nums[i];
            for (int j = 0; j <= P; j++) {
                // 不选 x
                dp[i + 1][j] = dp[i][j];
                // 选 x
                if (j >= x) {
                    dp[i + 1][j] += dp[i][j - x];
                }
            }
        }
        
        return dp[n][P];
    }
};
```
*典型错误：用了bool来解，最后只统计了true的数量，这样是存在漏解的，因为布尔型只关心**是否可达**，并不关心**有多少条路***

T1049 最后一块石头的重量
> **问题转化很关键**
题目：有一堆石头，每次选两块粉碎，最终最多剩一块，求剩下的最小可能重量。
> >*关键观察*：
粉碎过程等价于把石头分成两堆，让两堆互相抵消。
最终剩下的重量 = |sum1 - sum2|，其中 sum1 + sum2 = sum（总重量）。
要让剩余重量最小，就要让两堆重量尽可能接近，即让其中一堆的总重尽量接近 sum / 2。

>**转化后的问题**：
从石头中选出若干块，使它们的总重量不超过 sum / 2，并且尽可能大。
> 物品：每块石头。
物品重量 = 物品价值 = 石头的重量。
背包容量：target = sum / 2。
>> **定义：** dp[i][j] = 从前 i 块石头中选，总重量不超过 j 时，能获得的最大总重量。


### 布尔型dp
T3180 执行操作可获得的最大总奖励
> dp[s] 表示能否达到总奖励 s。
初始 dp[0] = true（总奖励为 0 是起点）

>**关键条件**：选择 v 时，当前总奖励 s 必须 严格小于 v，即 s < v。所以 s 只需从 v-1 遍历到 0
**倒序遍历**：从 v-1 递减到 0，保证每个 v 只被使用一次（0-1 背包）。如果正序，dp[s] 可能已经被当前 v 更新过，导致重复选择。
如果 dp[s] 为真，说明可以达到总和 s，那么加上 v 后可以达到 s + v，所以 dp[s+v] = true。

```C++
class Solution {
public:
    int maxTotalReward(vector<int>& rewardValues) {
        sort(rewardValues.begin(), rewardValues.end());
        int maxVal = rewardValues.back();
        int limit = 2 * maxVal;
        vector<bool> dp(limit+1, false);
        dp[0] = true;
        
        for (int v : rewardValues) {
            for (int s = v - 1; s >= 0; s--) {
                if (dp[s]) {
                    dp[s + v] = true;
                }
            }
        }
        for (int s = limit; s >= 0; s--) {
            if (dp[s]) return s;
        }
        return 0;
    }
};
```
## 完全背包
T322 零钱兑换
- 一维：
```C++
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        // dp[j] 表示凑成金额 j 的最少硬币数
        vector<int> dp(amount + 1, amount + 1); // 初始化为 amount+1 表示不可达
        dp[0] = 0;
        for (int coin : coins) {
            // 完全背包：内层正序遍历
            for (int j = coin; j <= amount; j++) {
                dp[j] = min(dp[j], dp[j - coin] + 1);
            }
        }
        
        return dp[amount] == amount + 1 ? -1 : dp[amount];
    }
};
```
> - 0-1 背包：倒序保证 dp[j-w] 是上一轮（未选当前物品）的状态，避免重复选。
> - 完全背包：正序允许 dp[j-w] 已经被当前物品更新过，从而实现重复选。

- 二维：
```C++
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        int n = coins.size();
        const int INF = amount + 1;
        // dp[i][j]：前 i 种硬币凑成金额 j 的最少硬币数
        vector<vector<int>> dp(n + 1, vector<int>(amount + 1, INF));
        dp[0][0] = 0;
        // 注意：dp[i][0] 也应该为 0，但初始化为 INF 后需要手动设置
        for (int i = 0; i <= n; i++) dp[i][0] = 0;
        
        for (int i = 1; i <= n; i++) {
            int coin = coins[i - 1];
            for (int j = 0; j <= amount; j++) {
                // 不使用第 i 种硬币
                dp[i][j] = dp[i - 1][j];
                // 使用第 i 种硬币（前提容量够）
                if (j >= coin) {
                    dp[i][j] = min(dp[i][j], dp[i][j - coin] + 1);
                }
            }
        }
        
        return dp[n][amount] == INF ? -1 : dp[n][amount];
    }
};
```
**核心区别：**
>
- 0-1背包：选完就不许再选 → 退回上一行（i-1）
```C++
 dp[i][j] = max(dp[i-1][j], dp[i-1][j-w] + v);
//                              ↑
//                         上一行 i-1
```
- 完全背包：选完还能继续选 → 停留在当前行（i）
```C++
dp[i][j] = max(dp[i-1][j], dp[i][j-w] + v);
//                              ↑
//                         当前行 i
```

T1449 数位成本和为目标值的最大数字
```C++
class Solution {
public:
    string largestNumber(vector<int>& cost, int target) {
        const int INF = -1e9;  // 表示不可达
        vector<int> dp(target + 1, INF);
        dp[0] = 0;  // 成本为 0 时，位数为 0

        // 完全背包：求最大位数
        for (int d = 1; d <= 9; d++) {
            int c = cost[d - 1];
            for (int j = c; j <= target; j++) {
                if (dp[j - c] != INF) {
                    dp[j] = max(dp[j], dp[j - c] + 1);
                }
            }
        }

        // 无法凑出 target
        if (dp[target] < 0) return "0";

        // 贪心构造最大数字
        string ans;
        int cur = target;
        while (cur > 0) {
            // 从大到小尝试数字 9 到 1
            for (int d = 9; d >= 1; d--) {
                int c = cost[d - 1];
                if (cur >= c && dp[cur - c] != INF && dp[cur] == dp[cur - c] + 1) {
                    ans += to_string(d);
                    cur -= c;
                    break;  // 选完一个数字就跳出，继续构造下一位
                }
            }
        }
        return ans;
    }
};
```
**核心要点**
>- unordered_map 会覆盖相同成本
多个数字可能有相同的成本（例如数字2和7的成本都是2），但 p[cost[i]] = i+1 会让后面的覆盖前面的，丢失了数字信息。
> - 先用完全背包求出最大位数，如果 dp[target] < 0，说明无法凑出，返回 "0"
> - 贪心构造最大数字：检查是否满足条件：
cur >= c：剩余成本够用
dp[cur - c] != INF：剩余成本 cur - c **能凑出**dp[cur] == dp[cur - c] + 1：选了 d 之后，总位数不减少（这是最关键的条件）
如果满足，就把 d 追加到 ans，cur -= c，然后跳出尝试循环，进入下一轮

