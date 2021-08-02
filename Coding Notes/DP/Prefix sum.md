# Problems based on Prefix Sum

### 1. XOR Queries of a Subarray
Given the array arr of positive integers and the array queries where queries[i] = [Li, Ri], for each query i compute the XOR of elements from Li to Ri (that is, arr[Li] xor arr[Li+1] xor ... xor arr[Ri] ). Return an array containing the result for the given queries.

Input: arr = [1,3,4,8], queries = [[0,1],[1,2],[0,3],[3,3]]

Output: [2,7,14,8] 

Hint: x ^ y ^ x = x (Compute the prefix sum for XOR)

```cpp
vector<int> xorQueries(vector<int>& arr, vector<vector<int>>& queries) {
    int n = arr.size();
    vector<int> res;
    vector<int> prefix_xor (n, 0);

    prefix_xor[0] = arr[0];

    /* computing xor prefix */

    for(int i=1; i<n; i++)
        prefix_xor[i] = prefix_xor[i-1]^arr[i];

    for(auto q : queries){
        int l = q[0], r = q[1];

        if(l==0) 
            res.push_back(prefix_xor[r]);
        else
            res.push_back(prefix_xor[r] ^ prefix_xor[l-1]);       // --> XOR of elements lie inside the given range
    }

    return res;
}
```

### 2. Sum of Absolute Differences in a Sorted Array
You are given an integer array nums sorted in non-decreasing order. Build and return an integer array result with the same length as nums such that result[i] is equal to the summation of absolute differences between nums[i] and all the other elements in the array.

In other words, result[i] is equal to sum(|nums[i]-nums[j]|) where 0 <= j < nums.length and j != i (0-indexed).

Input: nums = [2,3,5]

Output: [4,3,5]

```cpp
vector<int> getSumAbsoluteDifferences(vector<int>& nums) {
    int n = nums.size();
    vector<int> res(n, 0);
    vector<int> csum(n, 0);
    csum[0] = nums[0];

    for(int i=1; i<n; i++)
        csum[i] = csum[i-1] + nums[i];


    for(int i=0; i<n; i++){
        int left = 0, right = 0;

        left = csum[i];
        right = csum[n-1] - csum[i];
        res[i] = ((i+1)*nums[i] - left) + (right - (n-i-1)*nums[i]);   
    }
    return res;
}
```

### 3. Maximum Sum of Two Non-Overlapping Subarrays
Given an array nums of non-negative integers, return the maximum sum of elements in two non-overlapping (contiguous) subarrays, which have lengths firstLen and secondLen. The firstLen-length subarray could occur before or after the secondLen-length subarray.

Input: nums = [0,6,5,2,2,5,1,9,4], firstLen = 1, secondLen = 2

Output: 20

Hint: Precompute max sum for all subarrays starting from right of every index i. Do it for both subarray of len1 and len2.

```cpp
void fill_maxSubarrSum (vector<int> & nums, vector<int> & maxSubarrSum, vector<int> & prefixSum, int len, int n){
    int i=n-len, j=n-1;
    int mx_so_far = 0;

    while(i>0){
        int sum = prefixSum[j] - prefixSum[i-1];
        mx_so_far = max(mx_so_far, sum);

        maxSubarrSum[i] = mx_so_far;
        i--; j--;
    }

    maxSubarrSum[i] = max(mx_so_far, prefixSum[j]);      // --> boundary case for 0th index
}


int maxSumTwoNoOverlap(vector<int>& nums, int firstLen, int secondLen) {
    int n = nums.size();
    int ans = 0;

    vector<int> maxSubarrSum1 (n, -1);      // --> contains max sum of every subarray of len1 starting from right of idx i
    vector<int> maxSubarrSum2 (n, -1);      // --> contains max sum of every subarray of len2 starting from right of idx i

    vector<int> prefixSum (n, 0);
    prefixSum[0] = nums[0];

    for(int i=1; i<n; i++) 
        prefixSum[i] = prefixSum[i-1] + nums[i];

    fill_maxSubarrSum (nums, maxSubarrSum1, prefixSum, firstLen, n);
    fill_maxSubarrSum (nums, maxSubarrSum2, prefixSum, secondLen, n);

    /* Iterating for every index and computing sum */

    for(int i=0; i<=n-(firstLen + secondLen); i++){

        /* Checking for subarr1 in left and subarr2 in right */

        int s1 = i==0 ? prefixSum[i+firstLen-1] : prefixSum[i+firstLen-1] - prefixSum[i-1];
        int s2 = maxSubarrSum2[i+firstLen];
        ans = max(ans, s1+s2);

        /* Checking for subarr2 in left and subarr1 in right */


        s1 = i==0 ? prefixSum[i+secondLen-1] : prefixSum[i+secondLen-1] - prefixSum[i-1];
        s2 = maxSubarrSum1[i+secondLen];
        ans = max(ans, s1+s2);
    }

    return ans;
}
```

### 4. Flip String to Monotone Increasing
A binary string is monotone increasing if it consists of some number of 0's (possibly none), followed by some number of 1's (also possibly none). You are given a binary string s. You can flip s[i] changing it from 0 to 1 or from 1 to 0. Return the minimum number of flips to make s monotone increasing.

Input: s = "00011000"

Output: 2

Hint: Precompute one cnt on left side of every index and zero cnt on right side of every index (including curr idx)

```cpp
int minFlipsMonoIncr(string s) {
    int n = s.length();
    int minFlip = n;

    vector<int> oneCount (n+1, 0);      // --> keeps cnt of 1 on left side of idx without including curr idx
    vector<int> zeroCount (n+1, 0);     // --> keeps cnt of 0 on right side of idx including curr idx

    for(int i=n-1; i>=0; i--){
        if(s[i]=='0')
            zeroCount[i] = zeroCount[i+1]+1;
        else
            zeroCount[i] = zeroCount[i+1];
    }

    for(int i=1; i<=n; i++){
        if(s[i-1]=='1')
            oneCount[i] = oneCount[i-1] + 1;
        else
            oneCount[i] = oneCount[i-1];
    }

    /* So number of flips on each index = cnt of one on left side of idx + cnt of zero on right side if idx (including idx) */

    /* Beware of corner cases, loop will run from 0 to n and not n-1 */

    for(int i=0; i<=n; i++){
        int flips = zeroCount[i]+oneCount[i];
        minFlip = min(flips, minFlip);
    }

    return minFlip;
}
```

## @ Problems based on Range Queries

### 1. Corporate Flight Bookings
There are n flights that are labeled from 1 to n. You are given an array of flight bookings bookings, where bookings[i] = [firsti, lasti, seatsi] represents a booking for flights firsti through lasti (inclusive) with seatsi seats reserved for each flight in the range.

Return an array answer of length n, where answer[i] is the total number of seats reserved for flight i.

Input: bookings = [[1,2,10],[2,3,20],[2,5,25]], n = 5

Output: [10,55,45,25,25]

```cpp
/* 
    Approach based on Rachit's Jain trick 

    For every range query, just add number of seat to v[left] 
    and subtract number of seats from v[right+1]

    After doing this for all queries, compute prefix sum and return the vector
*/

vector<int> corpFlightBookings(vector<vector<int>>& bookings, int n) {
    vector<int> v (n, 0);

    for(auto b : bookings){
        int start = b[0], end = b[1], seats = b[2];

        v[start-1] += seats;    // --> Since Indexing is 0 based
        if(end<n) v[end] -= seats;
    }

    for(int i=1; i<n; i++)
        v[i] += v[i-1];

    return v;
}
```