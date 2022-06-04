[toc]

# for 循环横向遍历，递归纵向遍历，回溯不断调整结果集

### [77. 组合](https://leetcode-cn.com/problems/combinations/)

#### 未剪枝

```cpp
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;
    vector<vector<int>> combine(int n, int k) {
        backtracking(n, k, 1);

        return res;
    }

    void backtracking(int n, int k, int startIndex) {
        // 收集结果并返回
        if (path.size() == k) {
            res.push_back(path);
            return;
        }

        for (int i = startIndex; i <= n; ++i) {
            path.push_back(i);
            backtracking(n, k, i + 1);
            path.pop_back();
        }
    }
};
```

#### 剪枝

```cpp
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;
    vector<vector<int>> combine(int n, int k) {
        backtracking(n, k, 1);

        return res;
    }

    void backtracking(int n, int k, int startIndex) {
        // 收集结果并返回
        if (path.size() == k) {
            res.push_back(path);
            return;
        }

        for (int i = startIndex; i <= n - (k - path.size()) + 1; ++i) {
            path.push_back(i);
            backtracking(n, k, i + 1);
            path.pop_back();
        }
    }
};
```

### [216. Combination Sum III](https://leetcode.cn/problems/combination-sum-iii/)

#### 其实可以把 sum 当参数传递，减少每次计算 sum 的重复操作

```cpp
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;

    vector<vector<int>> combinationSum3(int k, int n) {
        backtracking(k, n, 1);
        return res;
    }

    void backtracking(int k, int n, int startIndex) {
        if (path.size() == k) {
            int sum = 0;
            for (const auto& num : path) {
                sum += num;
            }
            if (sum == n) {
                res.push_back(path);
                return;
            } else if (sum > n) {
                return;
            }
        } else if (path.size() > k) {
            return;
        }

        for (int i = startIndex; i <= 9; ++i) {
            path.push_back(i);
            backtracking(k, n, i + 1);
            path.pop_back();
        }
    }
};
```

### [17. Letter Combinations of a Phone Number](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/)

```cpp
class Solution {
public:
    vector<string> res;
    string path;
    int digitsSize = 0;
    vector<vector<char>> source;

    unordered_map<char, vector<char>> digit2LettersMap = {
        {'2', {'a', 'b', 'c'}},
        {'3', {'d', 'e', 'f'}},
        {'4', {'g', 'h', 'i'}},
        {'5', {'j', 'k', 'l'}},
        {'6', {'m', 'n', 'o'}},
        {'7', {'p', 'q', 'r', 's'}},
        {'8', {'t', 'u', 'v'}},
        {'9', {'w', 'x', 'y', 'z'}},
    };

    vector<string> letterCombinations(string digits) {
        // special case
        if (digits.empty()) return {};

        digitsSize = digits.size();
        for (const auto& ch : digits) {
            source.push_back(digit2LettersMap[ch]);
        }
        backtracking(0);
        return res;
    }

    void backtracking(int startIndex) {
        if (path.size() == digitsSize) {
            res.push_back(path);
            return;
        }

        auto letters = source[startIndex];
        int lettersSize = letters.size();
        for (int i = 0; i != lettersSize; ++i) {
            path.push_back(letters[i]);
            backtracking(startIndex + 1);
            path.pop_back();
        }
    }
};
```

### [39. Combination Sum](https://leetcode.cn/problems/combination-sum/)

```cpp
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;
    int targetSum = 0;
    vector<int> candidatesVec;

    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        targetSum = target;
        sort(candidates.begin(), candidates.end());
        candidatesVec = candidates;
        backtracking(0, 0);
        return res;
    }

    // 加上 startIndex 是为了去重，不往前取重复值
    void backtracking(int sum, int startIndex) {
        if (sum == targetSum) {
            res.push_back(path);
            return;
        } else if (sum > targetSum) {
            return;
        }

        for (int i = startIndex; i != candidatesVec.size(); ++i) {
            int candidate = candidatesVec[i];
            sum += candidate;
            path.push_back(candidate);
            // 不用 i + 1，表示可以重复读取当前的数
            backtracking(sum, i);
            path.pop_back();
            sum -= candidate;
        }
    }
};
```

### [40. Combination Sum II](https://leetcode.cn/problems/combination-sum-ii/)

#### 自创，得到结果后去重，会超时

```cpp
class Solution {
public:
    unordered_map<string, vector<int>> umap;
    vector<int> path;
    int targetSum = 0;
    vector<int> candidatesVec;

    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        candidatesVec = candidates;
        targetSum = target;
        backtracking(0, 0);
        vector<vector<int>> res;

        for (const auto& iter : umap) {
            res.push_back(iter.second);
        }

        return res;
    }

    void backtracking(int sum, int stratIndex) {
        if (sum == targetSum) {
            auto newPath = path;
            sort(newPath.begin(), newPath.end());
            std::string key;
            for (const auto i : newPath) {
                key += to_string(i);
            }
            umap[key] = newPath;
            return;
        } else if (sum > targetSum) {
            return;
        }

        for (int i = stratIndex; i != candidatesVec.size(); ++i) {
            auto candidate = candidatesVec[i];
            path.push_back(candidate);
            sum += candidate;
            backtracking(sum, i + 1);
            sum -= candidate;
            path.pop_back();
        }
    }
};
```

#### 直接抄的答案，用 used 数组，可以去题解中找找更好的答案

```cpp
class Solution {
private:
    vector<vector<int>> result;
    vector<int> path;
    void backtracking(vector<int>& candidates, int target, int sum, int startIndex, vector<bool>& used) {
        if (sum == target) {
            result.push_back(path);
            return;
        }
        for (int i = startIndex; i < candidates.size() && sum + candidates[i] <= target; i++) {
            // used[i - 1] == true，说明同一树枝candidates[i - 1]使用过
            // used[i - 1] == false，说明同一树层candidates[i - 1]使用过
            // 要对同一树层使用过的元素进行跳过
            if (i > 0 && candidates[i] == candidates[i - 1] && used[i - 1] == false) {
                continue;
            }
            sum += candidates[i];
            path.push_back(candidates[i]);
            used[i] = true;
            backtracking(candidates, target, sum, i + 1, used); // 和39.组合总和的区别1，这里是i+1，每个数字在每个组合中只能使用一次
            used[i] = false;
            sum -= candidates[i];
            path.pop_back();
        }
    }

public:
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        vector<bool> used(candidates.size(), false);
        path.clear();
        result.clear();
        // 首先把给candidates排序，让其相同的元素都挨在一起。
        sort(candidates.begin(), candidates.end());
        backtracking(candidates, target, 0, 0, used);
        return result;
    }
};
```

### [131. Palindrome Partitioning](https://leetcode.cn/problems/palindrome-partitioning/)

```cpp
class Solution {
public:
    vector<vector<string>> res;
    vector<string> path;

    vector<vector<string>> partition(string s) {
        backtracking(s, 0);
        return res;
    }

    void backtracking(const string& s, int startIndex) {
        if (startIndex >= s.size()) {
            res.push_back(path);
            return;
        }

        for (int i = startIndex; i != s.size(); ++i) {
            if (isPalindrome(s, startIndex, i)) {
                path.push_back(string(s.begin() + startIndex, s.begin() + i + 1));
                backtracking(s, i + 1);
                path.pop_back();
            } else {
                continue;
            }
        }
    }

    bool isPalindrome(const string& s, int startIndex, int i) {
        while (startIndex < i) {
            if (s[startIndex] != s[i]) return false;
            ++startIndex;
            --i;
        }

        return true;
    }
};
```

### [78. Subsets](https://leetcode.cn/problems/subsets/)

```cpp
class Solution {
public:
    // 在树形结构中子集问题是要收集所有节点的结果，而组合问题是收集叶子节点的结果
    // 收集 path 的位置不同了
    vector<vector<int>> res;
    vector<int> path;

    vector<vector<int>> subsets(vector<int>& nums) {
        backtracking(nums, 0);
        return res;
    }

    void backtracking(vector<int>& nums, int startIndex) {
        if (startIndex > nums.size()) return;
        res.push_back(path);

        for (int i = startIndex; i != nums.size(); ++i) {
            path.push_back(nums[i]);
            backtracking(nums, i + 1);
            path.pop_back();
        }
    }
};
```

### [90. Subsets II](https://leetcode.cn/problems/subsets-ii/)

```cpp
class Solution {
public:
    set<vector<int>> resSet;
    vector<int> path;

    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        backtracking(nums, 0);
        vector<vector<int>> res(resSet.begin(), resSet.end());
        return res;
    }

    void backtracking(vector<int>& nums, int startIndex) {
        if (resSet.count(path) == 0) resSet.insert(path);
        if (startIndex > nums.size()) return;

        for (int i = startIndex; i != nums.size(); ++i) {
            path.push_back(nums[i]);
            backtracking(nums, i + 1);
            path.pop_back();
        }
    }
};
```

### [491. Increasing Subsequences](https://leetcode.cn/problems/increasing-subsequences/)

```cpp
class Solution {
public:
    set<vector<int>> resSet;
    vector<int> path;

    vector<vector<int>> findSubsequences(vector<int>& nums) {
        backtracking(nums, 0);
        vector<vector<int>> res(resSet.begin(), resSet.end());
        return res;
    }

    void backtracking(vector<int>& nums, int startIndex) {
        if (path.size() > 1) resSet.insert(path);
        if (startIndex >= nums.size()) return;

        for (int i = startIndex; i != nums.size(); ++i) {
            if (path.empty() || nums[i] >= path.back()) {
                path.push_back(nums[i]);
                backtracking(nums, i + 1);
                path.pop_back();
            }
        }
    }
};
```

#### 用 unordered_set 去重，更推荐这种解法

```cpp
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;

    vector<vector<int>> findSubsequences(vector<int>& nums) {
        backtracking(nums, 0);
        return res;
    }

    void backtracking(vector<int>& nums, int startIndex) {
        if (path.size() > 1) res.push_back(path);
        if (startIndex >= nums.size()) return;

        unordered_set<int> usedNumSet;
        for (int i = startIndex; i != nums.size(); ++i) {
            if (usedNumSet.count(nums[i]) == 0 && (path.empty() || nums[i] >= path.back())) {
                path.push_back(nums[i]);
                usedNumSet.insert(nums[i]);
                backtracking(nums, i + 1);
                path.pop_back();
            }
        }
    }
};
```

### [46. Permutations](https://leetcode.cn/problems/permutations/)

#### used

```cpp
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;

    vector<vector<int>> permute(vector<int>& nums) {
        vector<bool> used(nums.size(), false);
        backtracking(nums, used);
        return res;
    }

    void backtracking(vector<int>& nums, vector<bool>& used) {
        if (path.size() == nums.size()) {
            res.push_back(path);
            return;
        }

        for (int i = 0; i != nums.size(); ++i) {
            if (used[i] == false) {
                path.push_back(nums[i]);
                used[i] = true;
                backtracking(nums, used);
                used[i] = false;
                path.pop_back();
            }
        }
    }
};
```

### [47. Permutations II](https://leetcode.cn/problems/permutations-ii/)

```cpp
class Solution {
public:
    set<vector<int>> resSet;
    vector<int> path;

    vector<vector<int>> permuteUnique(vector<int>& nums) {
        vector<bool> used(nums.size(), false);
        backtracking(nums, used);
        vector<vector<int>> res(resSet.begin(), resSet.end());
        return res;
    }

    void backtracking(vector<int>& nums, vector<bool>& used) {
        if (path.size() == nums.size()) {
            resSet.insert(path);
            return;
        }

        for (int i = 0; i != nums.size(); ++i) {
            if (used[i] == false) {
                path.push_back(nums[i]);
                used[i] = true;
                backtracking(nums, used);
                used[i] = false;
                path.pop_back();
            }
        }
    }
};
```

### [332. Reconstruct Itinerary](https://leetcode.cn/problems/reconstruct-itinerary/)

#### 还不会，复习时可以看答案再挑战一下

```cpp

```

### [51. N-Queens](https://leetcode.cn/problems/n-queens/)

#### 注意坐标系的取值

```cpp
class Solution {
public:
    vector<vector<string>> res;
    vector<string> chessboard;

    vector<vector<string>> solveNQueens(int n) {
        backtracking(n, 0);
        return res;
    }

    void backtracking(int n, int row) {
        if (chessboard.size() == n) {
            res.push_back(chessboard);
            return;
        }

        for (int i = 0; i != n; ++i) {
            string rowStr(n, '.');
            rowStr[i] = 'Q';
            if (isValid(chessboard, rowStr, i, n)) {
                chessboard.push_back(rowStr);
                backtracking(n, i + 1);
                chessboard.pop_back();
            }
        }
    }

    bool isValid(const vector<string>& chessboard, const string& rowStr, int i, int n) {
        int currentX = i + 1;
        int currentY = n - chessboard.size();

        for (int j = 0; j != chessboard.size(); ++j) {
            string aboveStr = chessboard[j];
            int aboveX = getAboveX(aboveStr) + 1;
            int aboveY = n - j;
            if (currentX == aboveX) {
                return false;
            }

            if (aboveX - currentX == aboveY - currentY) {
                return false;
            }

            if (currentX - aboveX == aboveY - currentY) {
                return false;
            }
        }

        return true;
    }

    int getAboveX(const string& aboveStr) {
        for (int i = 0; i != aboveStr.size(); ++i) {
            if (aboveStr[i] == 'Q') return i;
        }

        return -1;
    }
};
```