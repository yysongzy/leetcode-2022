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

#### 倒序遍历的原因是，本质上还是一个对二维数组的遍历，并且右下角的值依赖上一层左上角的值，因此需要保证左边的值仍然是上一层的，从右向左覆盖

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

### [494. Target Sum](https://leetcode.cn/problems/target-sum/)

#### 感觉在考数学题了，没什么意思，看看就好

```cpp
class Solution {
public:
    int findTargetSumWays(vector<int>& nums, int target) {
        // 思路：
        // left 组合 - right 组合 = target
        // left 组合 + right 组合 = sum
        // left 组合 = (target + sum) / 2
        // 即在 nums 找出和为 left 的组合

        // 1. 确定 dp table 以及下标的含义
        // dp[j] 表示填满 j 这么大容量的包，有 dp[j] 种方案
        // 2. 确定递推公式，物品重量为 nums[i]，物品价值为 nums[i]
        // dp[j] += dp[j - nums[i]]
        // 3. dp table 如何初始化
        // dp[0] = 1, 其他值初始化为 0
        // 4. 确定遍历顺序
        // i 从小到大，j 从大到小倒序遍历，自己画图理解

        int sum = accumulate(nums.begin(), nums.end(), 0);
        if ((sum + target) % 2) return 0;
        if (abs(target) > sum) return 0;

        int newTarget = (sum + target) / 2;
        vector<int> dp(newTarget + 1, 0);
        dp[0] = 1;
        for (int i = 0; i != nums.size(); ++i) {
            for (int j = newTarget; j >= nums[i]; --j) {
                dp[j] += dp[j - nums[i]];
            }
        }

        return dp[newTarget];
    }
};
```

### [518. Coin Change 2](https://leetcode.cn/problems/coin-change-2/)

#### 需要再理解下

```cpp
class Solution {
public:
    int change(int amount, vector<int>& coins) {
        // 1. 确定 dp table 以及下标的含义
        // dp[j] 表示凑成总金额 j 的货币组合数为 dp[j]
        // 2. 确定递推公式
        // dp[j] += dp[j - coins[i]]
        // 3. dp table 如何初始化
        // dp[0] = 1, 其他值初始化为 0
        // 4. 确定遍历顺序
        // 外层遍历 coins，内层遍历 account，自己画图理解

        vector<int> dp(amount + 1, 0);
        dp[0] = 1;

        for (int i = 0; i != coins.size(); ++i) {
            for (int j = coins[i]; j <= amount; ++j) {
                dp[j] += dp[j - coins[i]];
            }
        }

        return dp[amount];
    }
};
```

### [377. Combination Sum IV](https://leetcode.cn/problems/combination-sum-iv/)

```cpp
class Solution {
public:
    int combinationSum4(vector<int>& nums, int target) {
        // 1. 确定 dp table 以及下标的含义
        // dp[j] 表示凑成 target 的排列数为 dp[j]
        // 2. 确定递推公式
        // dp[j] += dp[j - nums[i]]
        // 3. dp table 如何初始化
        // dp[0] = 1, 其他值初始化为 0
        // 4. 确定遍历顺序
        // 外层遍历 target，内层遍历 nums，自己画图理解

        vector<int> dp(target + 1, 0);
        dp[0] = 1;
        
        for (int j = 0; j <= target; ++j) {
            for (int i = 0; i != nums.size(); ++i) {
                if (j - nums[i] >= 0) dp[j] += dp[j - nums[i]];
            }
        }

        return dp[target];
    }
};
```

### [70. Climbing Stairs](https://leetcode.cn/problems/climbing-stairs/)

```cpp
class Solution {
public:
    int climbStairs(int n) {
        // 1. 确定 dp table 以及下标的含义
        // dp[j] 表示到达楼顶的排列数为 dp[j]
        // 2. 确定递推公式
        // dp[j] += dp[j - i]
        // 3. dp table 如何初始化
        // dp[0] = 1, 其他值初始化为 0
        // 4. 确定遍历顺序
        // 外层遍历 target，内层遍历可以爬的最多台阶数，自己画图理解

        vector<int> dp(n + 1, 0);
        dp[0] = 1;

        for (int j = 0; j <= n; ++j) {
            for (int i = 1; i <= 2; ++i) { // 2 代表 Each time you can either climb 1 or 2 steps
                if (j - i >= 0) dp[j] += dp[j - i];
            }
        }

        return dp[n];
    }
};
```

### [322. Coin Change](https://leetcode.cn/problems/coin-change/)

```cpp
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        // 1. 确定 dp table 以及下标的含义
        // dp[j] 表示组成 amount 的最少 coins 数量为 dp[j]
        // 2. 确定递推公式
        // dp[j] = min(dp[j - coins[i]] + 1, dp[j])
        // 3. dp table 如何初始化
        // dp[0] = 0, 其他值初始化为 INT32_MAX
        // 4. 确定遍历顺序
        // 怎么遍历都行，因为无论排列还是组合都可以找到最少 coins 数量

        vector<int> dp(amount + 1, INT32_MAX);
        dp[0] = 0;

        for (int i = 0; i < coins.size(); ++i) {
            for (int j = coins[i]; j <= amount; ++j) {
                // if (dp[j - coins[i]] != INT_MAX) // 如果dp[j - coins[i]]是初始值则跳过
                dp[j] = min(dp[j - coins[i]] + 1, dp[j]);
            }
        }

        return dp[amount] == INT32_MAX ? -1 : dp[amount];
    }
};
```

### [279. Perfect Squares](https://leetcode.cn/problems/perfect-squares/)

```cpp
class Solution {
public:
    int numSquares(int n) {
        // 1. 确定 dp table 以及下标的含义
        // dp[j] 表示组成 n 的最少 perfect squares 数量为 dp[j]
        // 2. 确定递推公式, nums[i] 代表 1 到 n 的 perfect squares 数组
        // dp[j] = min(dp[j - nums[i]] + 1, dp[j])
        // 3. dp table 如何初始化
        // dp[0] = 0, 其他值初始化为 INT32_MAX
        // 4. 确定遍历顺序
        // 怎么遍历都行，因为无论排列还是组合都可以找到最少 perfect squares 数量

        // special case
        if (n <= 0) return -1;

        // 构造 nums[i]
        vector<int> nums;
        for (int i = 1; i <= n; ++i) {
            if (i * i > n) break;
            nums.push_back(i * i);
        }

        vector<int> dp(n + 1, INT32_MAX);
        dp[0] = 0;

        for (int i = 0; i < nums.size(); ++i) {
            for (int j = nums[i]; j <= n; ++j) {
                dp[j] = min(dp[j - nums[i]] + 1, dp[j]);
            }
        }

        return dp[n];
    }
};
```

### [198. House Robber](https://leetcode.cn/problems/house-robber/)

#### 经过前几天的训练，这题已经可以根据递归四部曲立刻写出来了

```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        // 1. 确定 dp table 以及下标的含义
        // dp[i] 代表房子数量为 i 时可以偷的最大金额为 dp[i]
        // 2. 确定递推公式
        // dp[i] = max(dp[i - 1], dp[i - 2] + nums[i])
        // 3. dp table 如何初始化
        // dp[0] = nums[0], dp[1] = max(nums[0], nums[1]), 其他初始化为 0
        // 4. 确定遍历顺序
        // i 从小到大

        // special case
        if (nums.size() == 0) return -1;
        if (nums.size() == 1) return nums[0];

        vector<int> dp(nums.size() + 1, 0);
        dp[0] = nums[0];
        dp[1] = max(nums[0], nums[1]);

        for (int i = 2; i < nums.size(); ++i) {
            dp[i] = max(dp[i - 1], dp[i - 2] + nums[i]);
        }

        return dp[nums.size() - 1];
    }
};
```

### [213. House Robber II](https://leetcode.cn/problems/house-robber-ii/)

#### 考虑两种情况，分类讨论

```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        // 1. 确定 dp table 以及下标的含义
        // dp[i] 代表房子数量为 i 时可以偷的最大金额为 dp[i]
        // 2. 确定递推公式
        // dp[i] = max(dp[i - 1], dp[i - 2] + nums[i])
        // 3. dp table 如何初始化
        // dp[0] = nums[0], dp[1] = max(nums[0], nums[1]), 其他初始化为 0
        // 4. 确定遍历顺序
        // i 从小到大

        // special case
        if (nums.size() == 0) return -1;
        if (nums.size() == 1) return nums[0];
        if (nums.size() == 2) return max(nums[0], nums[1]);

        return max(robRange(nums, 0, nums.size() - 1), robRange(nums, 1, nums.size()));
    }

    int robRange(const vector<int>& nums, int begin, int end) {
        if (begin > end) return -1;
        if (begin == end) return nums[begin];

        vector<int> dp(nums.size() + 1, 0);
        dp[begin] = nums[begin];
        dp[begin + 1] = max(nums[begin], nums[begin + 1]);

        for (int i = begin + 2; i < end; ++i) {
            dp[i] = max(dp[i - 1], dp[i - 2] + nums[i]);
        }

        return dp[end - 1];
    }
};
```

### [337. House Robber III](https://leetcode.cn/problems/house-robber-iii/)

#### recursive

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
    unordered_map<TreeNode*, int> memo;

    int rob(TreeNode* root) {
        // base case
        if (!root) return 0;
        else if (memo.count(root)) return memo[root];

        if (!root->left && !root->right) return root->val;

        // 偷父节点
        int v1 = root->val;
        if (root->left) v1 += rob(root->left->left) + rob(root->left->right);
        if (root->right) v1 += rob(root->right->left) + rob(root->right->right);
        // 不偷父节点
        int v2 = rob(root->left) + rob(root->right);

        int res = max(v1, v2);
        memo[root] = res;
        return res;
    }
};
```

#### dp

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
    int rob(TreeNode* root) {
        // 1. 确定 dp table 以及下标的含义
        // dp table 代表下标为 0 记录偷该节点所得到的的最大金钱，下标为 1 记录不偷该节点所得到的的最大金钱
        // 2. 确定递推公式
        // max(rob, noRob), rob = root->val + leftPair.second + rightPair.second, noRob = max(leftPair.first, leftPair.second) + max(rightPair.first, rightPair.second)
        // 3. dp table 如何初始化
        // 初始化为 0
        // 4. 确定遍历顺序
        // 后序遍历

        auto pair = robTree(root);
        return max(pair.first, pair.second);
    }

    std::pair<int, int> robTree(TreeNode* root) {
        // base case
        if (!root) return {0, 0};

        auto leftPair = robTree(root->left);
        auto rightPair = robTree(root->right);
        int rob = root->val + leftPair.second + rightPair.second;
        int noRob = max(leftPair.first, leftPair.second) + max(rightPair.first, rightPair.second);

        return {rob, noRob};
    }
};
```