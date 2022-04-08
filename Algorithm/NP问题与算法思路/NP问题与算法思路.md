# LeetCode 2226
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