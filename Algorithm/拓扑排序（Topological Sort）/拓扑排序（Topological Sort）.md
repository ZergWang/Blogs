# 问题引入
现实生活中我们遇到的要处理的事情可能要讲究先后顺序，比如说要先穿好衬衫才能穿外套，再比如说一个计算机系的学生想修完所有课程：
![](拓扑排序（Topological%20Sort）_1.png)
面对这样一张图，如何求出做事的顺序？这时需要拓扑排序：
将事件视为点，先后关系视为边，则可以把“先做事件2，再做事件1”抽象成：2→1，同理可将上面的课表变成一副有向图：
![](拓扑排序（Topological%20Sort）_2.png)
一个可以进行拓扑排序的有向图是不能存在环的：
![](拓扑排序（Topological%20Sort）_3.png)
如上图表示了一个环，在实际问题中，就表示完成事件2前要完成事件1，完成事件3前要完成事件2，但完成事件1前要完成事件3，这样无限循环，3件事都做不了。
<br/><br/>

# 拓扑排序算法
拓扑排序就是通过以上这种有向图来获知做这一系列事情的先后顺序。排序后得到的序列称为拓扑序列。拓扑排序的执行流程为：

1. 在图中找到一个入度为0的点K（入度为零表示没有别的点指向点K，找不到入度为0的点说明有向图中存在环）

2. 输出K并将与K相关的所有边删除（将K指向的点的入度全都减1）

3. 重复步骤1、2，直到找不到入度为0的点。

为了防止每次找入度为0的点时都要遍历图中所有点，可以在一开始就将所有入度为0的点记录下来，记为 $Q = \{K_1, K_2 ... K_n\}$，每次从 $Q$ 中找点 $N_i (1\le i \le n)$ 删边后，只会改变 $N_i$ 指向的点的入度，因此每次删边后将 $N_i$ 指向的点中入度为0的加入到 $Q$ 即可。在代码上，$Q$ 可以使用队列来实现。

<br/><br/>

# 例题
## LeetCode 207 & 210
LeetCode 207链接：[207. Course Schedule](https://leetcode.com/problems/course-schedule/) 

LeetCode 210链接：[210. Course Schedule II](https://leetcode.com/problems/course-schedule-ii/) 

题目207给出一堆课程的先后关系，问是否能学完所有的课程。题目210与207一样，但要求输出课程的完成顺序。以下给出210的代码。
```cpp
int* findOrder(int n, int** prerequisites, int prerequisitesSize, int* prerequisitesColSize, int* returnSize){
    *returnSize = 0;
    int* ans = (int*)malloc(sizeof(int)*n);
    int indegree[n];
    bool ifRequire[n][n];
    memset(indegree, 0, sizeof(indegree));
    memset(ifRequire, 0, sizeof(ifRequire));

    for (int i=0; i<prerequisitesSize; i++) {
        int pre = prerequisites[i][1];
        int now = prerequisites[i][0];
        indegree[now]++;
        ifRequire[pre][now] = 1;
    }
    
    int head = 0, tail = -1;
    for (int i=0; i<n; i++) 
        if (!indegree[i]) 
            ans[++tail] = i;
    
    while (head <= tail) {
        int now = ans[head++];
        for (int i=0; i<n; i++) 
            if (ifRequire[now][i]) {
                indegree[i]--;
                if (!indegree[i]) 
                    ans[++tail] = i;
            }
    }

    if (tail+1 == n)
        *returnSize = n;
    return ans;
}
```
<br/><br/>


## LeetCode 2392
链接：https://leetcode.com/problems/build-a-matrix-with-conditions/

将1到k的k个数字放在一个k*k的方格中（不放这k个数字的地方用0填充）。在放置时，还要满足某些数字要在另一些数字的左边或上边。给出这些限制条件，生成一个满足上述条件的方格。

```cpp
int GetSort(int **a, int n, int k, int *queue) {
    bool f[k+1][k+1];
    memset(f, 0, sizeof(f));
    
    int inDegree[k+1];
    int head = 0, tail = 0, qSize = 0;
    memset(queue, 0, k*sizeof(int));
    memset(inDegree, 0, sizeof(inDegree));
    
    for (int i=0; i<n; i++) {
        if (f[a[i][0]][a[i][1]])  //同一个限制条件出现多次，会影响元素的入度
            continue;
        f[a[i][0]][a[i][1]] = 1;
        inDegree[a[i][1]]++;
    }        
    
    for (int i=1; i<=k; i++) 
        if (!inDegree[i])
            queue[qSize++] = i;
    
    if (!qSize)
        return 1;   //检测环
    else 
        tail = qSize-1;
    
    while (head<=tail) {
        int now = queue[head];
        for (int i=1; i<=k; i++)
            if (f[now][i]) {
                inDegree[i]--;
                if (!inDegree[i]) {
                    queue[++tail] = i;
                    qSize++;
                }                    
            }
        head++;
    }
    
    if (qSize!=k)   //检测环
        return 1;
    
    return 0;
}


int** buildMatrix(int k, int** r, int rSize, int* rowConditionsColSize, int** c, int cSize, int* colConditionsColSize, int* returnSize, int** returnColumnSizes){
    
    int rSort[k], cSort[k]; 
    if (GetSort(r, rSize, k, rSort)) {
        *returnSize = 0;
        return NULL;    //按行号由小到大放置元素，rSort[i]表示元素rSort[i]应放置在第i行
    }        
    if (GetSort(c, cSize, k, cSort)){
        *returnSize = 0;
        return NULL;   //按列号由小到大放置元素，cSort[i]表示元素cSort[i]应放置在第i列
    }    
    
    int ** ans = (int**)malloc(k*sizeof(int*));
    *returnSize = k;
    *returnColumnSizes = (int*)malloc(k*sizeof(int));
    for (int i=0; i<k; i++) {
        int* newAns = (int*)calloc(k, sizeof(int));
        ans[i] = newAns;
        (*returnColumnSizes)[i] = k;
    }
    
    int pos[k+1][2];    
    for (int i=0; i<k; i++) {
        pos[rSort[i]][0] = i;
        pos[cSort[i]][1] = i;
    }
    
    for (int i=1; i<=k; i++)
        ans[pos[i][0]][pos[i][1]]= i;
    
    return ans;
}
```