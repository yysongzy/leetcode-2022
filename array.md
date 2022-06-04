[TOC]

### [704. 二分查找](https://leetcode-cn.com/problems/binary-search/)

```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        // special case
        if (nums.empty()) return -1;

        int left = 0;
        int right = nums.size() - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) {
                return mid;
            } else if (nums[mid] > target) {
                right = mid - 1;
            } else if (nums[mid] < target) {
                left = mid + 1;
            }
        }

        return -1;
    }
};
```

### [27. 移除元素](https://leetcode-cn.com/problems/remove-element/)

#### 冒泡移除法（由本人发明）

```cpp
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        // special case
        if (nums.empty()) return 0;

        int res = 0;
        for (int i = 0; i != nums.size(); ++i) {
            if (nums[i] == val) {
                for (int j = i + 1; j != nums.size(); ++j) {
                    if (nums[j] != val) {
                        ++res;
                        swap(nums[i], nums[j]);
                        break;
                    }
                }
            } else {
                ++res;
            }
        }

        nums.resize(res);
        return res;
    }
};
```

#### 双指针法，让一个 fastIndex 在前方探测是否有等于 val 的值，有则跳过

```cpp
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        // special case
        if (nums.empty()) return 0;

        int slowIndex = 0;
        for (int fastIndex = 0; fastIndex != nums.size(); ++fastIndex) {
            if (nums[fastIndex] != val) {
                nums[slowIndex++] = nums[fastIndex];
            }
        }

        nums.resize(slowIndex);
        return slowIndex;
    }
};
```

### [977. 有序数组的平方](https://leetcode-cn.com/problems/squares-of-a-sorted-array/)

#### 暴力

```cpp
class Solution {
public:
    vector<int> sortedSquares(vector<int>& nums) {
        for (auto& num : nums) {
            num = pow(num, 2);
        }

        sort(nums.begin(), nums.end());
        return nums;
    }
};
```

#### 双指针

```cpp
class Solution {
public:
    vector<int> sortedSquares(vector<int>& nums) {
        vector<int> res(nums.size(), 0);

        int k = nums.size() - 1;
        for (int left = 0, right = nums.size() - 1; left <= right;) {
            int powLeft = pow(nums[left], 2);
            int powRight = pow(nums[right], 2);
            if (powLeft < powRight) {
                res[k--] = powRight;
                --right;
            } else {
                res[k--] = powLeft;
                ++left;
            }
        }

        return res;
    }
};
```

### [209. 长度最小的子数组](https://leetcode-cn.com/problems/minimum-size-subarray-sum/)

#### 滑动窗口

```cpp
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        // special case
        if (nums.empty()) return 0;

        int res = INT32_MAX;
        int sum = 0;
        int left = 0;
        for (int right = 0; right != nums.size(); ++right) {
            sum += nums[right];

            while (sum >= target) {
                res = min(res, right - left + 1);
                sum -= nums[left++];
            }
        }

        return res == INT32_MAX ? 0 : res;
    }
};
```

### [59. 螺旋矩阵 II](https://leetcode-cn.com/problems/spiral-matrix-ii/)

#### 没必要做，看一遍即可

```cpp
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        // special case
        if (n <= 0) return {{}};

        vector<vector<int>> res(n, vector<int>(n, 0));
        int startX = 0, startY = 0;
        int loop = n / 2;
        int mid = n / 2;
        int count = 1;
        int offset = 1;

        int i = 0, j = 0;
        while (loop--) {
            i = startX;
            j = startY;

            // 下面开始的四个for就是模拟转了一圈
            // 模拟填充上行从左到右(左闭右开)
            for (j = startY; j < startY + n - offset; j++) {
                res[startX][j] = count++;
            }
            // 模拟填充右列从上到下(左闭右开)
            for (i = startX; i < startX + n - offset; i++) {
                res[i][j] = count++;
            }
            // 模拟填充下行从右到左(左闭右开)
            for (; j > startY; j--) {
                res[i][j] = count++;
            }
            // 模拟填充左列从下到上(左闭右开)
            for (; i > startX; i--) {
                res[i][j] = count++;
            }

            // 第二圈开始的时候，起始位置要各自加1， 例如：第一圈起始位置是(0, 0)，第二圈起始位置是(1, 1)
            ++startX;
            ++startY;

            // offset 控制每一圈里每一条边遍历的长度
            offset += 2;
        }

        // 如果n为奇数的话，需要单独给矩阵最中间的位置赋值
        if (n % 2) res[mid][mid] = count;

        return res;
    }
};
```

