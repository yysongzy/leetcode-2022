[TOC]

### [242. 有效的字母异位词](https://leetcode-cn.com/problems/valid-anagram/)

```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        if (s.size() != t.size()) return false;

        unordered_map<char, int> umap;
        for (auto ch : s) ++umap[ch];
        for (auto ch : t) --umap[ch];

        for (auto iter = umap.begin(); iter != umap.end(); ++iter) {
            if (iter->second != 0) return false;
        }

        return true;
    }
};
```

### [349. 两个数组的交集](https://leetcode-cn.com/problems/intersection-of-two-arrays/)

```cpp
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        vector<int> res;
        // special case
        if (nums1.empty() || nums2.empty()) return res;

        unordered_set<int> uset(nums1.begin(), nums1.end());

        unordered_set<int> resSet;
        for (auto num : nums2) {
            if (uset.count(num) > 0) resSet.insert(num);
        }

        res.assign(resSet.begin(), resSet.end());
        return res;
    }
};
```

### [202. 快乐数](https://leetcode-cn.com/problems/happy-number/)

```cpp
class Solution {
public:
    bool isHappy(int n) {
        // special case
        if (n == 1) return true;

        unordered_set<int> uset;
        while (n != 1) {
            if (uset.count(n) > 0) return false;
            else uset.insert(n);

            int sum = 0;
            while (n) {
                sum += pow(n % 10, 2);
                n /= 10;
            }
            n = sum;
        }

        return true;
    }
};
```

### [1. 两数之和](https://leetcode-cn.com/problems/two-sum/)

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        // special case
        if (nums.empty()) return {};

        unordered_map<int, int> umap;
        for (int i = 0; i != nums.size(); ++i) {
            if (umap.count(target - nums[i]) > 0) return {i, umap[target - nums[i]]};
            else umap[nums[i]] = i;
        }

        return {};
    }
};
```

### [454. 四数相加 II](https://leetcode-cn.com/problems/4sum-ii/)

```cpp
class Solution {
public:
    int fourSumCount(vector<int>& nums1, vector<int>& nums2, vector<int>& nums3, vector<int>& nums4) {
        unordered_map<int, int> umap;

        for (auto num1 : nums1)
            for (auto num2 : nums2) 
                ++umap[num1 + num2];
        
        int res = 0;
        for (auto num3 : nums3) {
            for (auto num4 : nums4) {
                if (umap.count(0 - (num3 + num4)) > 0) {
                    res += umap[0 - (num3 + num4)];
                }
            }
        }
        
        return res;
    }
};
```

### [383. 赎金信](https://leetcode-cn.com/problems/ransom-note/)

```cpp
class Solution {
public:
    bool canConstruct(string ransomNote, string magazine) {
        // special case
        if (ransomNote.empty() && magazine.empty()) return true;
        else if (ransomNote.empty() || magazine.empty()) return false;

        unordered_map<char, int> umap;
        for (auto ch : magazine) {
            ++umap[ch];
        }

        for (auto ch : ransomNote) {
            --umap[ch];
        }

        for (auto iter : umap) {
            if (iter.second < 0) return false;
        }

        return true;
    }
};
```

### [15. 三数之和](https://leetcode-cn.com/problems/3sum/)

#### 双指针

```cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        // special case
        if (nums.size() < 3) return {};

        vector<vector<int>> res;
        sort(nums.begin(), nums.end());
        // 找出 a + b + c = 0
        // a = nums[i], b = nums[left], c = nums[right]

        for (int i = 0; i != nums.size(); ++i) {
            if (nums[i] > 0) return res;

            // 错误去重方法，将会漏掉 -1,-1,2 这种情况
            /*
            if (nums[i] == nums[i + 1]) {
                continue;
            }
            */
            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }

            int left = i + 1;
            int right = nums.size() - 1;
            while (left < right) {
                if (nums[i] + nums[left] + nums[right] > 0) {
                    while (left < right && nums[right - 1] == nums[right]) --right;
                    --right;
                } else if (nums[i] + nums[left] + nums[right] < 0) {
                    while (left < right && nums[left + 1] == nums[left]) ++left;
                    ++left;
                } else {
                    res.push_back({nums[i], nums[left], nums[right]});
                    
                    while (left < right && nums[right - 1] == nums[right]) --right;
                    while (left < right && nums[left + 1] == nums[left]) ++left;

                    --right;
                    ++left;
                }
            }
        }

        return res;
    }
};
```

### [18. 四数之和](https://leetcode-cn.com/problems/4sum/)

#### 抄答案的

```cpp
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        vector<vector<int>> result;
        sort(nums.begin(), nums.end());
        for (int k = 0; k < nums.size(); k++) {
            // 这种剪枝是错误的，这道题目target 是任意值
            // if (nums[k] > target) {
            //     return result;
            // }
            // 去重
            if (k > 0 && nums[k] == nums[k - 1]) {
                continue;
            }
            for (int i = k + 1; i < nums.size(); i++) {
                // 正确去重方法
                if (i > k + 1 && nums[i] == nums[i - 1]) {
                    continue;
                }
                int left = i + 1;
                int right = nums.size() - 1;
                while (right > left) {
                    // nums[k] + nums[i] + nums[left] + nums[right] > target 会溢出
                    if (nums[k] + nums[i] > target - (nums[left] + nums[right])) {
                        right--;
                    } else if (nums[k] + nums[i] < target - (nums[left] + nums[right])) {
                        left++;
                    } else {
                        result.push_back(vector<int>{nums[k], nums[i], nums[left], nums[right]});
                        // 去重逻辑应该放在找到一个四元组之后
                        while (right > left && nums[right] == nums[right - 1]) right--;
                        while (right > left && nums[left] == nums[left + 1]) left++;

                        // 找到答案时，双指针同时收缩
                        right--;
                        left++;
                    }
                }

            }
        }
        return result;
    }
};
```

