# 博文内容简介

本博客记载在LeetCode周赛上遇到的一些做不出来且难以分类到某种算法上的题目。


# 周赛400第4题：[LeetCode 3171](https://leetcode.com/problems/find-subarray-with-bitwise-or-closest-to-k/)

给出一个int型数组a和正整数k，从a中找出一个子数组，子数组中所有元素或运算的结果与k的差值最小，输出最小的差值。

最简单的想法是暴力枚举出所有的子数组，算出每个子数组或运算的结果，然后与k的差值比较即可。首先我们声明一个全为0的数组b，然后枚举每个```a[i]```，然后令```a[i]```分别与```b[0]```到```b[i]```执行或运算，结果存储到对应b数组中。这样一来，每枚举一个```a[i]```，都能得到```a[0]```到```a[i]```组成的所有子数组。

| 枚举 | b[0]存储的值 | b[1]存储的值 | b[2]存储的值|
| --   | --          | --          | --          |
| i = 0| a[0]        | 0        | 0       |
| i = 1| a[0] \| a[1] | a[1]        |  0     |
| i = 2| a[0] \| a[1] \| a[2]| a[1] \| a[2]| a[2]|
| ... |

然而，上述枚举仍是$O(N^2)$的时间复杂度，仍会超时。实际上，利用位运算的性质可以优化到$O(N\log N)$。

首先明确一个知识点：当一个数逐步与其他多个数作或运算，结果要么不变，要么越来越大。这是因为或运算可以看作是求两个集合（在这里指数字）的并集，因此无论与什么数作或运算，结果必然不会变小。同理，与运算可以看作是求两个集合的交集，因此当一个数逐步与其他多个数作与运算，结果要么不变，要么越来越小。

利用上述性质，假设当```b[j] |= a[i]``` ( 0 < j < i)后```b[j]```值不变，这说明在执行或运算前，```a[i]```是```b[j]```的子集（即$ a[i] \subseteq b[j]$），那么此时对b的循环就可以break了，因为```a[i]```与```b[j]```执行或运算前，```b[j-1]```等于```a[j-1] | b[j]```，```b[j]```必为```b[j-1]```的子集（即 $b[j] \subseteq b[j-1]$）。因此```a[i]```必定为```b[j-1]```的子集，他们或运算后```b[j-1]```结果必定不变。同理，```a[i]```与b数组前面的元素或运算的结果也不变，因此可以直接break。

```cpp
class Solution {
public:
    int minimumDifference(vector<int>& a, int k) {
        int ans = INT_MAX;
        for (int i=0; i<a.size(); ++i) {
            ans = min(ans, abs(a[i]-k));
            for (int j=i-1; j>=0; --j) 
                if ((a[j] | a[i]) == a[j])
                    break;
                else {
                    a[j] |= a[i];
                    ans = min(ans, abs(a[j]-k));
                }
        }

        return ans; 
    }
};
```

以上性质同样适用于与运算。[LeetCode 1521](https://leetcode.com/problems/find-a-value-of-a-mysterious-function-closest-to-target/)把或运算改成与运算。其他地方和周赛这题一样，直接将周赛的代码中的或运算改成与运算就能过这道题。



<br/><br/>

# 周赛401第4题：[LeetCode 3181](https://leetcode.com/problems/maximum-total-reward-using-operations-ii/)

给出一个int型数组a，可逐步取该数组中的元素，数组中每个元素最多取一次。如果取了某个元素，则当前的得分会加上该元素的值。但是在取元素时，待取元素的值必须大于当前得分才能取。问能得到的最高得分是多少。

变种版本的01背包问题。为了达到最高得分，取的时候必须要先取小元素，再去大元素。因此先对a数组从小到大排序。然后用01背包的思想去做即可。

```cpp
class Solution {
public:
    int maxTotalReward(vector<int>& a) {
        sort(a.begin(), a.end());
        bool f[100005];    // f[j]为1表示最终得分可以达到j
        memset(f, 0, sizeof(f));

        f[0] = 1;
        for (int i=0; i<a.size(); ++i)
            for (int j=2*a[i]-1; j>= a[i]; --j)
    //取了第i个元素后得分达到j，那么在取之前的得分必须小于a[i]才行，即j-a[i] < a[i]，也就是说j不能大于2*a[i]。此为循环的边界条件
                f[j] = max(f[j], f[j-a[i]]);
        
        for (int i=100000; i>=0; --i)
            if (f[i])
                return i;
                
        return 0;
    }
};
```

上述代码可以过周赛的第3题，但第4题的$n=50000$，必须要进一步的优化。

首先可以使用bitset来替代布尔数组，从而节省空间，并使用位运算进行状态转移。这样一来可勉强将时间复杂度压缩到$10^8$内。

首先，```f[j] = max(f[j], f[j-a[i]])```可优化为```f[j] |= f[j-a[i]]```。其次，内层j的循环可以看作是如下图所示的，左边的一共a[i]位与右边的a[i]位一一进行或运算，结果赋值到左边对应位置。
![](LeetCode周赛查漏补缺_1.png)

使用位运算则可免除内层j的循环。我们可以生成一个新的bitset变量k，其中k的```a[i]```位到```2a[i]-1```位是变量f的0到```a[i]-1```位的值，k的其他部分均为0，然后我们令```f |= k```即可完成状态转移。

假设f的长度为m，则首先令k等于f左移m-a[i]位，此时k的最左边的a[i]位的值等于原来f的0到```a[i]-1```位，k剩下的所有位都为0。然后再让k右移m-2a[i]位，则k的```a[i]```位到```2a[i]-1```位是变量f的0到```a[i]-1```位的值，其他位都为0。

```cpp
class Solution {
public:
    int maxTotalReward(vector<int>& a) {
        sort(a.begin(), a.end());
        bitset<100005> f;
        f[0] = 1;
        for (int i=0; i<a.size(); ++i) 
            f |= f << (f.size() - a[i]) >> (f.size() - a[i]- a[i]);

        for (int i=100000; i>=0; --i)
            if (f[i])
                return i;
                
        return 0;
    }
};
```


# 周赛402第4题：[LeetCode 3197](https://leetcode.com/problems/find-the-minimum-area-to-cover-all-ones-ii/)

给出一个仅由0和1构成的二维数组，现在需要三个矩形去覆盖其中所所有的1。这三个矩形面积至少为1，且互不重叠。问符合要求的三个矩形面积最小为多少。

本题解思路参考资料：[六种情况，从 O((mn)^2) 到 O(mn)](https://leetcode.cn/problems/find-the-minimum-area-to-cover-all-ones-ii/solutions/2819357/mei-ju-pythonjavacgo-by-endlesscheng-uu5p/)

周赛第2题与此题类似，但是仅使用一个矩形去覆盖所有的1。可以考虑将第4题拆分成多个由多个第2题的问题。

首先考虑这三个矩形的位置关系，不难发现有如下6种情况：

![](LeetCode周赛查漏补缺_2.png)

因此，我们首先分6种情况，对于每种情况来枚举对应的蓝色分割线，将二维数组分成三个区域，对于每个区域直接复用第2题的代码即可求解。

进一步的优化：不难发现，case 4、5、6分别是case 1、2、3旋转90度后的情况，因此我们实际编码时只需要完成case 1、2、3的编码即可。在解决完case 1、2、3后将二维数组旋转，再执行case 1、2、3的代码。（实际写起来一个case的代码挺短，不旋转也行）


```cpp
class Solution {
public:
    int F(vector<vector<int>>& a, int ttop, int bbot, int llef, int rrig) {
        int top = 9999, bot = -1, lef = 9999, rig = -1;

        for (int i=ttop; i<=bbot; ++i)
            for (int j=llef; j<=rrig; ++j)
                if (a[i][j]) {
                    top = min(top, i);
                    bot = max(bot, i);
                    lef = min(lef, j);
                    rig = max(rig, j);
                }

        return (bot-top+1)*(rig-lef+1);
    }


    int minimumSum(vector<vector<int>>& a) {
        int n = a.size(), m = a[0].size();
        int ans = 99999;

        for (int i=0; i<n-2; ++i)     //case 1: i为上分割线，j为下分割线 
            for (int j=i+1; j<n-1; ++j) 
                ans = min(ans, F(a, 0, i, 0, m-1) + F(a, i+1, j, 0, m-1) + F(a, j+1, n-1, 0, m-1));
        
        for (int i=0; i<n-1; ++i)     //case 2: i为横分割线，j为竖分割线 
            for (int j=0; j<m-1; ++j) 
                ans = min(ans, F(a, 0, i, 0, m-1) + F(a, i+1, n-1, 0, j) + F(a, i+1, n-1, j+1, m-1));

        for (int i=0; i<n-1; ++i)     //case 3: i为横分割线，j为竖分割线 
            for (int j=0; j<m-1; ++j) 
                ans = min(ans, F(a, 0, i, 0, j) + F(a, 0, i, j+1, m-1) + F(a, i+1, n-1, 0, m-1));

        for (int i=0; i<m-2; ++i)     //case 4: i为左分割线，j为右分割线 
            for (int j=i+1; j<m-1; ++j) 
                ans = min(ans, F(a, 0, n-1, 0, i) + F(a, 0, n-1, i+1, j) + F(a, 0, n-1, j+1, m-1));
        
        for (int i=0; i<n-1; ++i)     //case 5: i为横分割线，j为竖分割线 
            for (int j=0; j<m-1; ++j) 
                ans = min(ans, F(a, 0, i, 0, j) + F(a, i+1, n-1, 0, j) + F(a, 0, n-1, j+1, m-1));
      
        for (int i=0; i<n-1; ++i)     //case 6: i为横分割线，j为竖分割线 
            for (int j=0; j<m-1; ++j) 
                ans = min(ans, F(a, 0, n-1, 0, j) + F(a, 0, i, j+1, m-1) + F(a, i+1, n-1, j+1, m-1));

        return ans;
    }
};
```
