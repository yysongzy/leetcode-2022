[TOC]

### [232. 用栈实现队列](https://leetcode-cn.com/problems/implement-queue-using-stacks/)

```cpp
class MyQueue {
public:
    MyQueue() {

    }
    
    void push(int x) {
        stkIn_.push(x);
    }
    
    int pop() {
        int top = peek();
        stkOut_.pop();
        return top;
    }
    
    int peek() {
        if (stkOut_.empty()) {
            while (!stkIn_.empty()) {
                stkOut_.push(stkIn_.top());
                stkIn_.pop();
            }
        }
        return stkOut_.top();
    }
    
    bool empty() {
        return stkIn_.empty() && stkOut_.empty();
    }

private:
    stack<int> stkIn_;
    stack<int> stkOut_;
};

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue* obj = new MyQueue();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->peek();
 * bool param_4 = obj->empty();
 */
```

### [225. 用队列实现栈](https://leetcode-cn.com/problems/implement-stack-using-queues/)

#### 两个队列

```cpp
class MyStack {
public:
    MyStack() {

    }
    
    void push(int x) {
        que2_.push(x);
        while (!que1_.empty()) {
            que2_.push(que1_.front());
            que1_.pop();
        }

        queue<int> tmp = que1_;
        que1_ = que2_;
        que2_ = tmp;
    }
    
    int pop() {
        int res = top();
        que1_.pop();
        return res;
    }
    
    int top() {
        return que1_.front();
    }
    
    bool empty() {
        return que1_.empty();
    }

private:
    queue<int> que1_;
    queue<int> que2_;
};

/**
 * Your MyStack object will be instantiated and called as such:
 * MyStack* obj = new MyStack();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->top();
 * bool param_4 = obj->empty();
 */
```

#### 一个队列

```cpp
class MyStack {
public:
    MyStack() {

    }
    
    void push(int x) {
        int oldLenth = que.size();

        que.push(x);
        while (oldLenth--) {
            que.push(que.front());
            que.pop();
        }
    }
    
    int pop() {
        int res = top();
        que.pop();
        return res;
    }
    
    int top() {
        return que.front();
    }
    
    bool empty() {
        return que.empty();
    }

private:
    queue<int> que;
};

/**
 * Your MyStack object will be instantiated and called as such:
 * MyStack* obj = new MyStack();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->top();
 * bool param_4 = obj->empty();
 */
```

### [20. 有效的括号](https://leetcode-cn.com/problems/valid-parentheses/)

```cpp
class Solution {
public:
    bool isValid(string s) {
        // special case
        if (s.empty()) return false;
        else if (s.size() % 2 == 1) return false;

        unordered_map<char, char> umap = {
            {')', '('},
            {']', '['},
            {'}', '{'},
        };

        stack<char> stk;

        for (auto ch : s) {
            if (stk.empty()) {
                stk.push(ch);
            } else {
                if (stk.top() == umap[ch]) {
                    stk.pop();
                } else {
                    stk.push(ch);
                }
            }
        }

        return stk.empty();
    }
};
```

### [1047. 删除字符串中的所有相邻重复项](https://leetcode-cn.com/problems/remove-all-adjacent-duplicates-in-string/)

#### 使用 std::stack

```cpp
class Solution {
public:
    string removeDuplicates(string s) {
        // special case
        if (s.empty()) return "";

        stack<char> stk;
        for (auto ch : s) {
            if (stk.empty()) {
                stk.push(ch);
            } else {
                if (stk.top() == ch) stk.pop();
                else stk.push(ch);
            }
        }

        string res;
        while (!stk.empty()) {
            res.push_back(stk.top());
            stk.pop();
        }

        reverse(res.begin(), res.end());

        return res;
    }
};
```

#### 直接使用字符串作为栈

```cpp
class Solution {
public:
    string removeDuplicates(string s) {
        // special case
        if (s.empty()) return "";

        string res;
        for (auto ch : s) {
            if (res.empty()) {
                res.push_back(ch);
            } else {
                if (res.back() == ch) res.pop_back();
                else res.push_back(ch);
            }
        }

        return res;
    }
};
```

### [150. 逆波兰表达式求值](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)

```cpp
class Solution {
public:
    int evalRPN(vector<string>& tokens) {
        // special case
        if (tokens.empty()) return 0;

        stack<int> stk;
        for (const auto& token : tokens) {
            if (token == "+") {
                auto nums = getTwoNums(stk);
                stk.push(nums.second + nums.first);
            } else if (token == "-") {
                auto nums = getTwoNums(stk);
                stk.push(nums.second - nums.first);
            } else if (token == "*") {
                auto nums = getTwoNums(stk);
                stk.push(nums.second * nums.first);
            } else if (token == "/") {
                auto nums = getTwoNums(stk);
                stk.push(nums.second / nums.first);
            } else {
                stk.push(stoi(token));
            }
        }

        return stk.top();
    }

    pair<int, int> getTwoNums(stack<int>& stk) {
        auto num1 = stk.top();
        stk.pop();
        auto num2 = stk.top();
        stk.pop();
        
        return {num1, num2};
    }
};
```

### [239. 滑动窗口最大值](https://leetcode-cn.com/problems/sliding-window-maximum/)

#### 自己写的超时算法

```cpp
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        // special case
        if (k > nums.size()) return {};

        vector<int> res;

        queue<int> que;
        int x = k;
        while (x != 0) {
            que.push(nums[k - x]);
            --x;
        }
        int maxValue = getMaxValueOfQueue(que);
        res.push_back(maxValue);

        for (int i = k; i != nums.size(); ++i) {
            que.pop();
            que.push(nums[i]);

            maxValue = getMaxValueOfQueue(que);
            res.push_back(maxValue);
        }

        return res;
    }

    int getMaxValueOfQueue(queue<int>& que) {
        int maxValue = INT32_MIN;

        int queSize = que.size();
        for (int i = 0; i != queSize; ++i) {
            maxValue = max(maxValue, que.front());
            que.push(que.front());
            que.pop();
        }

        return maxValue;
    }
};
```

#### 单调队列

```cpp
```

### [347. 前 K 个高频元素](https://leetcode-cn.com/problems/top-k-frequent-elements/)

#### 对 map 的 value 进行排序

#### [C++ STL中Map的按Key排序和按Value排序](https://blog.csdn.net/iicy266/article/details/11906189)

#### [reference to non-static member function must be called](https://blog.csdn.net/qq_26849233/article/details/77930991)

```cpp
class Solution {
private:
    // 不定义 static 就会有 this 指针，传入 sort 库函数时就会有问题
    static bool cmp(const pair<int, int>& lhs, const pair<int, int>& rhs) {
        return lhs.second > rhs.second;
    }

public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        // special case
        if (nums.empty() || k > nums.size()) return {};

        map<int, int, less<int>> map;
        for (auto num : nums) {
            ++map[num];
        }

        vector<pair<int, int>> vec(map.begin(), map.end());
        sort(vec.begin(), vec.end(), cmp);


        vector<int> res;
        for (const auto& iter : vec) {
            res.push_back(iter.first);
            --k;
            if (k == 0) break;
        }

        return res;
    }
};
```

#### 优先级队列

```cpp
```

