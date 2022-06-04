[TOC]

### [203. 移除链表元素](https://leetcode-cn.com/problems/remove-linked-list-elements/)

#### 不设置 dummy 节点

```cpp
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
        // special case
        if (!head) return nullptr;

        // 处理头节点与 val 相等的情况
        while (head) {
            if (head->val == val) head = head->next;
            else break;
        }

        ListNode* newHead = head;
        if (!newHead) return nullptr;

        while (head->next) {
            if (head->next->val == val) head->next = head->next->next;
            else head = head->next;
        }

        return newHead;
    }
};
```

#### 设置 dummy 节点

```cpp
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
        // special case
        if (!head) return nullptr;

        ListNode* dummy = new ListNode();
        dummy->next = head;

        ListNode* cur = dummy;

        while (cur->next) {
            if (cur->next->val == val) {
                ListNode* tmp = cur->next;
                cur->next = cur->next->next;
                delete tmp;
            } else {
                cur = cur->next;
            }
        }

        return dummy->next;
    }
};
```

#### 递归

```cpp
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
        // special case
        if (!head) return nullptr;

        head->next = removeElements(head->next, val);

        return head->val == val ? head->next : head;
    }
};
```

### [707. 设计链表](https://leetcode-cn.com/problems/design-linked-list/)

#### 不设置 dummy 节点

```cpp
class MyLinkedList {
public:
    struct ListNode {
        int val;
        ListNode* next;
        ListNode(int _val) : val(_val), next(nullptr) {}
    };

    ListNode* head;
    int length;

    MyLinkedList() {
        head = nullptr;
        length = 0;
    }
    
    int get(int index) {
        // special case
        if (index < 0 || index > length - 1) return -1;

        ListNode* cur = head;
        while (index--) {
            cur = cur->next;
        }
        return cur->val;
    }
    
    void addAtHead(int val) {
        ListNode* newHead = new ListNode(val);
        if (!head) {
            head = newHead;
        } else {
            newHead->next = head;
            head = newHead;
        }
        ++length;
    }
    
    void addAtTail(int val) {
        ListNode* newTail = new ListNode(val);
        if (!head) {
            head = newTail;
        } else {
            ListNode* curTail = head;
            while (curTail->next) curTail = curTail->next;
            curTail->next = newTail;
        }
        ++length;
    }
    
    void addAtIndex(int index, int val) {
        // special case
        if (index > length) return;

        if (index <= 0) {
            addAtHead(val);
        } else if (index == length) {
            addAtTail(val);
        } else {
            ListNode* newNode = new ListNode(val);

            ListNode* dummy = new ListNode(0);
            dummy->next = head;
            ListNode* pre = dummy;
            ListNode* cur = head;
            while (index--) {
                pre = pre->next;
                cur = cur->next;
            }
            pre->next = newNode;
            newNode->next = cur;
            ++length;

            delete dummy;
        }
    }
    
    void deleteAtIndex(int index) {
        if (index >= 0 && index <= length - 1) {
            if (index == 0) {
                ListNode* tmp = head;
                head = head->next;
                delete tmp;
            } else {
                ListNode* dummy = new ListNode(0);
                dummy->next = head;
                ListNode* pre = dummy;

                while (index--) {
                    pre = pre->next;
                }

                ListNode* deleteNode = pre->next;
                pre->next = pre->next->next;

                delete deleteNode;
                delete dummy;
            }
            --length;
        }
    }
};

/**
 * Your MyLinkedList object will be instantiated and called as such:
 * MyLinkedList* obj = new MyLinkedList();
 * int param_1 = obj->get(index);
 * obj->addAtHead(val);
 * obj->addAtTail(val);
 * obj->addAtIndex(index,val);
 * obj->deleteAtIndex(index);
 */
```

#### 设置 dummy 节点
```cpp
class MyLinkedList {
public:
    struct LinkedNode {
        int val;
        LinkedNode* next;
        LinkedNode() : val(0), next(nullptr) {}
        LinkedNode(int _val) : val(_val), next(nullptr) {}
    };

    LinkedNode* _dummy;
    int _size;

    MyLinkedList() {
        _dummy = new LinkedNode();
        _size = 0;
    }
    
    int get(int index) {
        // special case
        if (index < 0 || index > _size - 1) return -1;

        LinkedNode* cur = _dummy->next;
        while (index--) {
            cur = cur->next;
        }

        return cur->val;
    }
    
    void addAtHead(int val) {
        LinkedNode* newNode = new LinkedNode(val);
        newNode->next = _dummy->next;
        _dummy->next = newNode;

        ++_size;
    }
    
    void addAtTail(int val) {
        LinkedNode* newNode = new LinkedNode(val);
        LinkedNode* cur = _dummy;
        while (cur->next) {
            cur = cur->next;
        }
        cur->next = newNode;

        ++_size;
    }
    
    void addAtIndex(int index, int val) {
        // special case
        if (index < 0 || index > _size) return; // 这边不是 index > _size -1，注意审题
        if (index == 0) {
            addAtHead(val);
            ++_size;
            return;
        }

        LinkedNode* newNode = new LinkedNode(val);
        LinkedNode* cur = _dummy;

        while (index--) {
            cur = cur->next;
        }

        newNode->next = cur->next;
        cur->next = newNode;

        ++_size;
    }
    
    void deleteAtIndex(int index) {
        // special case
        if (index < 0 || index > _size - 1) return;

        LinkedNode* cur = _dummy;
        while (index--) {
            cur = cur->next;
        }

        if (cur->next) {
            auto tmp = cur->next;
            cur->next = cur->next->next;
            delete tmp;
        }
        --_size;
    }
};

/**
 * Your MyLinkedList object will be instantiated and called as such:
 * MyLinkedList* obj = new MyLinkedList();
 * int param_1 = obj->get(index);
 * obj->addAtHead(val);
 * obj->addAtTail(val);
 * obj->addAtIndex(index,val);
 * obj->deleteAtIndex(index);
 */
```

### [206. 反转链表](https://leetcode-cn.com/problems/reverse-linked-list/)

#### 递归（base case 很重要，否则要加 else return head; 的判断语句）

```cpp
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
    ListNode* reverseList(ListNode* head) {
        // base case
        if (!head) return nullptr;

        ListNode* last = reverseList(head->next);
        if (head->next) {
            head->next->next = head;
            head->next = nullptr;
        } else {
            return head; // 递归中，ahead 后无节点
        }

        return last;
    }
};
```

#### 迭代

```cpp
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
    ListNode* reverseList(ListNode* head) {
        // special case
        if (!head) return nullptr;

        ListNode* pre = nullptr;
        ListNode* cur = head;
        while (cur) {
            ListNode* nxt = cur->next;
            cur->next = pre;
            pre = cur;
            cur = nxt;
        }

        return pre;
    }
};
```

### [24. 两两交换链表中的节点](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

#### 递归1

```cpp
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
    ListNode* swapPairs(ListNode* head) {
        // base case
        if (!head) {
            return nullptr;
        } else if (!head->next) {
            return head;
        } else if (!head->next->next) {
            ListNode* newHead = head->next;
            head->next->next = head;
            head->next = nullptr;
            return newHead;
        }

        ListNode* newHead = head->next;
        head->next = swapPairs(head->next->next);
        newHead->next = head;
        return newHead;
    }
};
```

#### 递归2

```cpp
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
    ListNode* swapPairs(ListNode* head) {
        // base case
        if (!head || !head->next) return head;

        ListNode* newHead = head->next;
        head->next = swapPairs(head->next->next);
        newHead->next = head;

        return newHead;
    }
};
```

#### 迭代

```cpp
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
    ListNode* swapPairs(ListNode* head) {
        // special case
        if (!head || !head->next) return head;

        ListNode* dummy = new ListNode();
        dummy->next = head;
        ListNode* pre = dummy;
        while (pre->next && pre->next->next) {
            ListNode* node1 = pre->next;
            ListNode* node2 = pre->next->next;
            // swap
            pre->next = node2;
            node1->next = node2->next;
            node2->next = node1;

            pre = node1;
        }

        ListNode* res = dummy->next;
        delete dummy;
        return res;
    }
};
```

### [19. 删除链表的倒数第 N 个结点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

#### dummy

```cpp
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
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        // special case
        if (!head) return nullptr;

        ListNode* dummy = new ListNode();
        dummy->next = head;

        ListNode* slowPtr = dummy;
        ListNode* fastPtr = dummy;

        while (n--) {
            fastPtr = fastPtr->next;
        }

        while (fastPtr->next) {
            slowPtr = slowPtr->next;
            fastPtr = fastPtr->next;
        }

        ListNode*  deleteNode = slowPtr->next;
        slowPtr->next = slowPtr->next->next;
        delete deleteNode;

        ListNode* res = dummy->next;
        delete dummy;
        return res;
    }
};
```

### [面试题 02.07. 链表相交](https://leetcode-cn.com/problems/intersection-of-two-linked-lists-lcci/)

#### 双指针

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        // special case
        if (!headA || !headB) return nullptr;

        ListNode* nodeA = headA;
        ListNode* nodeB = headB;
        while (nodeA != nodeB) {
            nodeA = nodeA == nullptr ? headB : nodeA->next;
            nodeB = nodeB == nullptr ? headA : nodeB->next;
        }
        return nodeA;
    }
};
```

### [142. 环形链表 II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

#### 双指针

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        // special case
        if (!head) return nullptr;

        ListNode* fastPtr = head;
        ListNode* slowPtr = head;

        while (fastPtr && fastPtr->next) {
            // 不能写在前面，因为一开始 fastPtr == slowPtr
            // if (fastPtr == slowPtr) break;
            fastPtr = fastPtr->next->next;
            slowPtr = slowPtr->next;
            if (fastPtr == slowPtr) break;
        }

        if (!fastPtr || !fastPtr->next) return nullptr;

        // fastPtr 在相遇点，令 slowPtr 回到出发点，以同样的速度出发
        // 再次相遇点既是起始点
        slowPtr = head;
        while (slowPtr != fastPtr) {
            slowPtr = slowPtr->next;
            fastPtr = fastPtr->next;
        }

        return slowPtr;
    }
};
```

