# 题目描述
给出一个长度为numsSize的数组nums，并给出一个数target，数组中有两个数的和等于target，找出这两个数，输出它们在数组中的位置。（注意：这两个数，值可能相等但位置一定不同）

数据范围：
numsSize： $[ 2 , 10^4 ]$
数组中的数及target： $[-10^9,  10^9]$

# 避坑指南
题目链接：https://leetcode.com/problems/two-sum/

LeetCode给的接口感觉不太人性化，新手比较容易产生各种奇怪的错误。
```cpp
int* twoSum(int* nums, int numsSize, int target, int* returnSize)
```
如果出现无输出或编译报错的情况，请检查：
- 返回值是一个size为2的int数组，该数组在新建时应使用malloc函数，否则会出现“function returns address of local variable”的提示。
- 请设置```*returnSize = 2```，否则会出现无输出的情况。
- 无论函数内有没有return，在函数最后一定要写return，否则报错“control reaches end of non-void function”。
<br/><br/>

# 算法一
二重循环的暴力方法太简单就不写了……

本方法时间复杂度$O(N + N \times log_2N)$

首先对nums按从小到大排序，然后用两个指针 i 和 j。其中 i 指向nums的头，j 指向nums的尾。如果 i + j > target 的话 j 左移，i + j < target 的话 i 右移，直到 i + j = target。

由于输出的是对应数字在原数组中的下标，因此排序前要记录每个数字在原数组的下标信息。
```cpp
struct node {
    int n, i; //n存储数字，i存储下标
};

int Cmp(const void * a, const void * b) {
    return (*(struct node *)a).n - (*(struct node *)b).n;
}

int* twoSum(int* nums, int numsSize, int target, int* returnSize){
    *returnSize = 2;
    int* result = (int*)malloc(sizeof(int)*2);
    struct node b[numsSize];
    for (int i=0; i<numsSize; i++) {
        b[i].n = nums[i];
        b[i].i = i;
    }
    qsort(b, numsSize, sizeof(b[0]), Cmp);
    int i = 0, j = numsSize-1;
    while (i < j) {
        if (b[i].n + b[j].n < target) 
            i++;
        else if (b[i].n + b[j].n > target)
            j--;
        else {
            result[0] = b[i].i;
            result[1] = b[j].i;
            return result;
        }
    }
    return result;
}

```

相比二重循环的暴力法（204ms），该算法耗时仅20ms，效果拔群！
<br/><br/>

# 算法二
使用哈希表存储数字。哈希算法采用最简单的取模法。由于数据范围过大，因此有很大的概率产生哈希冲突。因此，哈希表中的每个元素均为链表，用于存储nums中哈希值相同的数。

```cpp
#define HASHMOD 389

typedef struct HashNode Node;
struct HashNode {
    int key;
    int val;
    Node *next;
} table[HASHMOD];

void HashInsert(int key, int pos) {
    int hashFunc = abs(key) % HASHMOD; //取模法算出哈希值
    Node *newNode = (Node*)malloc(sizeof(Node));
    newNode->key = key;
    newNode->val = pos;
    newNode->next = table[hashFunc].next;//链表中插入新元素
    table[hashFunc].next = newNode;
}

int HashFind(int key) {
    int hashFunc = abs(key) % HASHMOD;
    Node *current = table[hashFunc].next;
    while (current != NULL) {
        if (current->key == key) 
            return current->val;
        current = current->next;
    }  
    return -1;
}

int* twoSum(int* nums, int n, int target, int* returnSize){
    *returnSize = 2;
    int* result = (int*)malloc(sizeof(int)*2);
    
    for(int i=0; i<HASHMOD; i++)
        table[i].next = NULL; 

    for (int i=0; i<n; i++) {
        int index = HashFind(target-nums[i]);
        if (index!=-1) {
            result[0] = i;
            result[1] = index;
            return result;
        }
        HashInsert(nums[i], i);
    }
    return NULL;
}
```
相比算法一，时间从20ms提升到了4ms。效果再度拔群！

然而，使用哈希表的话，时间复杂度比较受哈希算法的影响。同样是取模法，取911的运行时间是9ms，不同的mod值时间能差一倍……
<br/><br/>

# 参考资料
[C Hash 4ms](https://leetcode.com/problems/two-sum/discuss/264882/C-Hash-4ms)


