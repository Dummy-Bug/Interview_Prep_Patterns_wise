# Problems on LIS Pattern

### 1. Longest Increasing Subsequence
Given an integer array nums, return the length of the longest strictly increasing subsequence.

**Approach 1 : Dynamic Programming | O(n2)**

```cpp
int lengthOfLIS(vector<int>& nums) {
    int n = nums.size();
    vector<int> dp (n, 1);

    int ans = 1;

    for(int i=1; i<n; i++){
        for(int j=0; j<i; j++){

            if(nums[j]<nums[i])
                dp[i] = max(dp[i], dp[j]+1);

            ans = max(ans, dp[i]);
        }
    }

    return ans;
}
```

**Approach 2 : Binary Search (lower_bound) | O(nlogn)**

--> This will only give length of LIS

```cpp
int lengthOfLIS(vector<int>& nums) {
    vector<int> v;

    for(int num : nums){
    
        if(v.empty() or num>v.back())
            v.push_back(num);

        else{
            int idx = lower_bound(v.begin(), v.end(), num) - v.begin();
            v[idx] = num;
        }
    }

    return v.size();
}
```

### 2. Longest Arithmetic Subsequence
Given an array nums of integers, return the length of the longest arithmetic subsequence in nums. Recall that a sequence seq is arithmetic if seq[i+1] - seq[i] are all the same value (for 0 <= i < seq.length - 1).

Input: nums = [9,4,7,2,10]

Output: 3

```cpp
int longestArithSeqLength(vector<int>& nums) {
    int n = nums.size();
    int ans = 2;

    /* vector of unordered_map will contain all possible diff of curr_nums with max_len so far */

    vector<unordered_map<int,int>> dp (n, unordered_map<int,int>());

    for(int i=1; i<n; i++){
        for(int j=0; j<i; j++){
            int diff = nums[i]-nums[j];

            if(dp[j].find(diff)!=dp[j].end())
                dp[i][diff] = dp[j][diff]+1;
            else
                dp[i][diff] = 2;

            ans = max(ans, dp[i][diff]);
        }
    }   

    return ans;
}
```

### 3. Length of Longest Fibonacci Subsequence
A sequence x1, x2, ..., xn is Fibonacci-like if:
1. n >= 3
2. xi + xi+1 == xi+2 for all i + 2 <= n

Given a strictly increasing array arr of positive integers forming a sequence, return the length of the longest Fibonacci-like subsequence of arr. If one does not exist, return 0.

Input: arr = [1,3,7,11,12,14,18]

Output: 3

```cpp
int lenLongestFibSubseq(vector<int>& arr) {
    int n = arr.size();
    int ans = 2;
    vector<unordered_map<int,int>> dp (n, unordered_map<int,int>());

    for(int i=1; i<n; i++){
        for(int j=0; j<i; j++){

            int diff = arr[i] - arr[j];

            if(dp[j].find(diff)!=dp[j].end())
                dp[i][arr[j]] = dp[j][diff]+1;
            else
                dp[i][arr[j]] = 2;

            ans = max(ans, dp[i][arr[j]]);
        }
    }

    return ans==2 ? 0 : ans;
}
```
