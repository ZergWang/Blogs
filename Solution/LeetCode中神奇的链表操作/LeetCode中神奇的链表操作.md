# 删除节点
相关题目：[LeetCode 237](https://leetcode.com/problems/delete-node-in-a-linked-list/)

编写一个删除单向链表中某个节点的函数。该题的难点在于，函数传入的参数即为被删除节点本身。因此，无法取得被删除节点的前一个节点，按照常规思路没法做。

实际上，题目在数据规模上给出了提示：被删除节点不会是链表中的最后一个节点。

假设被删除节点为node，既然不能取得node的前面的节点，题目又保证了node->next不为空。自然地，我们要拿node->next作文章：提前把node->next的值转移到node上，然后将node->next删掉！
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */
void deleteNode(struct ListNode* node) {    
    node->val = node->next->val;
    node->next = node->next->next;   
}
```
<br/><br/>

# 快慢指针
相关题目：[LeetCode 142](https://leetcode.com/problems/linked-list-cycle-ii/)

给出一个可能存在环的单向链表，找出该链表中环的起始点。一开始的思路是开数组存每个节点的地址，然后遍历链表，去找数组中第一个被重复访问的地址。时间空间复杂度均为$O(N)$。

以下方法将空间复杂度优化至$O(1)$：
- 令两个指针（slow和fast）同时遍历链表，其中，指针slow每次移动一个节点，指针fast每次移动2个节点。
- 假设n次迭代后slow和fast重合，此时fast比slow多走了n步，但由于两者重合，意味着fast比slow多走了若干次环。证明n为环中节点数量的整数倍。
- 新开一个指针ans，令ans从链表头部开始，与slow一起迭代，每次移动1个节点。
- 无论迭代多少次，slow总是比ans多走n步。也就是说，若ans刚进入环，则必定与slow重合。此时返回ans即为答案。
```cpp
struct ListNode *detectCycle(struct ListNode *head) {
    struct ListNode *slow = head, *fast = head;
    while(fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
        if(slow == fast) {
            struct ListNode *ans = head;
            while(slow != ans) {
                slow = slow->next;
                ans = ans->next;
            }
            return ans;
        }
    }
    return NULL;
}
```
此题的思路也可以用在[LeetCode 876](https://leetcode.com/problems/middle-of-the-linked-list/)，找出链表最中间的节点。令指针slow每次移动一个节点，指针fast每次移动2个节点。当fast抵达链表末尾时，slow正好在链表中间。
<br/><br/>

# 寻找倒数的节点
相关题目：[LeetCode 1721](https://leetcode.com/problems/swapping-nodes-in-a-linked-list/)

给出一个单向链表及整数k，交换链表中正数第k个和倒数第k个节点的值。

常规思路自然是先遍历一遍链表，确定链表中节点个数n，然后再遍历一遍，找到对应的节点交换。实际上，只需一次遍历即可完成。

由于事先知道k，令指针now从head开始直接迭代k次定位到正数第k个节点。然后另一个指针rig也从head开始，与now一起迭代，直到now指向NULL。由于now从第k个节点到NULL一共循环n-k次，与now一同迭代的rig从head开始也迭代n-k次，正好指向了倒数第k个节点。

```cpp
struct ListNode* swapNodes(struct ListNode* head, int k){
    struct ListNode *lef, *rig = head, *now = head;    
    if (head == NULL || head->next == NULL)
        return head;
    
    int i = k-1;
    while (i>0) {
        now = now->next;
        i--;
    }    
    lef = now;
  
    while (now->next != NULL){
        now = now->next;
        rig = rig->next;
    }
    i = lef->val;
    lef->val = rig->val;
    rig->val = i;
    return head;
}
```

对于此类要处理链表中倒数某个节点的题目，以上思路同样适用。例如[LeetCode 19](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)，删除链表中倒数第k个节点这种题目。

<br/><br/>

# 链表翻转

不使用递归，来翻转一个链表：[LeetCode 206](https://leetcode.com/problems/reverse-linked-list/description/)

```cpp
struct ListNode* reverseList(struct ListNode* head){
    if (!head)
        return NULL;
    
    struct ListNode* prev = head;
    struct ListNode* now = head->next;
    prev->next = NULL;

    struct ListNode* next = NULL;

    while (now) {
        next = now->next;
        now->next = prev;
        prev = now;
        now = next;
    }

    return prev;
}
```
<br/><br/>

# 哨兵节点(dummy node)
哨兵节点一般在链表开头，并不存储数据，而是用于简化边界处理。使用哨兵节点后，首个存储数据的节点实际上是链表的第二个节点，对它的处理和对其他实际存储数据的节点都是一样的，从而简化代码逻辑，提高可读性。

在双向链表中，哨兵节点的后继节点为链表第二个节点（也是首个实际存储数据的节点），哨兵节点的前驱结点可以设置为链表末尾节点，这样一来从哨兵节点开始可以沿两个方向遍历，所有实际存储数据的节点（包括第一个和最后一个）都可按同样的代码逻辑处理。

当链表为空时，链表为NULL或者链表仅有一个哨兵节点（此时哨兵节点的前驱和后继节点都指向自己）。下面列出利用哨兵节点进行节点插入和删除的操作：
```cpp
typedef struct LinkNode {
    T data;
    LinkNode *next, *prev;
} LinkNode;

//在某链表的开头插入节点，返回新节点的地址 
LinkNode* InsertNode(T data, LinkNode* dummy) {    
    LinkNode* nextNode = dummy->next;
    LinkNode* newNode = (LinkNode*)malloc(sizeof(LinkNode));
    newNode->data = data;
    newNode->prev = dummy;
    newNode->next = nextNode;
    nextNode->prev = newNode;
    dummy->next = newNode;
    return newNode;
}

//删除指定位置的节点
void DeleteNode(LinkNode* target) {      
    target->prev->next = target->next;
    target->next->prev = target->prev;
    free(target);
}
```