# [LeetCode 41](https://leetcode.com/problems/first-missing-positive/description/)

给一个长度为n的未经排序的数组，用$O(N)$时间找出该数组缺失的最小正整数。

解法：对于数组中的每个正整数，将该数换到其值对应的数组下标处。经过该处理后，数组中所有正整数都被放置在其对应的下标处。然后从数组下标1开始逐一检查每个位置的数是否与其位置信息相一致。首个不一致处的坐标值即为缺失的最小正整数。

如果在经过交换后数组中每个位置的数是否与其位置信息一致，则说明该数组包含了1到n的所有数，此时答案为n+1。

```cpp
class Solution {
public:
    int firstMissingPositive(vector<int>& a) {
        int n = a.size();
        for (int i=0; i<n; ++i)
            while (a[i] > 0 && a[i] <= n && a[i] != i+1 && a[a[i]-1] != a[i])
                swap(a[i], a[a[i]-1]);
        // 为防止交换时未经处理的正整数被换到数组前部从而无法被for循环覆盖，添加while循环，令被交换的数提前被处理到合适位置   
        for (int i=0; i<n; ++i)
            if (a[i] != i+1)
                return i+1;
        return n+1;
    }
};

```

