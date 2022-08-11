# 题目简介
题目链接：https://leetcode.com/problems/reducing-dishes/

有n道菜，每道菜有一个值a[i] ( 0 <= i < n) ，现在可以按任意顺序煮这n道菜，每道菜最多煮一次，煮每一道菜需要花费1个单位时间，但会得到该道菜的满意度，该满意度等于a[i]与当前时间的乘积。也就是说，第t个单位时间煮第i道菜，则从该菜中获得的满意度为a[i]*t，总满意度为各道菜满意度之和。求能得到的最大满意度
<br/>

# 解法一：贪心
### 思路
越晚做的菜时间权重越大，为了令总满意度最高，所以要排序，满意值大的菜放后面。

然后从后往前检验每道菜，若煮第i道菜能令总满意度增加，则煮，否则就直接结束。这是因为若煮第i道菜令总满意度降低了，说明当前菜的满意度为负数（a[i]<0），且其负满意度甚至能超过之前一系列菜大一级时间权重带来的正满意度（设 f[i] = a[i] + a[i+1] + ...+ a[n-1]，则有f[i] < 0）。而再下一道（i-1）菜的满意度要更低。如果真的要煮第i-1道菜，满意度增加值为 f[i-1] = a[i-1] + f[i]，显然f[i-1]为负数，且f[i-1]<f[i]。 由于a是递增数列，则从i至0，f[i]必为递减数列且均小于0。因此，当煮第i到菜令总满意度降低时，说明在第i+1道菜总满意度达到最大值。

### 代码
```cpp
// Time: O(n*log(n))
// Memory: O(n)
int Cmp(const void * a, const void * b) {
    return *(int*)a - *(int*)b;
}

int maxSatisfaction(int* a, int n){
    int f[n+1];
    memset(f, 0, sizeof(f));
    qsort(a, n, sizeof(int), Cmp);
    int ans = 0;
    for (int i=n-1; i>=0; i--) {
        f[i] = f[i+1] + a[i];
        if (ans+f[i] > ans)
            ans += f[i];
        else 
            return ans;
    }
    return ans;
}
```
<br/>

# 解法二：动态规划
### 思路
同样先排序，然后考虑如何划分状态：
1. 首先考虑到f[i]表示从前i道菜中能得到的最大值，但由于时间因素的影响，可能存在f[i-1]取最大值时所消耗时间大于f[i]这种情况，这导致f[i]无法直接利用到前一阶段的数据。

2. 因此，构造二维数组f[i][t]，表示从前i道菜中，只做其中的t道（消耗时间为t）能得到的最大值，将时间这一因素单独拎出来，让对应时间下前一阶段的数据可直接使用。

3. 对f[i][t]而言，当做第i道菜可得到最大值时，可从f[j][t-1]（j<i）中找到最大值，加上做第i道菜得到的满意值即可；若不做第i道菜，直接找f[j][t]（j<i）中的最大值即可。
   
4. 状态转移方程:
```cpp
    f[i][t] = max(f[i][t], f[j][t], f[j][t-1]+a[i]*t);
```

5. 边界条件：注意f[i][t]中，t要始终小于i+1。f[i][0]恒为0。


### 代码
```cpp
// Time: O(n^3)
// Memory: O(n^2)
// f[i][t]: Only cook t dishes(consuming t units of time), 
// the maximum value that can be obtained from the first i dishes (t<=i+1).

int max(int i, int j) {
    return i>j ? i : j;
}

int maxx(int i, int j, int k) {
    return max(i, max(j, k));
}

int Cmp(const void * a, const void * b) {
    return *(int*)a - *(int*)b;
}

int maxSatisfaction(int* a, int n){
    int f[n+2][n+2];
    memset(f, 0 , sizeof(f));
    qsort(a, n, sizeof(int), Cmp);
    f[0][1] = a[0];  
    
    for (int i=1; i<n; i++)
        for (int t=1; t<=i+1; t++) {
            f[i][t] = f[i-1][t-1]+a[i]*t;
            for (int j=t-1; j<i; j++) 
                f[i][t] = maxx(f[i][t], f[j][t], f[j][t-1]+a[i]*t);
        }  
            
    int ans = 0;
    for (int t=0; t<=n; t++)
        ans = max(f[n-1][t], ans);
    return ans;
}
```
<br/>

# 参考资料
[LeetCode-1402](https://leetcode.com/problems/reducing-dishes/)

[C Two different Solutions](https://leetcode.com/problems/reducing-dishes/discuss/2410176/C-Two-different-Solutions)