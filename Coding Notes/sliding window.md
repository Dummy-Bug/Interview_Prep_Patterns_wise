# Sliding Window

## Type 1 : Fixed Window Size

#### 1. Count Occurences of Anagrams
Given a word pat and a text txt. Return the count of the occurences of anagrams of the word in the text.

Hint: Create a count array for storing frequency

```cpp
int count_occurance(string pat, string txt) {
    int ans = 0;
    vector<int> v1 (26, 0);
    vector<int> v2 (26, 0);

    int k = pat.size();
    int n = txt.size();
    int i=0;

    for( ; i<k; i++){
        v1[pat[i]-'a'] += 1;
        v2[txt[i]-'a'] += 1;
    }
    if(v1==v2) ans+=1;

    for( ; i<n; i++){
        v2[txt[i]-'a'] +=1;
        v2[txt[i-k]-'a'] -=1;

        if(v1==v2) ans+=1;
    }
    return ans;
}
```


#### 2. First negative integer in every window of size k

Hint: The idea is to have a variable firstNegativeIndex to keep track of the first negative element in the k sized window.

```cpp
vector<long long> printFirstNegativeInteger(long long int A[], long long int N, long long int k) {  
    vector<long long> v;                                   
    int first_negative_index = -1;
    int next_index = 0;
    
    for(int i=0; i<k; i++){
        if(A[i]<0){
            first_negative_index=i;
            break;
        }
    }
    
    if(first_negative_index != -1){
        v.push_back(A[first_negative_index]);
        next_index = first_negative_index+1;
    }
    else{
        v.push_back(0);
        next_index = k; 
    }
        
    for(int i=1; i<N-k+1; i++){
        
        if(first_negative_index>=i) 
            v.push_back(A[first_negative_index]);
        
        else{
            for(int j=next_index; j<i+k; j++){
                if(A[j]<0){
                    first_negative_index = j;
                    break;
                }
            }
            if(first_negative_index>=i){
                v.push_back(A[first_negative_index]);
                next_index = first_negative_index+1;
            } 
            else{
                v.push_back(0);
                next_index = i+k;
            }
        }
    }
    return v;
 }
```

#### 3. Maximum Points You Can Obtain from Cards (Think in reverse)
There are several cards arranged in a row, and each card has an associated number of points. The points are given in the integer array cardPoints. In one step, you can take one card from the beginning or from the end of the row. You have to take exactly k cards. Your score is the sum of the points of the cards you have taken. Given the integer array cardPoints and the integer k, return the maximum score you can obtain.

Input: cardPoints = [1,2,3,4,5,6,1], k = 3

Output: 12

Hint: Similar to minimum sum windows of size (n-k)

```cpp
int maxScore(vector<int>& cardPoints, int k) {
    int totalSum = 0;
    for(int point : cardPoints)
        totalSum += point;

    int window_size = cardPoints.size() - k;

    int i=0;
    int minSum = 0, windowSum = 0;

    for( ; i<window_size; i++)
        windowSum += cardPoints[i];

    minSum = windowSum;

    for( ; i<cardPoints.size(); i++){
        windowSum += cardPoints[i];
        windowSum -= cardPoints[i-window_size];

        minSum = min(minSum, windowSum);
    }

    return totalSum - minSum;
}
```

## Type 2 : Variable Window Size

These type of problems can be solved with two poiters i and j. `i` points to the leftmost element of window (including i) and `j` points to rightmost element of window (including j)

#### 1. Longest Substring Without Repeating Characters

```cpp
int lengthOfLongestSubstring(string s) {
    if(s.length()==0) 
        return 0;

    vector<int> last_index (256, -1);  // It will store index of last occurance
    int i=0, j=0;
    int ans = 1;

    while(j<s.length()){
        char ch = s[j];

        if(last_index[ch]>=i)
            i = last_index[ch]+1;

        last_index[ch] = j;
        ans = max(ans, j-i+1);
        j++;
    }

    return ans;
}
```

#### 2. Longest Substring with atmost k distinct characters

```cpp
int longest_substring(string s, int k){
    unordered_map<char, int> freq;
    int ans=0;
    int i=0, j=0;

    while(j<s.size()){
        char ch = s[j];
        freq[ch]+=1;

        while(freq.size()>k){
            char temp = s[i];
            i++;

            if(freq[temp]>1) freq[temp]--;
            else freq.erase(temp); 
        }
        
        ans = max(ans, j-i+1);
        j++;
    }
    return ans;
}
```

**Similar Questions**

1. Fruit in a Basket (leetcode)
2. Pick toys (aditya verma playlist)

#### 3. Longest Repeating Character Replacement (Tricky)
You are given a string s and an integer k. You can choose any character of the string and change it to any other uppercase English character. You can perform this operation at most k times.

Input: s = "ABAB", k = 2

Output: 4

Hint: Given this, we can apply the at most k changes constraint and maintain a sliding window such that

`(length of substring - number of times of the maximum occurring character in the substring) <= k`

```cpp
int characterReplacement(string s, int k) {
    vector<int> freq (26, 0);
    int ans = 0;
    int i=0, j=0;
    int total_charCount = 0, max_freq_count = 0;

    while(j<s.length()){
        char ch = s[j];
        freq[ch-'A']++;
        total_charCount++;

        if(max_freq_count<freq[ch-'A'])
            max_freq_count = freq[ch-'A'];

        if(total_charCount - max_freq_count > k){
            char temp = s[i];
            freq[temp-'A']--;
            total_charCount--;
            i++;
        }

        ans = max(ans, j-i+1);
        j++;
    }
    return ans;
}
```

#### 4. Smallest Distinct Window
Given a string 's'. The task is to find the smallest window length that contains all the characters of the given string at least one time. For eg. A = “aabcbcdbca”, then the result would be 4 as of the smallest window will be “dbca”.

```cpp
string smallestWindow(string s){
    unordered_set<char> st;
    for(auto ch : s) st.insert(ch);
    int k = st.size();            // Number of unique elements

    string ans = s;
    unordered_map<int,int> freq;
    int i=0, j=0;

    while(j<s.size()){
        char ch = s[j];
        freq[ch] += 1;

        while(freq.size()==k){
            if(j-i+1<ans.size())
                ans = s.substr(i, j-i+1);

            char temp = s[i];

            if(freq[temp]==1) freq.erase(temp);
            else freq[temp]--;

            i++;
        }

        j++;
    }
    return ans;
}
```

#### 5. Minimum Window Substring
Given two strings s and t of lengths m and n respectively, return the minimum window substring of s such that every character in t (including duplicates) is included in the window. If there is no such substring, return the empty string "".

Hint: Use two vectors for storing frequency of characters. Use one count variable. Increment count whenever `freq_t[ch]>0 and freq_s[ch]==freq_t[ch]` and decrement it whenever `freq_t[temp]>0 and freq_s[temp]<freq_t[temp]`

[Video explaination](https://www.youtube.com/watch?v=iwv1llyN6mo)

```cpp
string minWindow(string s, string t) {
    if(t.length()>s.length()) 
        return "";

    vector<int> freq_s (128, 0);
    vector<int> freq_t (128, 0);

    for(char ch : t)
        freq_t[ch]++;

    /* No. of distinct characters in t */
    int k=0;            
    for(int i=0; i<128; i++)
        if(freq_t[i]>0) k++;

    /* It stores count of characters which are fulfilled completely so far */
    int count=0; 

    string ans = s;
    bool found = false;
    int i=0, j=0;

    while(j<s.length()){
        char ch = s[j];
        freq_s[ch]++;

        if(freq_t[ch]>0 and freq_s[ch]==freq_t[ch])
            count++;

        while(count==k){
            found = true;
            if(j-i+1 < ans.length())
                ans = s.substr(i, j-i+1);

            char temp = s[i];
            freq_s[temp]--;

            if(freq_t[temp]>0 and freq_s[temp]<freq_t[temp]) count--;

            i++;
        }

        j++;
    }

    if(!found) return "";
    return ans;
}
```

#### 6. Minimum Operations to Reduce X to Zero (Think in Reverse)
You are given an integer array nums and an integer x. In one operation, you can either remove the leftmost or the rightmost element from the array nums and subtract its value from x. Note that this modifies the array for future operations. Return the minimum number of operations to reduce x to exactly 0 if it's possible, otherwise, return -1.

Input: nums = [1,1,4,2,3], x = 5

Output: 2

Hint: Similar to finding longest window having sum = totalSum-x

```cpp
int minOperations(vector<int>& nums, int x) {
    int totalSum = 0;
    for(int num : nums)
        totalSum += num;

    if(totalSum<x) return -1;

    int required_windowSum = totalSum-x;
    int windowSize = 0;
    int sum = 0; 
    int i=0, j=0;
    bool found = false;

    while(j<nums.size()){
        sum += nums[j];

        while(sum>required_windowSum){
            sum -= nums[i];
            i++;
        }

        if(sum==required_windowSum){
            found = true;
            windowSize = max(windowSize, j-i+1);
        }

        j++;
    }

    if(!found) return -1;
    return nums.size()-windowSize;
}
```

#### 7. Max Consecutive Ones III
Given a binary array nums and an integer k, return the maximum number of consecutive 1's in the array if you can flip at most k 0's.

```cpp
    int longestOnes(vector<int>& nums, int k) {
        int ans = 0;
        int i=0, j=0;
        int zero_count = 0;
        
        while(j<nums.size()){
            if(nums[j]==0) zero_count++;
            
            while(zero_count>k){
                if(nums[i]==0) zero_count--;
                i++;
            }
            
            ans = max(ans, j-i+1);
            j++;
        }
        return ans;
    }
```

#### 8. Get Equal Substrings Within Budget
You are given two strings s and t of the same length. You want to change s to t. Changing the i-th character of s to i-th character of t costs |s[i] - t[i]| that is, the absolute difference between the ASCII values of the characters. You are also given an integer maxCost. Return the maximum length of a substring of s that can be changed to be the same as the corresponding substring of twith a cost less than or equal to maxCost. If there is no substring from s that can be changed to its corresponding substring from t, return 0.

Input: s = "abcd", t = "bcdf", maxCost = 3

Output: 3

```cpp
int equalSubstring(string s, string t, int maxCost) {
    int ans = 0;
    int i=0, j=0;
    int cost = 0;

    while(j<s.length()){
        cost += abs(s[j]-t[j]);

        while(cost>maxCost){
            cost -= abs(s[i]-t[i]);
            i++;
        }

        ans = max(ans, j-i+1);
        j++;
    }
    return ans;
}
```

#### 9. Count Number of Nice Subarrays (Tricky)
Given an array of integers nums and an integer k. A continuous subarray is called nice if there are k odd numbers on it. Return the number of nice sub-arrays.

Hint: Convert all odd numbers to 1 and all even numbers to 0. Now problem is reduced to number of subarrays having sum equals k.

```cpp
int no_of_subarrays(vector<int> & v, int k){
    unordered_map<int,int> mp;
    mp[0] = 1;
    int count = 0;
    int csum = 0;

    for(int num : v){
        csum += num;

        if(mp.find(csum-k)!=mp.end())
            count+=mp[csum-k];

        mp[csum]++; 
    }

    return count;
}



int numberOfSubarrays(vector<int>& nums, int k) {
    for(int i=0; i<nums.size(); i++){
        if(nums[i]%2==0)
            nums[i]=0;
        else
            nums[i]=1;
    }

    // Now problem reduced to no. of subarrays whose sum equals to k

    return no_of_subarrays(nums, k);
}
```

