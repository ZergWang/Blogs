# 题目简介
链接：https://leetcode.com/problems/top-k-frequent-elements/submissions/
<br/><br/>
给一个有n个元素的int型数组a，问该数组中出现最频繁的k个元素是什么，输出这k个元素。

数据范围：
$ 1 \leq n \leq 10^5 $
各个元素的大小范围没给

# 算法一
本来想用计数排序，用数组arr来统计每个元素的出现次数，arr[i]表示元素i出现的次数。但元素的大小范围没给，且元素可以为负数，因此arr要开多大心里没数，就此作罢。

本算法时间复杂度为$O(N \times log_2N)$。首先将原数组a排序，令相同元素与相邻。然后扫一遍a，统计出每个元素a[i]的出现次数sum。然后用数组nums存储。nums[i]存储的是出现了i次的元素。由于同样出现i次的元素可能不止一个，因此nums采用链表结构，每多一个要存储的元素，就开个新节点node。
```cpp
#define N 100005
struct node {
    int n;
    struct node* next;
};


int Cmp(const void * a, const void * b) {
    return *(int*)a - *(int*)b;
}


void Insert(struct node* nums, int idx, int n) {
    nums[idx].n++;     
    struct node* now = &(nums[idx]);
    struct node* newNode = (struct node*)malloc(sizeof(struct node));
    newNode->n = n;
    newNode->next = NULL;

    while (now->next != NULL) 
        now = now->next;
    
    now->next = newNode;
}


int* topKFrequent(int* a, int n, int k, int* returnSize){
    *returnSize = k;
    int* ans = (int*)malloc(N*sizeof(int));
    int ansNum = 0; 
    qsort(a, n, sizeof(a[0]), Cmp);
    struct node nums[N];
    memset(nums, 0, sizeof(nums));
    int sum = 1;
    
    for (int i=1; i<n; i++)    
        if (a[i] != a[i-1]) {
            Insert(nums, sum, a[i-1]);
            sum = 1;
        }
        else 
            sum++;
    Insert(nums, sum, a[n-1]);
   //nums[i] stores the numbers which occur i times
    
    for (int i=100000; i>=0; i--) 
        if (nums[i].n && ansNum<k) {
            struct node* now = nums[i].next;
            while (now!=NULL && ansNum<k) {
                ans[ansNum++] = now->n;
                now = now->next;
            }
        }
    return ans;
}
```
<br/><br/>

# 算法二
堆
```cpp


```