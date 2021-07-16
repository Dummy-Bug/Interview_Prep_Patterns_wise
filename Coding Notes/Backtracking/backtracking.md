# Recursion & Backtracking 

## 1D Problems

#### 1. Letter Case Permutation
Given a string s, we can transform every letter individually to be lowercase or uppercase to create another string. Return a list of all possible strings we could create.

Input: s = "a1b2"

Output: ["a1b2","a1B2","A1b2","A1B2"]

```cpp
void solve (string & ip, string op, int idx, int n, vector<string> & res){
    if(idx==n){
        res.push_back(op);
        return;
    }

    if(isdigit(ip[idx]))
        solve(ip, op, idx+1, n, res);

    else{   
        op[idx] = tolower(op[idx]);
        solve (ip, op, idx+1, n, res);

        op[idx] = toupper(op[idx]);
        solve (ip, op, idx+1, n, res);
    }
}

vector<string> letterCasePermutation(string s) {
    int n = s.length();
    vector<string> res;

    solve (s, s, 0, n, res);
    return res;
}
```

#### 2. Permutations
Given an array nums of distinct integers, return all the possible permutations. You can return the answer in any order.

Input: nums = [1,2,3]

Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]

```cpp
void solve (vector<int> & nums, int idx, vector<vector<int>> & res, vector<int> & v, vector<bool> & vis){
    if(idx==nums.size()){
        res.push_back(v);
        return;
    }
    
    /* we can fill any number at index idx which is not visited yet */  
    
    for(int i=0; i<nums.size(); i++){
        if(!vis[i]){
            v[idx] = nums[i];
            vis[i] = true;

            solve (nums, idx+1, res, v, vis);

            vis[i] = false;
        }
    }
}

vector<vector<int>> permute(vector<int>& nums) {
    vector<vector<int>> res;
    vector<int> v (nums.size(), 0);
    vector<bool> vis (nums.size(), false);

    solve(nums, 0, res, v, vis);
    return res;
}
```

#### 3. Subsets
Given an integer array nums of unique elements, return all possible subsets (the power set). The solution set must not contain duplicate subsets.

Input: nums = [1,2,3]

Output: [[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]

```cpp
/* we need to choose whether we include element of that pos or not */

void solve (vector<int> & nums, vector<vector<int>> & res, int idx, vector<int> v){
    if(idx==nums.size()){
        res.push_back(v);
        return;
    }

    /* vector without include element at idx*/

    solve (nums, res, idx+1, v);

    /* vector with include element at idx */

    v.push_back(nums[idx]);
    solve (nums, res, idx+1, v);
}

vector<vector<int>> subsets(vector<int>& nums) {
    vector<vector<int>> res;
    vector<int> v;

    solve(nums, res, 0, v);
    return res;
}
```

#### 4. Letter Tile Possibilities
You have n  tiles, where each tile has one letter tiles[i] printed on it. Return the number of possible non-empty sequences of letters you can make using the letters printed on those tiles.

Input: tiles = "AAB",  Output: 8

Explanation: The possible sequences are "A", "B", "AA", "AB", "BA", "AAB", "ABA", "BAA".

```cpp
void solve (vector<int> & freq, int idx, int n, unordered_set<string> & vis, string & s, int & count){
    if(idx==n) return; 

    /* At every pos we have 26 options (A-Z) to fill */

    for(int i=0; i<26; i++){

        /* If freq > 0 that means we can fill it */

        if(freq[i]>0){   

            /* Decrement its freq and push that char in string */

            freq[i]--;
            s.push_back('A'+i);

            /* If this string s is not vis before, mark it vis and increament the count */ 

            if(vis.find(s)==vis.end()){
                vis.insert(s);
                count++;
            }

            /* and simply recurse */

            solve(freq, idx+1, n, vis, s, count);

            /* after coming back, redo the changes */

            s.pop_back();
            freq[i]++;
        }
    }
}

int numTilePossibilities(string tiles) {
    int n = tiles.size();

    vector<int> freq (26, 0);          // --> for storing freq of all chars
    unordered_set<string> vis;         // --> for keeping track of visited strings

    string s = "";
    int count = 0;

    for(char ch : tiles)
        freq[ch-'A']++;

    solve (freq, 0, n, vis, s, count);
    return count;
}
```

#### 5. Generate Parentheses
Given n pairs of parentheses, write a function to generate all combinations of well-formed parentheses.

Input: n = 3

Output: ["((()))","(()())","(())()","()(())","()()()"]

```cpp
void solve (int n, int lc, int rc, string s, vector<string> & res){
    if(lc==n and rc==n){
        res.push_back(s);
        return;
    }

    /* If left bracket count is greater then right bracket count, we can fill both the brackets */

    if(lc>rc){
        if(lc<n) solve(n, lc+1, rc, s+"(", res);
        solve(n, lc, rc+1, s+")", res);
    }

    /* Else we can only fill left bracket */

    else 
        solve(n, lc+1, rc, s+"(", res);
}

vector<string> generateParenthesis(int n) {
    vector<string> res;
    solve(n, 0, 0, "", res);
    return res;
}
```

#### 6. Combination Sum III
Find all valid combinations of k numbers that sum up to n such that the following conditions are true:
1. Only numbers 1 through 9 are used.
2. Each number is used at most once.

Return a list of all possible valid combinations. The list must not contain the same combination twice, and the combinations may be returned in any order.

Input: k = 3, n = 9

Output: [[1,2,6],[1,3,5],[2,3,4]]

```cpp
int getSum (vector<int> & v){
    int sum = 0;

    for(int i : v)
        sum += i;

    return sum;
}


void solve (int k, int target, int idx, vector<vector<int>> & res, vector<int> & v){
    if(v.size()==k){
        if(getSum(v)==target)
            res.push_back(v);
        return;
    }

    /* we can choose any number for index i in vector v in range between [idx-9] where idx is one greater then the prev number */ 

    for(int i=idx; i<=9; i++){
        v.push_back(i);
        solve (k, target, i+1, res, v);
        v.pop_back();
    }
}


vector<vector<int>> combinationSum3(int k, int n) {
    vector<vector<int>> res;
    vector<int> v;

    solve(k, n, 1, res, v);
    return res;
}
```


## 2D Problems

#### 1. Path with Maximum Gold
In a gold mine grid of size m x n, each cell in this mine has an integer representing the amount of gold in that cell, 0 if it is empty. Return the maximum amount of gold you can collect under the conditions:

1. Every time you are located in a cell you will collect all the gold in that cell.
2. From your position, you can walk one step to the left, right, up, or down.
3. You can't visit the same cell more than once.
4. Never visit a cell with 0 gold.
5. You can start and stop collecting gold from any position in the grid that has some gold.

```cpp
bool isInside(int x, int y, int row, int col){
    return x>=0 and y>=0 and x<row and y<col;
}


int backtrack (vector<vector<int>>& grid, int row, int col, int x, int y, vector<vector<bool>> & curr_path){
    int gold = 0;
    curr_path[x][y] = true; 

    int dx[] = {1, 0, -1, 0};
    int dy[] = {0, 1, 0, -1};

    for(int i=0; i<4; i++){
        int xnew = x + dx[i];
        int ynew = y + dy[i];

        if(isInside(xnew, ynew, row, col) and grid[xnew][ynew]!=0 and !curr_path[xnew][ynew])
            gold = max(gold, backtrack(grid, row, col, xnew, ynew, curr_path));
    }

    gold += grid[x][y];

    curr_path[x][y] = false;
    return gold;
}


int getMaximumGold(vector<vector<int>>& grid) {
    int row = grid.size(), col = grid[0].size();
    vector<vector<bool>> curr_path (row, vector<bool> (col, false));
    int max_gold = 0;

    for(int i=0; i<row; i++){
        for(int j=0; j<col; j++){

            if(grid[i][j]!=0)
                max_gold = max(max_gold, backtrack(grid, row, col, i, j, curr_path));
        }
    }

    return max_gold;
}
```
