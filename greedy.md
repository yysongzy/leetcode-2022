[TOC]

# 贪心的本质是选择每一阶段的局部最优，从而达到全局最优

### [455. Assign Cookies](https://leetcode.cn/problems/assign-cookies/)

```cpp
class Solution {
public:
    int findContentChildren(vector<int>& g, vector<int>& s) {
        sort(g.begin(), g.end());
        sort(s.begin(), s.end());
        int res = 0;

        for (int i = 0; i != s.size(); ++i) {
            if (res < g.size() && g[res] <= s[i]) ++res;
        }

        return res;
    }
};
```

### [53. Maximum Subarray](https://leetcode.cn/problems/maximum-subarray/)

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int res = INT32_MIN;
        int sum = 0;

        for (int i = 0; i != nums.size(); ++i) {
            sum += nums[i];
            if (sum > res) res = sum;
            if (sum < 0) sum = 0;
        }

        return res;
    }
};
```

### [122. Best Time to Buy and Sell Stock II](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/)

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int res = 0;
        for (int i = 1; i != prices.size(); ++i) {
            res += max(prices[i] - prices[i - 1], 0);
        }

        return res;
    }
};
```

### [55. Jump Game](https://leetcode.cn/problems/jump-game/)

```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        // special case
        if (nums.empty() || nums.size() == 1) return true;

        int cover = 0;

        for (int i = 0; i <= cover; ++i) {
            cover = max(cover, i + nums[i]);
            if (cover >= nums.size() - 1) return true;
        }

        return false;
    }
};
```