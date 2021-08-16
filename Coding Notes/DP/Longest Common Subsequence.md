# Problems on LCS Pattern

### @ Longest Common Subsequence (Parent Problem)
Given two strings s1 and s2, return the length of their longest common subsequence.

**Method 1 : Memorization (Top-Down)**

```cpp
int solve (string & s1, string & s2, int i, int j, vector<vector<int>>& dp){

    /* Base condition is when any ptr reaches at the end of string */

    if(i==s1.length() or j==s2.length()) return 0;   

    if(dp[i][j]!=-1) return dp[i][j];

    /* If curr_char of s1 is equal to curr_char of s2 then we add 1 and increment both i and j */

    if(s1[i]==s2[j]){
        int res = 1 + solve(s1, s2, i+1, j+1, dp);
        return dp[i][j] = res;
    }

    /* Else we have two choices, either to increment i or increment j */

    else{
        int res1 = solve (s1, s2, i+1, j, dp);
        int res2 = solve (s1, s2, i, j+1, dp);
        return dp[i][j] = max(res1, res2);
    }
}


int longestCommonSubsequence(string text1, string text2) {
    vector<vector<int>> dp (text1.size()+1, vector<int> (text2.size()+1, -1));
    return solve (text1, text2, 0, 0, dp);
}
```

**Method 2 : Tabulation (Bottom-Up)**

```cpp
int longestCommonSubsequence(string s1, string s2) {

    /* Initialize first row and col with 0 */

    vector<vector<int>> dp (s1.length()+1, vector<int> (s2.length()+1, 0));

    for(int i=1; i<=s1.length(); i++){
        for(int j=1; j<=s2.length(); j++){

            if(s1[i-1]==s2[j-1])
                dp[i][j] = 1 + dp[i-1][j-1];                    // -- 1 + top-left diagonal

            else
                dp[i][j] = max (dp[i-1][j], dp[i][j-1]);        // --> max (top, left)
        }
    }

    return dp[s1.length()][s2.length()];
}
```

### 1. Print Longest Common Subsequence
Given two sequences, print the longest subsequence present in both of them. LCS for input Sequences “AGGTAB” and “GXTXAYB” is “GTAB” of length 4.

```cpp
string printLCS (string & s1, string & s2){
    int n = s1.length(), m = s2.length();
    vector<vector<int>> dp (n+1, vector<int> (m+1, 0));

    for(int i=1; i<=n; i++){
        for(int j=1; j<=m; j++){
            if(s1[i-1]==s2[j-1])
                dp[i][j] = 1 + dp[i-1][j-1];
            else
                dp[i][j] = max (dp[i-1][j], dp[i][j-1]);
        }
    }

    /* --------------------- Printing Logic ----------------------- */

    int i = n, j = m;
    string lcs = "";

    while(i>0 and j>0){

        if(s1[i-1]==s2[j-1]){
            lcs.push_back(s1[i-1]);
            i--; j--;
        }
        else{
            if(dp[i-1][j] > dp[i][j-1]) i--;
            else j--;
        }
    }

    reverse(lcs.begin(), lcs.end());
    return lcs;
}
```

### 2. Shortest Common Supersequence
Given two strings s1 and s2 of lengths m and n respectively, find the length of the smallest string which has both, s1 and s2 as its sub-sequences.

Input: s1 = abcd, s2 = xycd

Output: 6

Hint: len(supersequence) = len(s1) + len(s2) - lcs_len

```cpp
int lcs (string & s1, string & s2, int m, int n){
    vector<vector<int>> dp (m+1, vector<int> (n+1, 0));

    for(int i=1; i<=m; i++){
        for(int j=1; j<=n; j++){

            if(s1[i-1]==s2[j-1])
                dp[i][j] = 1 + dp[i-1][j-1];
            else
                dp[i][j] = max (dp[i-1][j], dp[i][j-1]);
        }
    }

    return dp[m][n];
}

int shortestCommonSupersequence(string s1, string s2, int m, int n){
    int lcs_len = lcs (s1, s2, m, n);
    return m+n-lcs_len;
}
```

### 3. Print Shortest Common Supersequence
Given two strings s1 and s2, return the shortest string that has both str1 and str2 as subsequences.  If multiple answers exist, you may return any of them.

Input: str1 = "abac", str2 = "cab"

Output: "cabac"

```cpp
string shortestCommonSupersequence(string s1, string s2) {
    int n = s1.size(), m = s2.size();
    vector<vector<int>> dp (n+1, vector<int> (m+1, 0));

    for(int i=1; i<=n; i++){
        for(int j=1; j<=m; j++){

            if(s1[i-1]==s2[j-1])
                dp[i][j] = 1 + dp[i-1][j-1];
            else
                dp[i][j] = max (dp[i-1][j], dp[i][j-1]);
        }
    }

    /* ---------------- Print Supersequence Logic ----------------- */

    int i = n, j = m;
    string supersequence = "";

    while(i>0 and j>0){

        /* If both char are equal, push it in string one time and move to top-left diagonal cell */

        if(s1[i-1]==s2[j-1]){
            supersequence.push_back(s1[i-1]);
            i--; j--;
        }

        /* Move towards left or up whichever is max, and push the char of the row or col which we are leaving */

        else{

            if(dp[i-1][j] > dp[i][j-1]){
                supersequence.push_back(s1[i-1]);
                i--;
            }
            else{
                supersequence.push_back(s2[j-1]);
                j--;
            }
        }
    }

    /* Don't forget to pushback remaining chars of s1 and s2 */

    while(i>0){
        supersequence.push_back(s1[i-1]);
        i--;
    }

    while(j>0){
        supersequence.push_back(s2[j-1]);
        j--;
    }

    reverse(supersequence.begin(), supersequence.end());
    return supersequence;
}
```

### 4. Minimum number of deletions and insertions to transform one string into another
Given two strings ‘s1’ and ‘s2’ of size m and n respectively. The task is to remove and insert the minimum number of characters from s1 to transform it into s2. It could be possible that the same character needs to be removed from one point of s1 and inserted to some another point.

![img](https://media.geeksforgeeks.org/wp-content/uploads/20200817135845/picture2-660x402.jpg)

Hint: Total operations = len(s1) + len(s2) - (2*lcs_len)

```cpp
int lcs (string & s1, string & s2, int m, int n){
    vector<vector<int>> dp (m+1, vector<int> (n+1, 0));

    for(int i=1; i<=m; i++){
        for(int j=1; j<=n; j++){

            if(s1[i-1]==s2[j-1])
                dp[i][j] = 1 + dp[i-1][j-1];
            else
                dp[i][j] = max (dp[i-1][j], dp[i][j-1]);
        }
    }

    return dp[m][n];
}

int minOperations(string s1, string s2) { 
    int m = s1.length(), n = s2.length();
    int lcs_len = lcs(s1, s2, m, n);

    return m+n-(2*lcs_len);
} 
```

### 5. Longest Palindromic Subsequence
Given a string s, find the longest palindromic subsequence's length in s.

Input: s = "bbbab"

Output: 4

Hint: Just find LCS of s and reverse(s)

```cpp
int lcs (string & s1, string & s2, int n){
    vector<vector<int>> dp (n+1, vector<int> (n+1, 0));

    for(int i=1; i<=n; i++){
        for(int j=1; j<=n; j++){

            if(s1[i-1]==s2[j-1])
                dp[i][j] = 1 + dp[i-1][j-1];
            else
                dp[i][j] = max (dp[i-1][j], dp[i][j-1]);
        }
    }

    return dp[n][n];
}

int longestPalindromeSubseq(string s) {
    int n = s.length();
    string rev = s;
    reverse(rev.begin(), rev.end());

    return lcs (s, rev, n);
}
```

### 6. Minimum Insertion Steps to Make a String Palindrome (Same as Min Deletion steps)
Given a string s. In one step you can insert any character at any index of the string. Return the minimum number of steps to make s palindrome.

Input: s = "mbadm"

Output: 2

Hint: Min Insertion == Min Deletion == len(s) - len(longest palindromic subsequence)

```cpp
int lcs (string & s1, string & s2, int n){
    vector<vector<int>> dp (n+1, vector<int> (n+1, 0));

    for(int i=1; i<=n; i++){
        for(int j=1; j<=n; j++){

            if(s1[i-1]==s2[j-1])
                dp[i][j] = 1 + dp[i-1][j-1];
            else
                dp[i][j] = max (dp[i-1][j], dp[i][j-1]);
        }
    }

    return dp[n][n];
}

int minInsertions(string s) {
    int n = s.length();
    string rev = s;
    reverse(rev.begin(), rev.end());

    int lps_len = lcs (s, rev, n);
    return s.length() - lps_len;
}
```

### 7. Longest Repeating Subsequence
Given a string, find the length of the longest repeating subsequence such that the two subsequences don’t have same string character at the same position, i.e., any i’th character in the two subsequences shouldn’t have the same index in the original string. 

Input: str = "aabb"

Output: 2

Hint: Find LCS(s,s) with one additional condition such that s[i-1]==s[j-1] and i!=j

```cpp
int LongestRepeatingSubsequence(string s){
    int n = s.length();
    vector<vector<int>> dp (n+1, vector<int> (n+1, 0));

    for(int i=1; i<=n; i++){
        for(int j=1; j<=n; j++){

            if(s[i-1]==s[j-1] and i!=j)
                dp[i][j] = 1 + dp[i-1][j-1];
            else
                dp[i][j] = max (dp[i-1][j], dp[i][j-1]);

        }
    }

    return dp[n][n];
}
```

### 8. Minimum ASCII Delete Sum for Two Strings
Given two strings s1 and s2, return the lowest ASCII sum of deleted characters to make two strings equal.

Input: s1 = "sea", s2 = "eat"

Output: 231

Hint: use memorized lcs with little variation, instead of adding 1 for length, add ascii val of curr char.

```cpp
int solve (string & s1, string & s2, int i, int j, vector<vector<int>> & dp){
    if(i==s1.length() or j==s2.length()) return 0;

    if(dp[i][j]!=-1) return dp[i][j];

    if(s1[i]==s2[j]){
        int res = s1[i] + solve(s1, s2, i+1, j+1, dp); 
        return dp[i][j] = res;
    }
    else{
        int res1 = solve (s1, s2, i+1, j, dp);
        int res2 = solve (s1, s2, i, j+1, dp);

        return dp[i][j] = max(res1, res2);
    }

}


int minimumDeleteSum(string s1, string s2) {
    int totalSum = 0;
    vector<vector<int>> dp (s1.length()+1, vector<int> (s2.length(), -1));

    for(char ch : s1)
        totalSum += ch;

    for(char ch : s2)
        totalSum += ch;

    int max_lcs_sum = solve (s1, s2, 0, 0, dp);

    return totalSum - (2*max_lcs_sum);
}
```

### 9. Uncrossed Lines (Tricky)
We write the integers of nums1 and nums2 (in the order they are given) on two separate horizontal lines. Now, we may draw connecting lines: a straight line connecting two numbers nums1[i] and nums2[j] such that:

1. nums1[i] == nums2[j];
2. The line we draw does not intersect any other connecting (non-horizontal) line.

Note that a connecting lines cannot intersect even at the endpoints: each number can only belong to one connecting line. Return the maximum number of connecting lines we can draw in this way.

![img](https://assets.leetcode.com/uploads/2019/04/26/142.png)

Hint: Just return len of lcs of both arrays

```cpp
int lcs (vector<int> & nums1, vector<int> & nums2){
    int m = nums1.size(), n = nums2.size();
    vector<vector<int>> dp (m+1 , vector<int> (n+1, 0));

    for(int i=1; i<=m; i++){
        for(int j=1; j<=n; j++){

            if(nums1[i-1]==nums2[j-1])
                dp[i][j] = 1 + dp[i-1][j-1];
            else
                dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
        }
    }

    return dp[m][n];
}


int maxUncrossedLines(vector<int>& nums1, vector<int>& nums2) {
    return lcs (nums1, nums2);
}
```

### 10. Interleaving String
Given strings s1, s2, and s3, find whether s3 is formed by an interleaving of s1 and s2.

![img](https://assets.leetcode.com/uploads/2020/09/02/interleave.jpg)

Input: s1 = "aabcc", s2 = "dbbca", s3 = "aadbbcbcac"

Output: true

```cpp
bool solve (string & s1, string & s2, string & s3, int i, int j, int k, vector<vector<int>> & dp){
    if(k==s3.length()) return true;

    if(dp[i][j]!=-1) return dp[i][j];

    bool res1 = false, res2 = false;

    if(i<s1.length() and s1[i]==s3[k]){
        res1 = solve(s1, s2, s3, i+1, j, k+1, dp);

        if(res1) 
            return dp[i][j] = true;
    }


    if(j<s2.length() and s2[j]==s3[k]){
        res2 = solve(s1, s2, s3, i, j+1, k+1, dp);

        if(res2) 
            return dp[i][j] = res2;
    }

    return dp[i][j] = false;
}


bool isInterleave(string s1, string s2, string s3) {
    if(s3.length()!=s1.length()+s2.length())
        return false;

    vector<vector<int>> dp (s1.length()+1, vector<int> (s2.length()+1, -1));
    return solve(s1, s2, s3, 0, 0, 0, dp);
}
```

### 11. Distinct Subsequences
Given two strings s and t, return the number of distinct subsequences of s which equals t.

Input: s = "rabbbit", t = "rabbit"

Output: 3

Explanation: As shown below, there are 3 ways you can generate "rabbit" from S.

rabbbit, rabbbit, rabbbit

```cpp
int solve (string & s, string & t, int i, int j, vector<vector<int>> & dp){
    if(j==t.length()) return 1;
    if(i==s.length()) return 0;

    if(dp[i][j]!=-1) return dp[i][j];

    /* If curr char of both strings are equal then we have two cases */

    if(s[i]==t[j]){    
        int res1 = solve (s, t, i+1, j+1, dp);          // --> Either consider it to be in subsequence
        int res2 = solve (s, t, i+1, j, dp);            // --> Else do not consider it to be in subsequence

        return dp[i][j] = res1 + res2;
    }

    /* Else we can't consider that char and recurse for rest part of the s */

    else{
        int res = solve (s, t, i+1, j, dp);
        return dp[i][j] = res;
    }
}


int numDistinct(string s, string t) {
    int slen = s.length(), tlen = t.length();
    vector<vector<int>> dp (slen, vector<int> (tlen, -1));
    return solve (s, t, 0, 0, dp);
}
```


### 12. Edit Distance
Given two strings word1 and word2, return the minimum number of operations required to convert word1 to word2. You have the following three operations permitted on a word:
1. Insert a character
2. Delete a character
3. Replace a character

Input: word1 = "intention", word2 = "execution"

Output: 5

**Approach 1 : Using Memorization**

```cpp
int solve (string & s1, string & s2, int i, int j, vector<vector<int>> & dp){

    /* 
        Base Cases:
        when one string gets empty, we need to insert all remaining char of second string
        hence simply return that char count
    */

    if(i==s1.length()) return s2.length()-j;

    if(j==s2.length()) return s1.length()-i;

    if(dp[i][j]!=-1) return dp[i][j];

    /* Now if curr char of both strings are equal rescurse for next part on both strings */

    if(s1[i]==s2[j]){
        int res = solve (s1, s2, i+1, j+1, dp);
        return dp[i][j] = res;
    }

    /* Else we have following 3 choices */

    else{

        int res1 = solve (s1, s2, i, j+1, dp);          // --> case 1 : Insert
        int res2 = solve (s1, s2, i+1, j, dp) ;         // --> case 2 : Delete
        int res3 = solve (s1, s2, i+1, j+1, dp);        // --> case 3 : Replace

        return dp[i][j] = min({res1, res2, res3}) + 1;
    }
}


int minDistance(string word1, string word2) {
    int len1 = word1.length(), len2 = word2.length();
    vector<vector<int>> dp (len1, vector<int> (len2, -1));

    return solve (word1, word2, 0, 0, dp);
}
```

**Approach 2 : Bottom Up (Tabulation)**

```cpp
int minDistance(string word1, string word2) {
    int len1 = word1.length(), len2 = word2.length();
    vector<vector<int>> dp (len1+1, vector<int> (len2+1, 0));

    for(int i=0; i<=len1; i++){
        for(int j=0; j<=len2; j++){

            if(i==0)
                dp[i][j] = j;

            else if(j==0)
                dp[i][j] = i;

            else if (word1[i-1]==word2[j-1])
                dp[i][j] = dp[i-1][j-1];

            else
                dp[i][j] = min({dp[i-1][j], dp[i][j-1], dp[i-1][j-1]}) + 1;
        }
    } 

    return dp[len1][len2];
}
```
