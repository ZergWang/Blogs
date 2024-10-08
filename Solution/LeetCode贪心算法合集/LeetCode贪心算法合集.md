# 洛谷 P2242

题目链接：https://www.luogu.com.cn/problem/P2242

公路上有n（n<=15000）个坑需要维修，因此要封住m段公路。给出坑的坐标，问在不漏掉任何一个坑的情况下，最少要封多少米的路。
比如说有5个坑的坐标：1 2 5 6 8 9，要封3段路，那么最优解就是封1\~2、5\~6、8~9 ，最少封了6个单位长的路。

一开始我从Dp的角度思考，也有了思路，我的状态转移方程是这样的： f[k][i]=min(f[k-1][i]+s[j],f[k-1][i])

(f[k][i]表示在封k段公路且覆盖第1个坑到第i个坑的时候的最少的封路的长度，s[i]表示这k段公路在k-1段公路的基础上又多出一段后减少的封路值，当我们决策到i时有两个选择，那就是：1.第i个坑与之前的所有坑连在一起为一段，2.自己一个坑一段。我们需要比较找出最小值就可以了)
Dp虽然好，时间复杂度为O（n^2），题目给出的n最大为15000，这样用DP反而会超时。而用贪心就能得到正解。
还是这5个坑：1  2  5  6  8  9

Data[i+1]-data[i]，得: 1  3  1  1  2  1
将新数组排序，得3  2  1  1  1
9-1+1-(3-1)-(2-1)=6
9-1+1是求出从第一个坑封到最后一个坑要封掉的路的长度。
而（3-1）和（2-1）就是说明在两个地方断开后能减少的最大封路的长度。
为什么这样做？因为题目要求最少得封路长度，而将几个坑连在一起封就会导致一些没有坑的公路被封掉。这样我们求出两个坑i,j之间的距离x，就能知道如果把i,j连在一起封就会多封x个单位长度的路，我们将计算出的新值排序，就能知道在哪里断开能最大地减少封路的长度，再减去即可得到解。
```cpp
#include <fstream>
#include <algorithm> 
using namespace std;
ifstream fin("公路维修.in");
ofstream fout("公路维修.out");
 

int a[15000],data[15000],n,m,ans;
bool f[15000];
void init();
void work();
bool cmp(int x,int y);
int main()

{
  init();
  sort(data+1,data+n-1,cmp);   //快排
  work();   
}

 

bool cmp(int x,int y)     //确定排序是升序还是降序
{
  return x>y;
}

void init()
{
  fin>>n>>m;
  for (int i=1;i<=n;++i) fin>>a[i];
  for (int i=1;i<n;++i) data[i]=a[i+1]-a[i]-1;  //生成新数组
  ans=a[n]-a[1]+1;  
} 

void work() {
   for (int i=1;i<m;++i)
    {
      ans-=data[i]; //因为输入数据要求分成m段就可以了，那我们就需要截m-1次
    }
   fout<<ans<<endl;
}
```
<br/><br/>

# [LeetCode 1402](https://leetcode.com/problems/reducing-dishes/)
## 题目简介
有n道菜，每道菜有一个值a[i] ( 0 <= i < n) ，现在可以按任意顺序煮这n道菜，每道菜最多煮一次，煮每一道菜需要花费1个单位时间，但会得到该道菜的满意度，该满意度等于a[i]与当前时间的乘积。也就是说，第t个单位时间煮第i道菜，则从该菜中获得的满意度为a[i]*t，总满意度为各道菜满意度之和。求能得到的最大满意度。
<br/>

## 解法一：贪心
越晚做的菜时间权重越大，为了令总满意度最高，所以要排序，满意值大的菜放后面。

然后从后往前检验每道菜，若煮第i道菜能令总满意度增加，则煮，否则就直接结束。这是因为若煮第i道菜令总满意度降低了，说明当前菜的满意度为负数（a[i]<0），且其负满意度甚至能超过之前一系列菜大一级时间权重带来的正满意度（设 f[i] = a[i] + a[i+1] + ...+ a[n-1]，则有f[i] < 0）。而再下一道（i-1）菜的满意度要更低。如果真的要煮第i-1道菜，满意度增加值为 f[i-1] = a[i-1] + f[i]，显然f[i-1]为负数，且f[i-1]<f[i]。 由于a是递增数列，则从i至0，f[i]必为递减数列且均小于0。因此，当煮第i到菜令总满意度降低时，说明在第i+1道菜总满意度达到最大值。

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

## 解法二：动态规划
同样先排序，然后考虑如何划分状态：
1. 首先考虑到f[i]表示从前i道菜中能得到的最大值，但由于时间因素的影响，可能存在f[i-1]取最大值时所消耗时间大于f[i]这种情况，这导致f[i]无法直接利用到前一阶段的数据。

2. 因此，构造二维数组f[i][t]，表示从前i道菜中，只做其中的t道（消耗时间为t）能得到的最大值，将时间这一因素单独拎出来，让对应时间下前一阶段的数据可直接使用。

3. 对f[i][t]而言，当做第i道菜可得到最大值时，可从f[j][t-1]（j<i）中找到最大值，加上做第i道菜得到的满意值即可；若不做第i道菜，直接找f[j][t]（j<i）中的最大值即可。
   
4. 状态转移方程:
```cpp
    f[i][t] = max(f[i][t], f[j][t], f[j][t-1]+a[i]*t);
```

5. 边界条件：注意f[i][t]中，t要始终小于i+1。f[i][0]恒为0。

本方法时间复杂度为$O(n^3)$，空间复杂度$O(n^2)$，均劣于贪心算法。
<br/><br/>

# [LeetCode 134](https://leetcode.com/problems/gas-station/)

n段公路构成了一个环，连接着n个城镇。第i个城镇会有```gas[i]```升的汽油补给，但是要通过连接第i个城镇和第i+1个城镇的公路会消耗掉```cost[i]```升的汽油（连接第0个城镇和第n-1个城镇的公路汽油消耗量为```cost[n-1]```）。现在给出```gas, cost```两数组，问应该从哪个城镇出发，可以环绕一圈回到起点。如果无论从哪个城镇出发都到不了起点，返回-1。

n最大为$10^5$，暴力肯定超时。实际上我们可以从任意一个城镇出发开始模拟（假设起点为a），假如走到城镇b就走不下去了，则说明城镇a到城镇b之间的所有城镇都不能作为起点。因为从城镇a出发，抵达这些中间城镇还未取补给时，汽油余量肯定是大于等于0的。经过某个中间城镇的补给后，汽油余量肯定大于等于直接从这个中间城镇为起点出发时的汽油余量。在这种情况下都不能绕圈，那从这些中间城镇出发，先天条件更差的情况下就更不可能绕圈了。所以下次模拟直接从城镇b开始。使用以上贪心思维可以$O(N)$复杂度求解。

```cpp
class Solution {
public:
    int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
        int n = gas.size();
        for (int i=0; i<n; ++i) {
            gas.push_back(gas[i]);
            cost.push_back(cost[i]);
        }
        // 如果想避免处理环，可直接将数组延长一倍    
        int start = 0;
        int now = 0;
        int fuel = gas[0];
        while (start < n) {
            fuel -= cost[now++];
            if (fuel < 0) {
                start = now;
                fuel = gas[now];
            }
            else {
                if (now == start + n)
                    return start;
                fuel += gas[now];
            }
        }
        return -1;
    }
};
```
<br/><br/>

# [LeetCode 1605](https://leetcode.com/problems/find-valid-matrix-given-row-and-column-sums/)

给出一个```n * m```的由非负整数构成的矩阵，告知矩阵每行以及每列的和，返回一个满足上述条件的矩阵。

本题的贪心思想其实就是一句话：在合法的情况下能填多大的数就填多大的数！我们从左上角（即```ans[0][0]```）开始，假设```row[i]```表示第i行数字的和，```col[i]```表示第i列数字的和，那么由于矩阵由非负整数构成，因此左上角填的数最大为```min(row[0], col[0])```。

假设```row[0] < col[0]```，我们直接把最大的数填上去（令```ans[0][0] = row[0]```），这样一来ans的首行剩余的位置就只能都填0了。至此，我们把问题简化成了：构造一个```(n-1) * m```的矩阵，矩阵首行的和为```row[1]```，次行的和为```row[2]```…… 首列的和为```col[0]-row[0]，```，次列的和为```col[1]```……

重复以上步骤，即可构造出满足条件的矩阵。
```cpp
class Solution {
public:
    vector<vector<int>> restoreMatrix(vector<int>& row, vector<int>& col) {
        int n = row.size(), m = col.size();
        vector<vector<int>> ans (n, vector<int>(m));

        for (int i = 0; i < n; ++i) 
            for (int j = 0; j < m; ++j) {
                ans[i][j] = min(row[i], col[j]);
                row[i] -= ans[i][j];
                col[j] -= ans[i][j];
            }
            
        return ans;
    }
};
```


<br/><br/>

# [LeetCode 910](https://leetcode.com/problems/smallest-range-ii/)

给出一个长为n的数组和一个非负数k，现在需要对数组中的每个数加上k或者减去k，从而让新数组中的最大值和最小值的差最小。


```cpp
int Cmp(const void * a, const void * b) {
    return *(int*)a - *(int*)b;
}

int smallestRangeII(int* a, int n, int k){
    qsort(a, n, sizeof(a[0]), Cmp);
    int ans = a[n-1] - a[0];
    for (int i=0; i<n-1; i++) {
        int min = a[0]+k < a[i+1]-k ? a[0]+k : a[i+1]-k;
        int max = a[i]+k > a[n-1]-k ? a[i]+k : a[n-1]-k;
        ans = max-min < ans ? max-min : ans;
    }
    return ans;
}
```
<br/><br/>

# [LeetCode 621](https://leetcode.com/problems/task-scheduler/)
```cpp
int Cmp(const void * a, const void * b) {
    return *(int*)a - *(int*)b;
}


int leastInterval(char* tasks, int tasksSize, int n){
    int a[26];
    memset(a, 0, sizeof(a));
    for (int i=0; i<tasksSize; i++) 
        a[tasks[i]-'A']++;
    
        
    qsort(a, 26, sizeof(a[0]), Cmp);
    int now = 24;
    while (now>=0 && a[now]==a[25])
        now--;
    
    int ans = (a[25]-1)*(n+1)+25-now;
    return ans > tasksSize ? ans : tasksSize;
}
```
