[toc]

### [144. 二叉树的前序遍历](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/)

#### 递归

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
    vector<int> preorderTraversal(TreeNode* root) {
        // special case
        if (!root) return {};

        vector<int> res;
        preorder(root, res);
        return res;
    }

    void preorder(TreeNode* root, vector<int>& res) {
        // base case
        if (!root) return;

        res.push_back(root->val);
        preorder(root->left, res);
        preorder(root->right, res);
    }
};
```

#### 迭代

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
    vector<int> preorderTraversal(TreeNode* root) {
        // special case
        if (!root) return {};

        vector<int> res;
        stack<TreeNode*> stk;

        stk.push(root);
        while (!stk.empty()) {
            // 访问中节点
            TreeNode* node = stk.top();
            res.push_back(node->val);
            stk.pop();
            // 先压入右孩子，再压入左孩子，这样出栈顺序是先左孩子再右孩子
            if (node->right) stk.push(node->right);
            if (node->left) stk.push(node->left);
        }

        return res;
    }
};
```

### [145. 二叉树的后序遍历](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/)

#### 递归

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
    vector<int> postorderTraversal(TreeNode* root) {
        // special case
        if (!root) return {};

        vector<int> res;
        postorder(root, res);
        return res;
    }

    void postorder(TreeNode* root, vector<int>& res) {
        // base case
        if (!root) return;

        postorder(root->left, res);
        postorder(root->right, res);
        res.push_back(root->val);
    }
};
```

#### 迭代 tricky

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
    vector<int> postorderTraversal(TreeNode* root) {
        // special case
        if (!root) return {};

        // 前序遍历：中左右 -> 中右左 -> 左右中
        // 后序遍历：左右中
        vector<int> res;
        stack<TreeNode*> stk;

        stk.push(root);
        while (!stk.empty()) {
            TreeNode* node = stk.top();
            stk.pop();
            res.push_back(node->val);

            if (node->left) stk.push(node->left);
            if (node->right) stk.push(node->right);
        }

        reverse(res.begin(), res.end());
        return res;
    }
};
```

#### 真正的迭代

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
    vector<int> postorderTraversal(TreeNode* root) {
        // special case
        if (!root) return {};

        vector<int> res;
        stack<TreeNode*> stk;

        TreeNode* cur = root;
        TreeNode* pre = nullptr;
        while (cur || !stk.empty()) {
            while (cur) {
                stk.push(cur);
                cur = cur->left;
            }
            cur = stk.top();
            stk.pop();
            if (!cur->right || cur->right == pre) {
                res.push_back(cur->val);
                pre = cur;
                cur = nullptr;
            } else {
                stk.push(cur);
                cur = cur->right;
            }
        }

        return res;
    }
};
```

#### 注释版

```java
class Solution{
    public List<Integer> method1(TreeNode root) {
        List<Integer> ans=new LinkedList<>();
        Stack<TreeNode> stack=new Stack<>();
        TreeNode prev=null;
        //主要思想：
        //由于在某颗子树访问完成以后，接着就要回溯到其父节点去
        //因此可以用prev来记录访问历史，在回溯到父节点时，可以由此来判断，上一个访问的节点是否为右子树
        while(root!=null||!stack.isEmpty()){
            while(root!=null){
                stack.push(root);
                root=root.left;
            }
            //从栈中弹出的元素，左子树一定是访问完了的
            root=stack.pop();
            //现在需要确定的是是否有右子树，或者右子树是否访问过
            //如果没有右子树，或者右子树访问完了，也就是上一个访问的节点是右子节点时
            //说明可以访问当前节点
            if(root.right==null||prev==root.right){
                ans.add(root.val);
                //更新历史访问记录，这样回溯的时候父节点可以由此判断右子树是否访问完成
                prev=root;
                root=null;
            }else{
            //如果右子树没有被访问，那么将当前节点压栈，访问右子树
                stack.push(root);
                root=root.right;
            }
        }
        return ans;
    }
}
```

### [94. 二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

#### 递归

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
    vector<int> inorderTraversal(TreeNode* root) {
        // special case
        if (!root) return {};

        vector<int> res;
        inorder(root, res);
        return res;
    }

    void inorder(TreeNode* root, vector<int>& res) {
        // base case
        if (!root) return;

        inorder(root->left, res);
        res.push_back(root->val);
        inorder(root->right, res);
    }
};
```

#### 迭代

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
    vector<int> inorderTraversal(TreeNode* root) {
        // special case
        if (!root) return {};

        vector<int> res;
        stack<TreeNode*> stk;

        TreeNode* cur = root;
        while (cur || !stk.empty()) {
            while (cur) {
                stk.push(cur);
                cur = cur->left;
            }
            cur = stk.top();
            stk.pop();
            res.push_back(cur->val);
            cur = cur->right;
        }

        return res;
    }
};
```

### [102. 二叉树的层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

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
    vector<vector<int>> levelOrder(TreeNode* root) {
        // special case
        if (!root) return {};

        vector<vector<int>> res;
        queue<TreeNode*> que;
        que.push(root);

        while (!que.empty()) {
            auto length = que.size();
            vector<int> level;
            while (length--) {
                TreeNode* node = que.front();
                que.pop();
                level.push_back(node->val);
                if (node->left) que.push(node->left);
                if (node->right) que.push(node->right);
            }
            res.push_back(level);
        }

        return res;
    }
};
```

### [107. 二叉树的层序遍历 II](https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/)

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
    vector<vector<int>> levelOrderBottom(TreeNode* root) {
        // special case
        if (!root) return {};

        vector<vector<int>> res;
        queue<TreeNode*> que;
        que.push(root);

        while (!que.empty()) {
            auto length = que.size();
            vector<int> level;
            while (length--) {
                TreeNode* node = que.front();
                que.pop();
                level.push_back(node->val);
                if (node->left) que.push(node->left);
                if (node->right) que.push(node->right);
            }
            res.push_back(level);
        }

        reverse(res.begin(), res.end());
        return res;
    }
};
```

### [199. 二叉树的右视图](https://leetcode-cn.com/problems/binary-tree-right-side-view/)

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
    vector<int> rightSideView(TreeNode* root) {
        // special case
        if (!root) return {};

        vector<int> res;
        queue<TreeNode*> que;

        que.push(root);
        while (!que.empty()) {
            auto length = que.size();
            while (length--) {
                auto node = que.front();
                que.pop();
                if (length == 0) res.push_back(node->val);

                if (node->left) que.push(node->left);
                if (node->right) que.push(node->right);
            }
        }

        return res;
    }
};
```

### [637. 二叉树的层平均值](https://leetcode-cn.com/problems/average-of-levels-in-binary-tree/)

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
    vector<double> averageOfLevels(TreeNode* root) {
        // special case
        if (!root) return {};

        vector<double> res;
        queue<TreeNode*> que;

        que.push(root);
        while (!que.empty()) {
            auto length = que.size();
            double sum = 0;
            for (int i = 0; i != length; ++i) {
                auto node = que.front();
                que.pop();
                sum += node->val;
                
                if (node->left) que.push(node->left);
                if (node->right) que.push(node->right);
            }
            res.push_back(sum / length);
        }

        return res;
    }
};
```

### [429. N 叉树的层序遍历](https://leetcode-cn.com/problems/n-ary-tree-level-order-traversal/)

```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    vector<Node*> children;

    Node() {}

    Node(int _val) {
        val = _val;
    }

    Node(int _val, vector<Node*> _children) {
        val = _val;
        children = _children;
    }
};
*/

class Solution {
public:
    vector<vector<int>> levelOrder(Node* root) {
        // special case
        if (!root) return {};

        vector<vector<int>> res;
        queue<Node*> que;

        que.push(root);
        while (!que.empty()) {
            auto length = que.size();
            vector<int> level;
            while (length--) {
                Node* node = que.front();
                que.pop();
                level.push_back(node->val);

                if (!node->children.empty()) {
                    for (auto child : node->children) {
                        que.push(child);
                    }
                }
            }
            res.push_back(level);
        }

        return res;
    }
};
```

### [515. 在每个树行中找最大值](https://leetcode-cn.com/problems/find-largest-value-in-each-tree-row/)

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
    vector<int> largestValues(TreeNode* root) {
        // special case
        if (!root) return {};

        vector<int> res;
        queue<TreeNode*> que;

        que.push(root);
        while (!que.empty()) {
            int length = que.size();
            int maxValue = INT32_MIN;
            while (length--) {
                TreeNode* node = que.front();
                que.pop();
                maxValue = max(maxValue, node->val);

                if (node->left) que.push(node->left);
                if (node->right) que.push(node->right);
            }
            res.push_back(maxValue);
        }

        return res;
    }
};
```

### [116. 填充每个节点的下一个右侧节点指针](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/)

#### 队列

```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* left;
    Node* right;
    Node* next;

    Node() : val(0), left(NULL), right(NULL), next(NULL) {}

    Node(int _val) : val(_val), left(NULL), right(NULL), next(NULL) {}

    Node(int _val, Node* _left, Node* _right, Node* _next)
        : val(_val), left(_left), right(_right), next(_next) {}
};
*/

class Solution {
public:
    Node* connect(Node* root) {
        // special case
        if (!root) return nullptr;

        queue<Node*> que;

        // root->next = nullptr;
        que.push(root);
        while (!que.empty()) {
            int length = que.size();
            for (int i = 0; i != length; ++i) {
                Node* node = que.front();
                que.pop();

                if (i == length - 1) {
                    // node->next = nullptr;
                } else {
                    node->next = que.front();
                }

                if (node->left) que.push(node->left);
                if (node->right) que.push(node->right);
            }
        }

        return root;
    }
};
```

#### 递归

```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* left;
    Node* right;
    Node* next;

    Node() : val(0), left(NULL), right(NULL), next(NULL) {}

    Node(int _val) : val(_val), left(NULL), right(NULL), next(NULL) {}

    Node(int _val, Node* _left, Node* _right, Node* _next)
        : val(_val), left(_left), right(_right), next(_next) {}
};
*/

class Solution {
public:
    Node* connect(Node* root) {
        // special case
        if (!root) return nullptr;

        connectTwoNodes(root->left, root->right);

        return root;
    }

    void connectTwoNodes(Node* node1, Node* node2) {
        // base case
        if (!node1 || !node2) return;

        // 将传入的两个节点相连
        node1->next = node2;

        // 连接相同父节点的两个子节点
        connectTwoNodes(node1->left, node1->right);
        connectTwoNodes(node2->left, node2->right);

        // 连接跨越父节点的两个子节点
        connectTwoNodes(node1->right, node2->left);
    }
};
```

### [117. 填充每个节点的下一个右侧节点指针 II](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node-ii/)

#### 队列

```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* left;
    Node* right;
    Node* next;

    Node() : val(0), left(NULL), right(NULL), next(NULL) {}

    Node(int _val) : val(_val), left(NULL), right(NULL), next(NULL) {}

    Node(int _val, Node* _left, Node* _right, Node* _next)
        : val(_val), left(_left), right(_right), next(_next) {}
};
*/

class Solution {
public:
    Node* connect(Node* root) {
        // special case
        if (!root) return nullptr;

        queue<Node*> que;
        
        root->next = nullptr;
        que.push(root);
        while (!que.empty()) {
            int length = que.size();
            while (length--) {
                Node* node = que.front();
                que.pop();
                if (length == 0) {
                    node->next = nullptr;
                } else {
                    node->next = que.front();
                }

                if (node->left) que.push(node->left);
                if (node->right) que.push(node->right);
            }
        }

        return root;
    }
};
```

### [104. 二叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

#### 递归

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
    int maxDepth(TreeNode* root) {
        // base case
        if (!root) return 0;

        return 1 + max(maxDepth(root->left), maxDepth(root->right));
    }
};
```

#### 层序遍历

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
    int maxDepth(TreeNode* root) {
        // special case
        if (!root) return 0;

        int res = 0;
        queue<TreeNode*> que;

        que.push(root);
        while (!que.empty()) {
            int length = que.size();
            ++res;
            while (length--) {
                TreeNode* node = que.front();
                que.pop();
                
                if (node->left) que.push(node->left);
                if (node->right) que.push(node->right);
            }
        }

        return res;
    }
};
```

### [111. 二叉树的最小深度](https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/)

#### 递归

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
    int minDepth(TreeNode* root) {
        // base case
        if (!root) return 0;

        if (!root->left) return 1 + minDepth(root->right);
        else if (!root->right) return 1 + minDepth(root->left);
        else return 1 + min(minDepth(root->left), minDepth(root->right));
    }
};
```

#### 层序遍历

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
    int minDepth(TreeNode* root) {
        // special case
        if (!root) return 0;

        int res = 0;
        queue<TreeNode*> que;

        que.push(root);
        while (!que.empty()) {
            int length = que.size();
            ++res;
            while (length--) {
                TreeNode* node = que.front();
                que.pop();
                
                if (!node->left && !node->right) return res;
                
                if (node->left) que.push(node->left);
                if (node->right) que.push(node->right);
            }
        }

        return res;
    }
};
```

### [226. 翻转二叉树](https://leetcode-cn.com/problems/invert-binary-tree/)

#### 递归

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
    TreeNode* invertTree(TreeNode* root) {
        // base case
        if (!root) return nullptr;

        TreeNode* leftNode = invertTree(root->left);
        TreeNode* rightNode = invertTree(root->right);

        root->left = rightNode;
        root->right = leftNode;

        return root;
    }
};
```

#### 栈

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
    TreeNode* invertTree(TreeNode* root) {
        // special case
        if (!root) return nullptr;

        stack<TreeNode*> stk;
        stk.push(root);
        while (!stk.empty()) {
            TreeNode* node = stk.top();
            stk.pop();
            swap(node->left, node->right);

            if (node->right) stk.push(node->right);
            if (node->left) stk.push(node->left);
        }

        return root;
    }
};
```

#### 队列，层序遍历

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
    TreeNode* invertTree(TreeNode* root) {
        // special case
        if (!root) return nullptr;

        queue<TreeNode*> que;
        que.push(root);
        while (!que.empty()) {
            TreeNode* node = que.front();
            que.pop();
            swap(node->left, node->right);

            if (node->right) que.push(node->right);
            if (node->left) que.push(node->left);
        }

        return root;
    }
};
```

### [101. 对称二叉树](https://leetcode-cn.com/problems/symmetric-tree/)

#### 递归

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
    bool isSymmetric(TreeNode* root) {
        // special case
        if (!root) return true;

        return check(root->left, root->right);
    }

    bool check(TreeNode* leftNode, TreeNode* rightNode) {
        // base case
        if (!leftNode && !rightNode) return true;
        else if (!leftNode || !rightNode) return false;

        return leftNode->val == rightNode->val && check(leftNode->left, rightNode->right) && check(leftNode->right, rightNode->left);
    }
};
```

#### 队列

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
    bool isSymmetric(TreeNode* root) {
        // special case
        if (!root) return true;
        else if (!root->left && !root->right) return true;
        else if (!root->left || !root->right) return false;

        queue<TreeNode*> que;
        
        que.push(root->left);
        que.push(root->right);
        while (!que.empty()) {
            TreeNode* leftNode = que.front();
            que.pop();
            TreeNode* rightNode = que.front();
            que.pop();

            if (leftNode->val != rightNode->val) return false;

            if (leftNode->left && rightNode->right) {
                que.push(leftNode->left);
                que.push(rightNode->right);
            } else if (!leftNode->left && !rightNode->right) {

            } else {
                return false;
            }

            if (leftNode->right && rightNode->left) {
                que.push(leftNode->right);
                que.push(rightNode->left);
            } else if (!leftNode->right && !rightNode->left) {

            } else {
                return false;
            }
        }

        return true;
    }
};
```

#### 栈

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
    bool isSymmetric(TreeNode* root) {
        // special case
        if (!root) return true;

        stack<TreeNode*> stk;
        
        stk.push(root->left);
        stk.push(root->right);
        while (!stk.empty()) {
            TreeNode* leftNode = stk.top();
            stk.pop();
            TreeNode* rightNode = stk.top();
            stk.pop();

            if (!leftNode && !rightNode) continue;
            else if (!leftNode || !rightNode || leftNode->val != rightNode->val) return false;

            stk.push(leftNode->left);
            stk.push(rightNode->right);
            stk.push(leftNode->right);
            stk.push(rightNode->left);

        }

        return true;
    }
};
```

### [559. N 叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-n-ary-tree/)

#### 递归

```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    vector<Node*> children;

    Node() {}

    Node(int _val) {
        val = _val;
    }

    Node(int _val, vector<Node*> _children) {
        val = _val;
        children = _children;
    }
};
*/

class Solution {
public:
    int maxDepth(Node* root) {
        // base case
        if (!root) return 0;

        int maxValue = 0;
        for (auto child : root->children) {
            int depth = maxDepth(child);
            maxValue = max(maxValue, depth);
        }

        return 1 + maxValue;
    }
};
```

#### 层序遍历

```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    vector<Node*> children;

    Node() {}

    Node(int _val) {
        val = _val;
    }

    Node(int _val, vector<Node*> _children) {
        val = _val;
        children = _children;
    }
};
*/

class Solution {
public:
    int maxDepth(Node* root) {
        // special case
        if (!root) return 0;

        int res = 0;
        queue<Node*> que;

        que.push(root);
        while (!que.empty()) {
            ++res;
            int length = que.size();
            while (length--) {
                Node* node = que.front();
                que.pop();

                for (auto child : node->children) {
                    if (child) {
                        que.push(child);
                    }
                }
            }
        }

        return res;
    }
};
```

### [222. 完全二叉树的节点个数](https://leetcode-cn.com/problems/count-complete-tree-nodes/)

#### 递归或层序

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
    int countNodes(TreeNode* root) {
        // base case
        if (!root) return 0;

        return 1 + countNodes(root->left) + countNodes(root->right);
    }
};
```

#### 利用完全二叉树的节点

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
    // 可以看出如果整个树不是满二叉树，就递归其左右孩子，直到遇到满二叉树为止，用公式计算这个子树（满二叉树）的节点数量
    int countNodes(TreeNode* root) {
        // special case
        if (!root) return 0;

        auto leftNode = root->left;
        auto rightNode = root->right;

        int leftHeight = 0;
        int rightHeight = 0;

        while (leftNode) {
            leftNode =leftNode->left;
            ++leftHeight;
        }

        while (rightNode) {
            rightNode =rightNode->right;
            ++rightHeight;
        }

        if (leftHeight == rightHeight) return (2 << leftHeight) - 1;
        else return 1 + countNodes(root->left) + countNodes(root->right);

    }
};
```

### [110. 平衡二叉树](https://leetcode-cn.com/problems/balanced-binary-tree/)

#### 递归

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
    bool isBalanced(TreeNode* root) {
        // special case
        if (!root) return true;

        return abs(depth(root->left) - depth(root->right)) <= 1 && isBalanced(root->left) && isBalanced(root->right);
    }

    int depth(TreeNode* root) {
        // base case
        if (!root) return 0;

        return 1 + max(depth(root->left), depth(root->right));
    }
};
```

### [257. 二叉树的所有路径](https://leetcode-cn.com/problems/binary-tree-paths/) 好题，值得多做几遍

#### DFS

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
    vector<string> binaryTreePaths(TreeNode* root) {
        vector<string> res;
        constructPath(root, "", res);
        return res;
    }

    void constructPath(TreeNode* cur, string path, vector<string>& res) {
        // base case
        if (!cur) return;

        path += to_string(cur->val);

        if (!cur->left && !cur->right) {    // 如果是叶子节点
            res.push_back(path);
        } else {                            // 如果不是叶子节点
            path += "->";
            constructPath(cur->left, path, res);
            constructPath(cur->right, path, res);
        }
    }
};
```

#### 回溯，还不会

```cpp

```
#### 栈

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
    vector<string> binaryTreePaths(TreeNode* root) {
        // special case
        if (!root) return {};

        vector<string> res;     // 保存最终路径集合
        stack<TreeNode*> stk;   // 保存树的遍历节点
        stack<string> path;     // 保存遍历路径的节点
        
        stk.push(root);
        path.push(to_string(root->val));

        while (!stk.empty()) {
            TreeNode* node = stk.top();
            stk.pop();

            string pathStr = path.top();
            path.pop();

            if (!node->left && !node->right) res.push_back(pathStr);

            if (node->left) {
                stk.push(node->left);
                path.push(pathStr + "->" + to_string(node->left->val));
            }

            if (node->right) {
                stk.push(node->right);
                path.push(pathStr + "->" + to_string(node->right->val));
            }
        }

        return res;
    }
};
```

### [404. 左叶子之和](https://leetcode-cn.com/problems/sum-of-left-leaves/)

#### 递归

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
    int sumOfLeftLeaves(TreeNode* root) {
        // special case
        if (!root) return 0;

        int sum = 0;
        traverse(root, sum);
        return sum;
    }

    void traverse(TreeNode* root, int& sum) {
        // base case
        if (!root) return;

        if (root->left && !root->left->left && !root->left->right) sum += root->left->val;

        traverse(root->left, sum);
        traverse(root->right, sum);
    }
};
```

#### 迭代

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
    int sumOfLeftLeaves(TreeNode* root) {
        // special case
        if (!root) return 0;

        int sum = 0;
        stack<TreeNode*> stk;

        stk.push(root);
        while (!stk.empty()) {
            auto node = stk.top();
            stk.pop();

            if (node->left && !node->left->left && !node->left->right) sum += node->left->val;

            if (node->right) stk.push(node->right);
            if (node->left) stk.push(node->left);
        }

        return sum;
    }
};
```

### [513. 找树左下角的值](https://leetcode-cn.com/problems/find-bottom-left-tree-value/)

#### 层序遍历

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
    int findBottomLeftValue(TreeNode* root) {
        // special case
        if (!root) return 0;

        int res = 0;
        queue<TreeNode*> que;
        
        que.push(root);
        while (!que.empty()) {
            int length = que.size();
            for (int i = 0; i != length; ++i) {
                auto node = que.front();
                que.pop();

                if (i == 0) res = node->val;

                if (node->left) que.push(node->left);
                if (node->right) que.push(node->right);
            }
        }

        return res;
    }
};
```

### [112. 路径总和](https://leetcode-cn.com/problems/path-sum/)

#### 回溯

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
    bool res = false;
    int _targetSum = 0;

    bool hasPathSum(TreeNode* root, int targetSum) {
        int sum = 0;
        _targetSum = targetSum;
        traverse(root, sum);
        return res;
    }

    void traverse(TreeNode* root, int& sum) {
        // base case
        if (!root) return;

        sum += root->val;
        if (!root->left && !root->right && sum == _targetSum) res = true;

        traverse(root->left, sum);
        traverse(root->right, sum);

        sum -= root->val;
    }
};
```

#### 递归

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
    bool hasPathSum(TreeNode* root, int targetSum) {
        // base case
        if (!root) return false;

        if (!root->left && !root->right && targetSum == root->val) return true;

        return hasPathSum(root->left, targetSum - root->val) || hasPathSum(root->right, targetSum - root->val);
    }
};
```

#### 迭代，栈

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
    bool hasPathSum(TreeNode* root, int targetSum) {
        // special case
        if (!root) return false;

        stack<pair<TreeNode*, int>> stk;
        stk.push({root, root->val});
        while (!stk.empty()) {
            auto pairValue = stk.top();
            stk.pop();

            if (!pairValue.first->left && !pairValue.first->right && pairValue.second == targetSum) return true;

            if (pairValue.first->left) stk.push({pairValue.first->left, pairValue.second + pairValue.first->left->val});
            if (pairValue.first->right) stk.push({pairValue.first->right, pairValue.second + pairValue.first->right->val});
        }

        return false;
    }
};
```

### [113. 路径总和 II](https://leetcode-cn.com/problems/path-sum-ii/)

#### 回溯

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
    int target = 0;
    vector<vector<int>> res;

    vector<vector<int>> pathSum(TreeNode* root, int targetSum) {
        // special case
        if (!root) return {};

        
        int sum = 0;
        target = targetSum;
        vector<int> path;
        traverse(root, sum, path);
        return res;
    }

    void traverse(TreeNode* root, int& sum, vector<int>& path) {
        // base case
        if (!root) return;

        sum += root->val;
        path.push_back(root->val);
        if (!root->left && !root->right && sum == target) res.push_back(path);

        traverse(root->left, sum, path);
        traverse(root->right, sum, path);

        sum -= root->val;
        path.pop_back();
    }
};
```

### [589. N 叉树的前序遍历](https://leetcode-cn.com/problems/n-ary-tree-preorder-traversal/)

#### 递归

```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    vector<Node*> children;

    Node() {}

    Node(int _val) {
        val = _val;
    }

    Node(int _val, vector<Node*> _children) {
        val = _val;
        children = _children;
    }
};
*/

class Solution {
public:
    vector<int> res;

    vector<int> preorder(Node* root) {
        // base case
        if (!root) return {};

        res.push_back(root->val);

        for (auto child : root->children) {
            preorder(child);
        }

        return res;
    }
};
```

#### 迭代

```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    vector<Node*> children;

    Node() {}

    Node(int _val) {
        val = _val;
    }

    Node(int _val, vector<Node*> _children) {
        val = _val;
        children = _children;
    }
};
*/

class Solution {
public:
    vector<int> preorder(Node* root) {
        // special case
        if (!root) return {};

        vector<int> res;
        stack<Node*> stk;
        
        stk.push(root);
        while (!stk.empty()) {
            auto node = stk.top();
            res.push_back(node->val);
            stk.pop();

            int length = node->children.size();
            for (int i = length - 1; i >= 0; --i) {
                stk.push(node->children[i]);
            }
        }

        return res;
    }
};
```

### [590. N 叉树的后序遍历](https://leetcode-cn.com/problems/n-ary-tree-postorder-traversal/)

#### 递归

```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    vector<Node*> children;

    Node() {}

    Node(int _val) {
        val = _val;
    }

    Node(int _val, vector<Node*> _children) {
        val = _val;
        children = _children;
    }
};
*/

class Solution {
public:
    vector<int> res;

    vector<int> postorder(Node* root) {
        // base case
        if (!root) return {};

        for (const auto child : root->children) {
            postorder(child);
        }

        res.push_back(root->val);
        
        return res;
    }
};
```

#### 迭代 tricky

```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    vector<Node*> children;

    Node() {}

    Node(int _val) {
        val = _val;
    }

    Node(int _val, vector<Node*> _children) {
        val = _val;
        children = _children;
    }
};
*/

class Solution {
public:
    vector<int> postorder(Node* root) {
        // special case
        if (!root) return {};

        vector<int> res;
        stack<Node*> stk;
        
        stk.push(root);
        while (!stk.empty()) {
            auto node = stk.top();
            res.push_back(node->val);
            stk.pop();

            for (const auto child : node->children) {
                stk.push(child);
            }
        }

        reverse(res.begin(), res.end());
        return res;
    }
};
```

### [106. 从中序与后序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

#### 递归，先不做，比较麻烦

```cpp
```

### [105. 从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

#### 递归，先不做，比较麻烦

```cpp
```

### [654. 最大二叉树](https://leetcode-cn.com/problems/maximum-binary-tree/)

#### 递归，定义新的 vector 效率较低，直接用数组下标效果更好

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
    TreeNode* constructMaximumBinaryTree(vector<int>& nums) {
        // base case
        if (nums.empty()) return nullptr;

        auto iter = max_element(nums.begin(), nums.end());
        TreeNode* root = new TreeNode(*iter);
        
        vector<int> leftNums;
        if (iter != nums.begin()) leftNums.assign(nums.begin(), iter);
        vector<int> rightNums;
        if (iter != nums.end()) rightNums.assign(iter + 1, nums.end());

        root->left = constructMaximumBinaryTree(leftNums);
        root->right = constructMaximumBinaryTree(rightNums);

        return root;
    }
};
```

### [617. 合并二叉树](https://leetcode-cn.com/problems/merge-two-binary-trees/)

#### 递归

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
    TreeNode* mergeTrees(TreeNode* root1, TreeNode* root2) {
        // base case
        if (!root1 && !root2) return nullptr;
        else if (!root1 || !root2) return root1 == nullptr ? root2 : root1;

        TreeNode* root = new TreeNode(root1->val + root2->val);

        root->left = mergeTrees(root1->left, root2->left);
        root->right = mergeTrees(root1->right, root2->right);

        return root;
    }
};
```

#### 迭代，没理解

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
    TreeNode* mergeTrees(TreeNode* root1, TreeNode* root2) {
        // special case
        if (!root1) return root2;
        else if (!root2) return root1;

        queue<TreeNode*> que;

        que.push(root1);
        que.push(root2);
        while (!que.empty()) {
            int length = que.size() / 2;
            while (length--) {
                auto node1 = que.front();
                que.pop();
                auto node2 = que.front();
                que.pop();

                node1->val += node2->val;

                if (node1->left && node2->left) {
                    que.push(node1->left);
                    que.push(node2->left);
                } else if (!node1->left && node2->left) {
                    node1->left = node2->left;
                }

                if (node1->right && node2->right) {
                    que.push(node1->right);
                    que.push(node2->right);
                } else if (!node1->right && node2->right) {
                    node1->right = node2->right;
                }
            }
        }

        return root1;
    }
};
```

### [700. 二叉搜索树中的搜索](https://leetcode-cn.com/problems/search-in-a-binary-search-tree/)

#### 自己写的递归，会超时

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
    TreeNode* searchBST(TreeNode* root, int val) {
        // base case
        if (!root) return nullptr;

        if (root->val != val) {
            if (searchBST(root->left, val)) return searchBST(root->left, val);
            else if (searchBST(root->right, val)) return searchBST(root->right, val);
            else return nullptr;
        } else {
            return root;
        }
    }
};
```

#### 递归，利用二叉搜索树的性质

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
    TreeNode* searchBST(TreeNode* root, int val) {
        // base case
        if (!root) return nullptr;

        if (root->val == val) return root;
        else if (root->val > val) return searchBST(root->left, val);
        else /*if (root->val < val)*/ return searchBST(root->right, val);
    }
};
```

#### 迭代，利用二叉搜索树的性质

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
    TreeNode* searchBST(TreeNode* root, int val) {
        // special case
        if (!root) return nullptr;

        while (root) {
            if (root->val == val) return root;
            else if (root->val > val) root = root->left;
            else if (root->val < val) root = root->right;
        }

        return nullptr;
    }
};
```

### [98. 验证二叉搜索树](https://leetcode-cn.com/problems/validate-binary-search-tree/)

### 递归，利用二叉搜索树的性质，中序遍历是生序

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
    vector<int> vec;

    bool isValidBST(TreeNode* root) {
        // special case
        // BST 可以为空
        if (!root) return true;

        traverse(root);

        for (int i = 1; i != vec.size(); ++i) {
            if (vec[i] <= vec[i - 1]) return false;
        }
        return true;
    }

    void traverse(TreeNode* root) {
        // base case
        if (!root) return;

        traverse(root->left);
        vec.push_back(root->val);
        traverse(root->right);
    }
};
```

#### 递归，我们通过使用辅助函数，增加函数参数列表，在参数中携带额外信息，将这种约束传递给子树的所有节点，这也是二叉树算法的一个小技巧

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
    bool isValidBST(TreeNode* root) {
        // special case
        // BST 可以为空
        if (!root) return true;

        return isValidBST(root, nullptr, nullptr);
    }

    bool isValidBST(TreeNode* root, TreeNode* minNode, TreeNode* maxNode) {
        // base case
        if (!root) return true;

        if (minNode && root->val <= minNode->val) return false;
        if (maxNode && root->val >= maxNode->val) return false;

        return isValidBST(root->left, minNode, root) && isValidBST(root->right, root, maxNode);
    }
};
```

#### 自创，简单易懂

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
    bool isValidBST(TreeNode* root) {
        // base case
        if (!root) return true;

        if (root->left) {
            // 左子树最右边的点
            TreeNode* cur = root->left;
            while (cur->right) cur = cur->right;
            if (cur->val >= root->val) {
                return false;
            }
        }
        if (root->right) {
            // 右子树最左边的点
            TreeNode* cur = root->right;
            while (cur->left) cur = cur->left;
            if (cur->val <= root->val) {
                return false;
            }
        }

        return isValidBST(root->left) && isValidBST(root->right);
    }
};
```

### [530. 二叉搜索树的最小绝对差](https://leetcode-cn.com/problems/minimum-absolute-difference-in-bst/)

#### 递归，数组

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
    int minValue = INT32_MAX;

    int getMinimumDifference(TreeNode* root) {
        // special case
        if (!root) return 0;

        vector<int> nums;
        traverse(root, nums);
        
        return minValue;
    }

    void traverse(TreeNode* root, vector<int>& nums) {
        // base case
        if (!root) return;

        traverse(root->left, nums);
        if (!nums.empty()) minValue = min(minValue, abs(root->val - nums.back()));
        nums.push_back(root->val);
        traverse(root->right, nums);
    }
};
```

#### 递归，记录 pre 指针，中序遍历会有序排列 BST

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
    int minValue = INT32_MAX;
    TreeNode* pre = nullptr;

    int getMinimumDifference(TreeNode* root) {
        // special case
        if (!root) return 0;

        traverse(root);
        
        return minValue;
    }

    void traverse(TreeNode* cur) {
        // base case
        if (!cur) return;

        traverse(cur->left);
        if (pre) minValue = min(minValue, abs(cur->val - pre->val));
        pre = cur;
        traverse(cur->right);
    }
};
```

### [501. 二叉搜索树中的众数](https://leetcode-cn.com/problems/find-mode-in-binary-search-tree/)

#### map

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
    unordered_map<int, int> umap;
    int maxCount = 0;

    vector<int> findMode(TreeNode* root) {
        // special case
        if (!root) return {};

        traverse(root);

        vector<int> res;
        for (const auto iter : umap) {
            if (iter.second == maxCount) res.push_back(iter.first);
        }

        return res;
    }

    void traverse(TreeNode* root) {
        // base case
        if (!root) return;

        traverse(root->left);
        ++umap[root->val];
        maxCount = max(maxCount, umap[root->val]);
        traverse(root->right);
    }
};
```

#### pre 节点，维护 curCount 和 maxCount

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
    vector<int> res;
    TreeNode* pre = nullptr;
    int maxCount = 0;
    int curCount = 0;

    vector<int> findMode(TreeNode* root) {
        // special case
        if (!root) return {};

        traverse(root);

        return res;
    }

    void traverse(TreeNode* cur) {
        // base case
        if (!cur) return;

        traverse(cur->left);
        if (!pre) {
            curCount = 1;
            maxCount = 1;
            res.push_back(cur->val);
        } else {
            if (cur->val == pre->val) {
                ++curCount;
                if (curCount == maxCount) {
                    res.push_back(cur->val);
                } else if (curCount > maxCount) {
                    maxCount = curCount;
                    res.clear();
                    res.push_back(cur->val);
                }
            } else {
                curCount = 1;
                if (curCount == maxCount) res.push_back(cur->val);
            }
        }
        pre = cur;
        traverse(cur->right);
    }
};
```

### [236. 二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

#### 多做，对回溯、后序遍历加深印象

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        // base case
        if (!root || root == p || root == q) return root;

        auto leftNode = lowestCommonAncestor(root->left, p, q);
        auto rightNode = lowestCommonAncestor(root->right, p, q);

        if (!leftNode && !rightNode) return nullptr;
        else if (!leftNode || !rightNode) return leftNode != nullptr ? leftNode : rightNode;
        else return root;
    }
};
```

### [235. Lowest Common Ancestor of a Binary Search Tree](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-search-tree/)

#### 递归

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */

class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        // base case
        if (!root || root == p || root == q) return root;
        
        if (root->val < p->val && root->val < q->val) return lowestCommonAncestor(root->right, p, q);
        else if (root->val > p->val && root->val > q->val) return lowestCommonAncestor(root->left, p, q);
        else return root;
    }
};
```

### [701. Insert into a Binary Search Tree](https://leetcode.cn/problems/insert-into-a-binary-search-tree/)

#### 递归

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
    TreeNode* insertIntoBST(TreeNode* root, int val) {
        // base case
        if (!root) {
            TreeNode* newNode = new TreeNode(val);
            return newNode;
        } else if (root->val > val) {
            root->left = insertIntoBST(root->left, val);
            return root;
        } else {
            root->right = insertIntoBST(root->right, val);
            return root;
        }
    }
};
```

### [450. Delete Node in a BST](https://leetcode.cn/problems/delete-node-in-a-bst/)

#### 递归

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
    TreeNode* deleteNode(TreeNode* root, int key) {
        // base case
        // 第一种情况：没找到删除的节点，遍历到空节点直接返回了
        if (!root) return nullptr;
        if (root->val == key) {
            if (!root->left && !root->right) {
                // 第二种情况：左右孩子都为空（叶子节点），直接删除节点， 返回NULL为根节点
                delete root;
                return nullptr;
            } else if (root->left && !root->right) {
                // 第三种情况：其右孩子为空，左孩子不为空，删除节点，左孩子补位，返回左孩子为根节点
                auto replaceNode = root->left;
                delete root;
                return replaceNode;
            } else if (!root->left && root->right) {
                // 第四种情况：其左孩子为空，右孩子不为空，删除节点，右孩子补位 ，返回右孩子为根节点
                auto replaceNode = root->right;
                delete root;
                return replaceNode;
            } else /*if (root->left && root->right)*/ {
                // 第五种情况：左右孩子节点都不为空，则将删除节点的左子树放到删除节点的右子树的最左面节点的左孩子的位置，并返回删除节点右孩子为新的根节点
                auto replaceNode = root->right;
                auto cur = root->right;
                while (cur->left) cur = cur->left;
                cur->left = root->left;
                delete root;
                return replaceNode;
            }
        } else if (root->val > key) {
            root->left = deleteNode(root->left, key);
            return root;
        } else /*if (root->val < key)*/ {
            root->right = deleteNode(root->right, key);
            return root;
        }
    }
};
```

### [669. Trim a Binary Search Tree](https://leetcode.cn/problems/trim-a-binary-search-tree/)

#### 错误的答案，理解题目有问题

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
    int _low = 0;
    int _high = 0;
    vector<int> _vec;
    unordered_map<int, TreeNode*> _umap;

    TreeNode* trimBST(TreeNode* root, int low, int high) {
        // special case
        if (!root) return nullptr;
        _low = low;
        _high = high;
        traverse(root);
        sort(_vec.begin(), _vec.end());
        auto dummy = new TreeNode();
        auto cur = dummy;
        for (const auto& iter : _vec) {
            cur->right = _umap[iter];
            cur = cur->right;
        }
        auto newRoot = dummy->right;
        delete dummy;
        return newRoot;
    }

    void traverse(TreeNode* root) {
        // base case
        if (!root) return;

        traverse(root->left);
        if (root->val >= _low && root->val <= _high) {
            _vec.push_back(root->val);
            auto newNode = new TreeNode(root->val);
            _umap[root->val] = newNode;
        }
        traverse(root->right);
    }
};
```

#### 递归

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
    TreeNode* trimBST(TreeNode* root, int low, int high) {
        // base case
        if (!root) return nullptr;
        if (root->val < low) {
            return trimBST(root->right, low, high);
        } else if (root->val > high) {
            return trimBST(root->left, low, high);
        } else {
            root->left = trimBST(root->left, low, high);
            root->right = trimBST(root->right, low, high);
            return root;
        }
    }
};
```

### [108. Convert Sorted Array to Binary Search Tree](https://leetcode.cn/problems/convert-sorted-array-to-binary-search-tree/)

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
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        // base case
        if (nums.empty()) return nullptr;

        auto pos = nums.size() / 2;
        auto root = new TreeNode(nums[pos]);

        auto leftNums = vector<int>(nums.begin(), nums.begin() + pos);
        auto rightNums = vector<int>(nums.begin() + pos + 1, nums.end());

        root->left = sortedArrayToBST(leftNums);
        root->right = sortedArrayToBST(rightNums);

        return root;
    }
};
```

### [538. Convert BST to Greater Tree](https://leetcode.cn/problems/convert-bst-to-greater-tree/)

#### 反中序遍历

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
    int pre = 0;

    TreeNode* convertBST(TreeNode* root) {
        traverse(root);
        return root;
    }

    void traverse(TreeNode* root) {
        // base case
        if (!root) return;

        traverse(root->right);
        root->val += pre;
        pre = root->val;
        traverse(root->left);
    }
};
```