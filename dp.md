[TOC]

# 递归五部曲

1. 确定dp数组（dp table）以及下标的含义
2. 确定递推公式
3. dp数组如何初始化
4. 确定遍历顺序
5. 举例推导dp数组

### [509. Fibonacci Number](https://leetcode.cn/problems/fibonacci-number/)

#### 注意数组越界问题

```cpp
class Solution {
public:
    int fib(int n) {
        // special case
        if (n < 2) return n;

        vector<int> dp(n + 2, 0);
        dp[0] = 0;
        dp[1] = 1;

        for (int i = 2; i <= n; ++i) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }

        return dp[n];
    }
};
```

### [70. Climbing Stairs](https://leetcode.cn/problems/climbing-stairs/)

```cpp
class Solution {
public:
    int climbStairs(int n) {
        if (n < 0) return -1;
        else if (n <= 1) return n;

        vector<int> dp(n + 1, 0);

        dp[1] = 1;
        dp[2] = 2;
        
        for (int i = 3; i <= n; ++i) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }

        return dp[n];
    }
};
```

### [746. Min Cost Climbing Stairs](https://leetcode.cn/problems/min-cost-climbing-stairs/)

```cpp
class Solution {
public:
    int minCostClimbingStairs(vector<int>& cost) {
        vector<int> dp(cost.size(), 0);

        dp[0] = cost[0];
        dp[1] = cost[1];

        for (int i = 2; i < cost.size(); ++i) {
            dp[i] = min(dp[i - 1], dp[i - 2]) + cost[i];
        }

        return min(dp[cost.size() - 1], dp[cost.size() - 2]);
    }
};
```

### [62. Unique Paths](https://leetcode.cn/problems/unique-paths/)

#### i 从 1 开始，防止数组越界

```cpp
class Solution {
public:
    int uniquePaths(int m, int n) {
        // 1. 确定 dp table 以及下标的含义
        // dp[i][j] 代表到 (i, j) 坐标的总 path
        // 2. 确定递推公式
        // dp[i][j] = dp[i - 1][j] + dp[i][j - 1]
        // 3. dp table 如何初始化
        // dp[0][0] = 0, dp[i][0] = 1, dp[0][j] = 1
        // 4. 确定遍历顺序
        // 从上到下，从左到右一层一层地遍历

        vector<vector<int>> dp(m, vector<int>(n, 0));

        for (int i = 0; i < m; ++i) dp[i][0] = 1;
        for (int j = 0; j < n; ++j) dp[0][j] = 1;

        for (int i = 1; i < m; ++i) {
            for (int j = 1; j < n; ++j) {
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }
        }

        return dp[m - 1][n - 1];
    }
};
```

### [63. Unique Paths II](https://leetcode.cn/problems/unique-paths-ii/)

```cpp
class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        // 1. 确定 dp table 以及下标的含义
        // dp[i][j] 代表到 (i, j) 坐标的总 path
        // 2. 确定递推公式
        // dp[i][j] = dp[i - 1][j] + dp[i][j - 1]
        // 3. dp table 如何初始化
        // dp[0][0] = 0, 障碍点 (x, y), dp[x][y] = 0
        // dp[i][0] = 1, dp[0][j] = 1，但遇到障碍点，后续的点都为 0
        // 4. 确定遍历顺序
        // 从上到下，从左到右一层一层地遍历

        int m = obstacleGrid.size();
        int n = obstacleGrid[0].size();
        vector<vector<int>> dp(m, vector<int>(n, 0));

        for (int i = 0; i < m && obstacleGrid[i][0] == 0; ++i)  dp[i][0] = 1;
        for (int j = 0; j < n && obstacleGrid[0][j] == 0; ++j)  dp[0][j] = 1;

        for (int i = 1; i < m; ++i) {
            for (int j = 1; j < n; ++j) {
                if (obstacleGrid[i][j] == 0) {
                    dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
                }
            }
        }

        return dp[m - 1][n - 1];
    }
};
```

### [343. Integer Break](https://leetcode.cn/problems/integer-break/)

```cpp
class Solution {
public:
    int integerBreak(int n) {
        // 1. 确定 dp table 以及下标的含义
        // dp[i] 代表整数 i 的乘积最大值
        // 2. 确定递推公式
        // dp[i] = max(dp[i], max(j * (i - j), j * dp[i - j]))
        // 3. dp table 如何初始化
        // dp[0] = 1, dp[1] = 1, dp[2] = 1
        // 4. 确定遍历顺序
        // 按数轴从前往后方向遍历

        vector<int> dp(n + 1, 0);
        dp[2] = 1;

        for (int i = 3; i <= n; ++i) {
            for (int j = 1; j < i; ++j) {
                dp[i] = max(dp[i], max(j * (i - j), j * dp[i - j]));
            }
        }

        return dp[n];
    }
};
```

### [96. Unique Binary Search Trees](https://leetcode.cn/problems/unique-binary-search-trees/)

```cpp
class Solution {
public:
    int numTrees(int n) {
        // 1. 确定 dp table 以及下标的含义
        // dp[i] 代表节点为 i 的 BST 数量
        // 2. 确定递推公式
        // dp[i] = dp[i - 1] * dp[0] + dp[i - 2] * dp[1] + ... + dp[0] * dp[i - 1]
        // 3. dp table 如何初始化
        // dp[0] = 1, dp[1] = 1, dp[2] = 2, dp[3] = 5
        // 4. 确定遍历顺序
        // i 从小到大

        if (n == 1) return 1;

        vector<int> dp(n + 1, 0);

        dp[0] = 1;
        dp[1] = 1;
        dp[2] = 2;

        for (int i = 3; i <= n; ++i) {
            for (int j = 0; j <= i - 1; ++j) {
                dp[i] += dp[i - 1 - j] * dp[j];
            }
        }

        return dp[n];
    }
};
```

### 背包问题的暴力解法——回溯

```cpp
这是标准的背包问题，以至于很多同学看了这个自然就会想到背包，甚至都不知道暴力的解法应该怎么解了
这样其实是没有从底向上去思考，而是习惯性想到了背包，那么暴力的解法应该是怎么样的呢？
每一件物品其实只有两个状态，取或者不取，所以可以使用回溯法搜索出所有的情况，那么时间复杂度就是$o(2^n)$，这里的n表示物品数量
所以暴力的解法是指数级别的时间复杂度。进而才需要动态规划的解法来进行优化！
```

### [416. Partition Equal Subset Sum](https://leetcode.cn/problems/partition-equal-subset-sum/)

#### 一维的 01 背包一定要自己画图理解

```cpp
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        // 1. 确定 dp table 以及下标的含义
        // dp[j] 表示容量为 j 的背包，所背的物品价值最大为 dp[j]
        // 2. 确定递推公式，物品重量为 nums[i]，物品价值为 nums[i]
        // dp[j] = max(dp[j], dp[j - nums[i]] + nums[i])
        // 3. dp table 如何初始化
        // 初始化为 0，这样在递推公式中 dp[j] 才不会被初始值破坏
        // 4. 确定遍历顺序
        // i 从小到大，j 从大到小倒序遍历，自己画图理解

        int sum = accumulate(nums.begin(), nums.end(), 0);
        // special case
        if (sum % 2 == 1) return false;

        int target = sum / 2;
        vector<int> dp(target + 1, 0);

        for (int i = 0; i != nums.size(); ++i) {
            for (int j = target; j >= nums[i]; --j) {
                dp[j] = max(dp[j], dp[j - nums[i]] + nums[i]);
            }
        }

        // 集合中的元素正好可以凑成总和 target
        if (dp[target] == target) return true;
        return false;
    }
};
```

### [1049. Last Stone Weight II](https://leetcode.cn/problems/last-stone-weight-ii/)

```cpp
class Solution {
public:
    int lastStoneWeightII(vector<int>& stones) {
        // 1. 确定 dp table 以及下标的含义
        // dp[j] 表示重量为 j 的背包，所背的重量最大为 dp[j]
        // 2. 确定递推公式，石头重量为 nums[i]，石头价值为 nums[i]
        // dp[j] = max(dp[j], dp[j - stones[i]] + stones[i])
        // 3. dp table 如何初始化
        // 初始化为 0，这样在递推公式中 dp[j] 才不会被初始值破坏
        // 4. 确定遍历顺序
        // i 从小到大，j 从大到小倒序遍历，自己画图理解

        int sum = accumulate(stones.begin(), stones.end(), 0);
        int target = sum / 2;
        vector<int> dp(target + 1, 0);

        for (int i = 0; i != stones.size(); ++i) {
            for (int j = target; j >= stones[i]; --j) {
                dp[j] = max(dp[j], dp[j - stones[i]] + stones[i]);
            }
        }

        return (sum - dp[target]) - dp[target];
    }
};
```