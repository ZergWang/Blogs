# 定义
#### P问题
当解决某个问题的算法的时间复杂度为多项式级，如$O(log N)、O(N^a)$，则该问题属于P问题。换句话说，能在多项式时间内解决的问题为P问题。
#### NP问题
对于某个问题，能在多项式时间内验证某个答案是否为该问题的解，则该问题是NP问题。对于NP问题，可能无法在多项式时间内求解，但可以在多项式时间内验证该问题的某个解。

因此，P问题必定为NP问题，但反过来，NP是否等于P呢，目前尚无定论。如果能证明某个NP问题不存在多项式级复杂度的算法，则能证明NP$\neq$P，但目前
#### NPC问题



<br/><br/>

# LeetCode 2226
#### 问题描述
给出n堆糖果以及每堆中糖果的具体颗数。我们可以将任意一堆糖果分成任意数量的多堆，但不可合并。现在有k个小朋友，每个小朋友可以取其中任意一堆糖果，但要求每个小朋友得到的糖果数量一样。问每个小朋友能得到的糖果数量最多？输出糖果的颗数。

#### 数据范围
$1 \leq n \leq 10^5 $

$1 \leq candies[i] \leq 10^7 $

$1 \leq k \leq 10^{12} $

#### 思路解析

#### 程序展示
```cpp
int maximumCandies(int* a, int n, long long k){
    long long sum = 0;
    int maxPile = 0, left = 0, right, num;
    for (int i=0; i<n; i++) {
        sum += a[i];
        maxPile = a[i] > maxPile ? a[i] : maxPile;
    }

    right = sum/k > maxPile ? maxPile : sum/k;

    while (left < right) {
        sum = 0;
        num = (left + right + 1)/2;
        for (int i=0; i<n; i++) 
            sum += a[i]/num;
        if (sum>=k) 
            left = num;
        else 
            right = num-1;
    }
    return right;
}
```

<br/><br/>
# 参考资料
[什么是P问题、NP问题和NPC问题](http://www.matrix67.com/blog/archives/105)
[16 年后重谈 P 和 NP](http://www.matrix67.com/blog/archives/7084)