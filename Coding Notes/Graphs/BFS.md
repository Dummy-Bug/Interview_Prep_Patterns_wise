# BFS

## @ BFS on adjacency list

#### 1. Keys and Rooms
There are N rooms and you start in room 0. A key rooms[i][j] = v opens the room with number v. Return true if and only if you can enter every room.

Input: [[1],[2],[3],[]]

Output: true

```cpp
bool canVisitAllRooms(vector<vector<int>>& rooms) {
    int n = rooms.size();
    vector<bool> vis (n, false);
    queue<int> q;
    int count = 1;

    q.push(0);
    vis[0] = true;

    while(!q.empty()){
        int key = q.front(); q.pop();
        for(int nbr : rooms[key]){
            if(!vis[nbr]){
                q.push(nbr);
                count++;
                vis[nbr] = true;
            }
        }
    }
    return count==n;
}
```

#### 2. Time Needed to Inform All Employees
A company has n employees with a unique ID for each employee from 0 to n - 1. The head of the company is the one with headID.

Each employee has one direct manager given in the manager array, manager[headID] = -1. Also, it is guaranteed that the subordination relationships have a tree structure.

The head of the company wants to inform all the company employees of an urgent piece of news. He will inform his direct subordinates, and they will inform their subordinates, and so on until all employees know about the urgent news.

The i-th employee needs informTime[i] minutes to inform all of his direct subordinates (i.e., After informTime[i] minutes, all his direct subordinates can start spreading the news). Return the number of minutes needed to inform all the employees about the urgent news.

Input: n = 4, headID = 2, manager = [3,3,-1,2], informTime = [0,0,162,914]

Output: 1076

```cpp
int numOfMinutes(int n, int headID, vector<int>& manager, vector<int>& informTime) {
    unordered_map<int, vector<int>> graph;
    int time = informTime[headID];

    for(int i=0; i<n; i++)
        graph[manager[i]].push_back(i);

    queue<pair<int,int>> q;
    q.push({headID, time});

    while(!q.empty()){
        auto emp = q.front(); q.pop();
        int id = emp.first, t = emp.second;

        for(int nbr : graph[id]){
            q.push({nbr, t+informTime[nbr]});
            time = max(time, t+informTime[nbr]);
        }
    }

    return time;
}
```

## @ BFS on matrix

#### 1. Flood Fill
You are given an image and three integers sr, sc, and newColor. You should perform a flood fill on the image starting from the pixel image[sr][sc]. To perform a flood fill, consider the starting pixel, plus any pixels connected 4-directionally to the starting pixel of the same color as the starting pixel, plus any pixels connected 4-directionally to those pixels (also with the same color), and so on. Replace the color of all of the aforementioned pixels with newColor.

![img](https://assets.leetcode.com/uploads/2021/06/01/flood1-grid.jpg)

```cpp
bool isInside(int x, int y, int row, int col){
    return x>=0 and y>=0 and x<row and y<col;
}

vector<vector<int>> floodFill(vector<vector<int>>& image, int sr, int sc, int newColor) {
    queue<pair<int,int>> q;
    int row = image.size(), col = image[0].size();
    vector<vector<bool>> vis (row, vector<bool>(col, false));
    int color = image[sr][sc];

    int dx[] = {1, 0, -1, 0};
    int dy[] = {0, 1, 0, -1};

    q.push({sr, sc});
    image[sr][sc] = newColor;
    vis[sr][sc] = true;

    while(!q.empty()){
        auto p = q.front(); q.pop();

        for(int i=0; i<4; i++){
            int x = p.first + dx[i];
            int y = p.second + dy[i];

            if(isInside(x, y, row, col) and !vis[x][y] and image[x][y]==color){
                q.push({x,y});
                image[x][y] = newColor;
                vis[x][y] = true;
            }
        }
    }
    return image;
}
```

#### 2. Rotting Oranges
You are given an m x n grid where each cell can have one of three values:

1. 0 representing an empty cell,
2. 1 representing a fresh orange, or
3. 2 representing a rotten orange.

Every minute, any fresh orange that is 4-directionally adjacent to a rotten orange becomes rotten. Return the minimum number of minutes that must elapse until no cell has a fresh orange. If this is impossible, return -1.

Hint: Use some delimeter to keep track of ending of one level. Total time to rott oranges will be equal to number of bfs levels.

```cpp
bool isInside(int x, int y, int row, int col){
    return x>=0 and y>=0 and x<row and y<col;
}

int bfs (vector<vector<int>>& grid, queue<pair<int,int>> & q, int row, int col, int & rotten_oranges){
    int time = 0;

    int dx[] = {1, 0, -1, 0};
    int dy[] = {0, 1, 0, -1};

    while(!q.empty()){
        auto cell = q.front(); q.pop();
        int x = cell.first, y = cell.second;

        if(x==-1){
            if(q.empty()) return time;

            q.push({-1, -1});
            time++;
            continue;
        }

        rotten_oranges++;         // It will increment count of total rotten oranges

        for(int i=0; i<4; i++){
            int xnew = x + dx[i];
            int ynew = y + dy[i];

            if(isInside(xnew, ynew, row, col) and grid[xnew][ynew]==1){
                q.push({xnew, ynew});
                grid[xnew][ynew] = 2;
            }
        }

    }

    return time;
}

int orangesRotting(vector<vector<int>>& grid) {
    int row = grid.size(), col = grid[0].size();
    queue<pair<int, int>> q;

    int total_oranges = 0;
    int rotten_oranges = 0;

    for(int i=0; i<row; i++){
        for(int j=0; j<col; j++){

            if(grid[i][j]==1)
                total_oranges++;

            else if(grid[i][j]==2){
                q.push({i,j});
                total_oranges++;
            }

        }
    }

    q.push({-1, -1});      // ----> will act as a ending delimeter for bfs levels

    int time = bfs(grid, q, row, col, rotten_oranges);

    if(total_oranges==rotten_oranges) return time;        // ----> Don't forget to check this condition
    else return -1;
}
```

#### 3. 01 Matrix
Given an m x n binary matrix mat, return the distance of the nearest 0 for each cell. The distance between two adjacent cells is 1.

![img](https://assets.leetcode.com/uploads/2021/04/24/01-2-grid.jpg)

Output: [[0,0,0],[0,1,0],[1,2,1]]

Hint: Think somewhat similar to rotting oranges (parallel bfs)

```cpp
bool isInside(int x, int y, int row, int col){
    return x>=0 and y>=0 and x<row and y<col;
}

void bfs (vector<vector<int>>& mat, queue<pair<int,int>> & q, int row, int col){
    int dx[] = {1, 0, -1, 0};
    int dy[] = {0, 1, 0, -1};

    while(!q.empty()){
        auto cell = q.front(); q.pop();
        int x = cell.first, y = cell.second;

        for(int i=0; i<4; i++){
            int xnew = x + dx[i];
            int ynew = y + dy[i];

            /* If nbr is valid and path through parent is more shorter then we will update nbr and push it in the queue */

            if(isInside(xnew, ynew, row, col) and mat[x][y]+1 < mat[xnew][ynew]){
                mat[xnew][ynew] = mat[x][y]+1;
                q.push({xnew, ynew});
            }
        }
    }
}

vector<vector<int>> updateMatrix(vector<vector<int>>& mat) {
    int row = mat.size(), col = mat[0].size();
    queue<pair<int,int>> q;

    /* 
        Same matrix will be used to store shortest distances
        First add all cells with 0 to the queue and set cells with 1 to INT_MAX 
    */

    for(int i=0; i<row; i++){
        for(int j=0; j<col; j++){

            if(mat[i][j]==0)
                q.push({i,j});     // --> They will be the starting point for parallel bfs

            else
                mat[i][j]=INT_MAX;
        }
    }

    bfs (mat, q, row, col);
    return mat;
}
```

#### 4. Shortest Path in Binary Matrix
Given an n x n binary matrix grid, return the length of the shortest clear path in the matrix. If there is no clear path, return -1. A clear path is a path from the top-left cell (i.e., (0, 0)) to the bottom-right cell (i.e., (n - 1, n - 1)) such that: All the visited cells of the path are 0. All the adjacent cells of the path are 8-directionally connected.

![img](https://assets.leetcode.com/uploads/2021/02/18/example2_1.png)

```cpp
bool isInside(int x, int y, int row, int col){
    return x>=0 and y>=0 and x<row and y<col;
}

int bfs (vector<vector<int>>& grid, int row, int col){
    int distance = 1;

    int dx[] = {1, -1, 1, -1, 1, 0, -1, 0};
    int dy[] = {1, 1, -1, -1, 0, 1, 0, -1};

    queue<pair<int,int>> q;
    q.push({0,0});
    q.push({-1, -1});

    grid[0][0] = 2;         // ----> marked as vis

    while (!q.empty()){
        auto p = q.front(); q.pop();
        int x = p.first, y = p.second;

        if(x==row-1 and y==col-1) return distance;

        if(x==-1){
            if(q.empty()) break;

            q.push({-1, -1});
            distance++;
            continue;
        }

        for(int i=0; i<8; i++){
            int xnew = x + dx[i];
            int ynew = y + dy[i];

            if(isInside(xnew, ynew, row, col) and grid[xnew][ynew]==0){
                q.push({xnew, ynew});
                grid[xnew][ynew] = 2;
            }
        }
    }

    return -1;
}

int shortestPathBinaryMatrix(vector<vector<int>>& grid) {
    int row = grid.size(), col = grid[0].size();
    if(grid[0][0]!=0) return -1;

    return bfs (grid, row, col);
}
```

## @ Hard to Guess as Graphs

#### 1. Open the Lock (Too Tricky)
You have a lock in front of you with 4 circular wheels. Each wheel has 10 slots: '0', '1', '2', '3', '4', '5', '6', '7', '8', '9'. The wheels can rotate freely and wrap around: for example we can turn '9' to be '0', or '0' to be '9'. Each move consists of turning one wheel one slot. The lock initially starts at '0000'.

You are given a list of deadends dead ends, meaning if the lock displays any of these codes, the wheels of the lock will stop turning and you will be unable to open it. Given a target representing the value of the wheels that will unlock the lock, return the minimum total number of turns required to open the lock, or -1 if it is impossible.

Input: deadends = ["0201","0101","0102","1212","2002"], target = "0202"

Output: 6

Hint: Find all 8 nbrs of current state (4 wheels * 2 directional moves i.e. scroll_up and scroll_down). Apply bfs on string by pushing all nbrs to queue as we do in other questions. Check for deadends before pushing any nbrs to queue and use unordered_set to keep track of already visited strings.

[Video Explaination](https://www.youtube.com/watch?v=vtxETRvR9JY)

```cpp
vector<string> get_nbrs (string s){
    vector<string> nbrs;

    for(int i=0; i<4; i++){
        int digit = s[i]-'0';
        int digit_after_rotate_up = (digit+1)%10;
        int digit_after_rotate_down = (digit + 10 - 1)%10;

        string s1 = s, s2 = s;
        s1[i] = digit_after_rotate_up + '0';
        s2[i] = digit_after_rotate_down + '0';

        nbrs.push_back(s1);
        nbrs.push_back(s2);
    }

    return nbrs;
}

int bfs (unordered_set<string> & deadends, string src, string target){
    int moves = 0;
    queue<string> q;
    unordered_set<string> vis;

    q.push(src);
    q.push("#");              // ----> It will act as ending delimeter for one bfs level    
    vis.insert(src);

    while(!q.empty()){
        string s = q.front(); q.pop();

        if(s==target) return moves;

        else if(s=="#"){
            if(q.empty()) break;

            q.push("#");
            moves++;
        }

        else{
            auto nbrs = get_nbrs(s);
            for(auto nbr : nbrs){
                if(vis.find(nbr)==vis.end() and deadends.find(nbr)==deadends.end()){
                    q.push(nbr);
                    vis.insert(nbr);
                }
            }
        }
    }

    return -1;
}

int openLock(vector<string>& deadends, string target) {
    unordered_set<string> dead_ends;

    for(string s : deadends)
        dead_ends.insert(s);

    if(dead_ends.find("0000")!=dead_ends.end()) return -1;

    return bfs(dead_ends, "0000", target);
}
```

#### 2. Minimum Genetic Mutation
A gene string can be represented by an 8-character long string, with choices from 'A', 'C', 'G', and 'T'. Suppose we need to investigate a mutation where one mutation is defined as one single character changed in the gene string.

For example, "AACCGGTT" --> "AACCGGTA" is one mutation.
There is also a gene bank that records all the valid gene mutations. A gene must be in bank to make it a valid gene string. Note that the starting point is assumed to be valid, so it might not be included in the bank.

Given the two gene strings start and end and the gene bank bank, return the minimum number of mutations needed to mutate from start to end. If there is no such a mutation, return -1.

Input: start = "AACCGGTT", end = "AAACGGTA", bank = ["AACCGGTA","AACCGCTA","AAACGGTA"]

Output: 2

Hint: Similar to Open the Lock Problem

```cpp
vector<string> get_nbrs(string s, string end){
    vector<string> nbrs;
    char choice[] = {'A', 'C', 'G', 'T'};

    for(int i=0; i<8; i++){            
        for(int c=0; c<4; c++){
            if(s[i]!=choice[c]){
                string temp = s;
                temp[i] = choice[c];
                nbrs.push_back(temp);
            }
        }
    }

    return nbrs;
}

/* here, gene bank will also used to mark any string visited. Just erase that string from the bank */

int bfs (string start, string end, unordered_set<string> & gene_bank){
    int mutation = 0;

    queue<string> q;
    q.push(start);
    q.push("#");              

    while(!q.empty()){
        string s = q.front(); q.pop();

        if(s==end) return mutation;

        if(s=="#"){
            if(q.empty()) break;

            q.push("#");
            mutation++;
            continue;
        }

        vector<string> nbrs = get_nbrs(s, end);

        for(string nbr : nbrs){
            if(gene_bank.find(nbr)!=gene_bank.end()){
                q.push(nbr);
                gene_bank.erase(nbr);            // ----> mark that nbr vis by erasing it from bank
            }
        }
    }

    return -1;
}

int minMutation(string start, string end, vector<string>& bank) {
   unordered_set<string> gene_bank;
    for(string s : bank)
        gene_bank.insert(s);

    return bfs (start, end, gene_bank);
}
```

#### 3. K-Similar Strings
Strings s1 and s2 are k-similar (for some non-negative integer k) if we can swap the positions of two letters in s1 exactly k times so that the resulting string equals s2. Given two anagrams s1 and s2, return the smallest k for which s1 and s2 are k-similar.

Input: s1 = "abac", s2 = "baca"

Output: 2

```cpp
/* Find nbrs by swapping posn (with desired chars) */

vector<string> get_nbrs(string s, string s2, int pos){
    vector<string> nbrs;
    char ch = s2[pos];      // ----> desired char

    for(int i=pos+1; i<s.length(); i++){

        if(s[i]==ch){
            string temp = s;
            swap(temp[pos], temp[i]);
            nbrs.push_back(temp);
        }
    }

    return nbrs;
}

int bfs (string s1, string s2){
    int swaps = 0;         // ----> Number of swaps required
    int pos = 0;           // ----> Position on which we are working currently

    unordered_set<string> vis;
    queue<pair<string, int>> q;

    q.push({s1, pos});
    q.push({"#", pos});
    vis.insert(s1);

    while(!q.empty()){
        auto src = q.front(); q.pop();
        string s = src.first; int pos = src.second;

        if(s==s2) return swaps;

        if(s=="#"){
            if(q.empty()) break;

            q.push({"#", pos});
            swaps++;
            continue;
        }

        while(s[pos]==s2[pos]) pos++;

        vector<string> nbrs = get_nbrs (s, s2, pos);

        for(string nbr : nbrs){
            if(vis.find(nbr)==vis.end()){                    
                q.push({nbr, pos+1});
                vis.insert(nbr);
            }
        }
    }

    return -1;
}

int kSimilarity(string s1, string s2) {
    int n = s1.size();
    return bfs (s1, s2);
}
```

#### 4. Word Ladder
A transformation sequence from word beginWord to word endWord using a dictionary wordList is a sequence of words beginWord -> s1 -> s2 -> ... -> sk such that:

1. Every adjacent pair of words differs by a single letter.
2. Every si for 1 <= i <= k is in wordList. Note that beginWord does not need to be in wordList.
3. sk == endWord

Given two words, beginWord and endWord, and a dictionary wordList, return the number of words in the shortest transformation sequence from beginWord to endWord, or 0 if no such sequence exists.

Input: beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log","cog"]

Output: 5

```cpp
/* 
    Replace char at every index with all 25 other possible lowercase char 
    and check if that new_word is present in set wordList
    then push that word in the nbrs vector
*/

vector<string> get_nbrs (unordered_set<string> & words, string & s){
    vector<string> nbrs;

    for(int i=0; i<s.length(); i++){
        string new_word = s;

        for(int offset=0; offset<26; offset++){
            char ch = 'a' + offset;
            if(ch!=s[i]) new_word[i] = ch;

            if(words.find(new_word)!=words.end())
                nbrs.push_back(new_word);
        }
    }

    return nbrs;
}

int bfs (unordered_set<string> & words, string & beginWord, string & endWord){
    int transformation = 1;
    queue<string> q;

    q.push(beginWord);
    q.push("#");
    words.erase(beginWord);              // ----> Erasing is similar to marking that word visited

    while(!q.empty()){
        string s = q.front(); q.pop();

        if(s==endWord) return transformation;

        if(s=="#"){
            if(q.empty()) break;

            q.push("#");
            transformation++;
            continue;
        }

        vector<string> nbrs = get_nbrs(words, s);

        for(string nbr : nbrs){
            q.push(nbr);
            words.erase(nbr);
        }
    }

    return 0;
}

int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
    unordered_set<string> words;

    for(auto word : wordList)
        words.insert(word);

    return bfs (words, beginWord, endWord);   
}
```

#### 5. Word Ladder II
Given two words, beginWord and endWord, and a dictionary wordList, return all the shortest transformation sequences from beginWord to endWord, or an empty list if no such sequence exists. Each sequence should be returned as a list of the words [beginWord, s1, s2, ..., sk]

Input: beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log","cog"]

Output: [["hit","hot","dot","dog","cog"],["hit","hot","lot","log","cog"]]

Hint: Push whole path in the queue

```cpp
/* same as word ladder 1 */

vector<string> get_nbrs (unordered_set<string>& words, string & s){
    vector<string> nbrs;

    for(int i=0; i<s.size(); i++){
        string new_word = s;

        for(int offset=0; offset<26; offset++){
            char ch = 'a' + offset;
            if(ch==s[i]) continue; 

            new_word[i] = ch;

            if(words.find(new_word)!=words.end())
                nbrs.push_back(new_word);
        }
    }

    return nbrs;
}


vector<vector<string>> bfs (unordered_set<string>& words, string & beginWord, string & endWord){
    vector<vector<string>> res;   

    /* min_path length discovered so far*/

    int minPath_len = INT_MAX;    

    /* here we store whole path instead of string */

    queue<vector<string>> q;       
    q.push({beginWord});           // ----> Initially pushing one vector containing beginWord 

    unordered_map<string, int> vis;

    while(!q.empty()){
        auto path = q.front(); q.pop();

        /* If we got the complete path then */

        if(path.back()==endWord){

            /* 
                If current path len is lesser then discover path so far
                then clear the result vector and push new path with shorter len
                and also update the variable minPath_len
            */

            if(path.size() < minPath_len){
                res.clear();
                res.push_back(path);
                minPath_len = path.size();
                continue;
            }

            /* Else if push curr path also in the res vector and continue our search*/ 

            else if(path.size()==minPath_len){
                res.push_back(path);
                continue;
            }

            /* Else we discoverd the path with greater len, so break (as we are using BFS) */

            else break; 
        }

        vector<string> nbrs = get_nbrs (words, path.back());

        for(string nbr : nbrs){

            /* 
                If we got the already visited nbr and 
                curr_path size >= earlier path size (where this nbr is used) 
                then we will not use this nbr for curr_path so continue
            */

            if(vis.find(nbr)!=vis.end() and path.size()>=vis[nbr]) continue;

            /* Else create new branch (new_path) by pushing nbr to old_path */

            vector<string> new_path = path;
            new_path.push_back(nbr);
            q.push(new_path);

            vis[nbr] = new_path.size();
        }
    }

    return res;
}


vector<vector<string>> findLadders(string beginWord, string endWord, vector<string>& wordList) {
    unordered_set<string> words;
    for(string word : wordList)
        words.insert(word);

    words.erase(beginWord);

    return bfs (words, beginWord, endWord);
}
```

#### 6. Jump Game IV
Given an array of integers arr, you are initially positioned at the first index of the array. In one step you can jump from index i to index:

1. i + 1 where: i + 1 < arr.length.
2. i - 1 where: i - 1 >= 0.
3. j where: arr[i] == arr[j] and i != j.

Return the minimum number of steps to reach the last index of the array. Notice that you can not jump outside of the array at any time.

Input: arr = [100,-23,-23,404,100,23,23,23,3,404]

Output: 3 (You need three jumps from index 0 --> 4 --> 3 --> 9)

```cpp
vector<int> get_nbrs (unordered_map<int, vector<int>>& mp, vector<int>& arr, int src, int n){
    vector<int> nbrs;

    if(src>0) nbrs.push_back(src-1);
    if(src<n-1) nbrs.push_back(src+1);

    for(int nbr : mp[arr[src]])
        if(nbr!=src or nbr!=src-1 or nbr!=src+1)
            nbrs.push_back(nbr);

    return nbrs;
}

int bfs (vector<int>& arr, unordered_map<int, vector<int>>& mp, int src, int n){
    vector<bool> vis (n, false);
    queue<int> q;
    int jumps = 0;

    q.push(src);
    q.push(-1);
    vis[src] = true;

    while(!q.empty()){
        src = q.front(); q.pop();

        if(src==n-1) return jumps;

        if(src==-1){
            if(q.empty()) break;

            q.push(-1);
            jumps++;
            continue;
        }

        vector<int> nbrs = get_nbrs(mp, arr, src, n);

        for(int nbr : nbrs){
            if(!vis[nbr]){
                q.push(nbr);
                vis[nbr] = true;
            }
        }
    }

    return -1;
}

int minJumps(vector<int>& arr) {
    int n = arr.size();
    unordered_map<int, vector<int>> mp;         // ---> It will map same values to their indexes

    int item = 0;
    int count = 0;

    /* Trick for duplicates : We will insert only first and last index for continous duplicates */

    for(int i=0; i<n; i++){

        if(item==arr[i]) count++;
        else{
            item = arr[i];
            count = 1;
        } 

        if(count>2) mp[arr[i]].pop_back();

        mp[arr[i]].push_back(i);
    }

    return bfs(arr, mp, 0, n);
}
```

#### 7. Water and Jug Problem
You are given two jugs with capacities jug1Capacity and jug2Capacity liters. There is an infinite amount of water supply available. Determine whether it is possible to measure exactly targetCapacity liters using these two jugs. If targetCapacity liters of water are measurable, you must have targetCapacity liters of water contained within one or both buckets by the end.

Input: jug1Capacity = 3, jug2Capacity = 5, targetCapacity = 4

Output: true

```cpp
struct jug{
    int one, two, steps;
    jug(int one, int two, int steps){
        this->one = one;
        this->two = two;
        this->steps = steps;
    }
};

vector<pair<int,int>> get_nbrs (int & jug1, int & jug2, int & jug1Capacity, int & jug2Capacity){
    vector<pair<int,int>> nbrs;

    /* Following are the 6 options to perform operations */

    nbrs.push_back({0, jug2});                                                         // --> empty jug1 
    nbrs.push_back({jug1, 0});                                                         // --> empty jug2
    nbrs.push_back({jug1Capacity, jug2});                                              // --> fill jug1 completely
    nbrs.push_back({jug1, jug2Capacity});                                              // --> fill jug2 completely
    nbrs.push_back({min(jug1Capacity, jug1+jug2), max(0,jug2-(jug1Capacity-jug1))});   // --> pour water from jug2 to jug1
    nbrs.push_back({max(0,jug1-(jug2Capacity-jug2)), min(jug2Capacity, jug2+jug1)});   // --> pour water from jug1 to jug2

    return nbrs;
}


bool bfs(int & jug1Capacity, int & jug2Capacity, int & target){
    jug init (0, 0, 0);

    queue<jug> q;
    q.push(init);

    unordered_set<string> vis;
    vis.insert("0-0");

    while(!q.empty()){
        auto j = q.front(); q.pop();
        int jug1 = j.one, jug2 = j.two, steps = j.steps;

        /* Target condition is when any one jug is equal to targetCapacity or sum of both jug equals to targetCapacity */

        if(jug1==target or jug2==target or jug1+jug2==target) return true;

        auto nbrs = get_nbrs(jug1, jug2, jug1Capacity, jug2Capacity);

        for(auto nbr : nbrs){
            string s = to_string(nbr.first) + "-" + to_string(nbr.second);
            if(vis.find(s)!=vis.end()) continue;

            jug nbr_j (nbr.first, nbr.second, steps+1);
            q.push(nbr_j);
            vis.insert(s);
        }
    }

    return false;
}


bool canMeasureWater(int jug1Capacity, int jug2Capacity, int targetCapacity) {

    return bfs (jug1Capacity, jug2Capacity, targetCapacity);
}
```

#### 8. Bus Routes (Tricky)
You are given an array routes representing bus routes where routes[i] is a bus route that the ith bus repeats forever. For example, if routes[0] = [1, 5, 7], this means that the 0th bus travels in the sequence 1 -> 5 -> 7 -> 1 -> 5 -> 7 -> 1 -> ... forever.
You will start at the bus stop source (You are not on any bus initially), and you want to go to the bus stop target. You can travel between bus stops by buses only. Return the least number of buses you must take to travel from source to target. Return -1 if it is not possible.

Input: routes = [[1,2,7],[3,6,7]], source = 1, target = 6

Output: 2

```cpp
struct node{
    int bus, buses_taken;
    node(int b, int bt){
        bus = b; 
        buses_taken = bt;
    }
};


int bfs (unordered_map<int, vector<int>>& buses, unordered_map<int, unordered_set<int>>& stops, queue<node>& q, unordered_set<int>& vis_stops, unordered_set<int>& vis_buses, int target){

    while(!q.empty()){
        node n = q.front(); q.pop();
        int bus = n.bus, buses_taken = n.buses_taken;

        /* Fetch the route of the popped bus --> If that route contains target stop, then simply return the buses_taken */

        auto route = stops[bus];
        if(route.find(target)!=route.end()) return buses_taken;

        /* Else Iterate for each stop of that route */

        for(int stop : route){
            if(vis_stops.find(stop)!=vis_stops.end()) continue;

            /* If that stop is not visited then iterate for all possible buses from that stop */

            for(int b : buses[stop]){
                if(vis_buses.find(b)!=vis_buses.end()) continue;

                /* If this bus is not visited then push it in the queue and also increment the buses_taken */

                node nbr (b, buses_taken+1);
                q.push(nbr);
                vis_buses.insert(b);         // ----> mark that bus visited
            }
            vis_stops.insert(stop);          // ----> mark that stop visited
        }
    }

    return -1;
}


int numBusesToDestination(vector<vector<int>>& routes, int source, int target) {
    if(source==target) return 0;

    int total_buses = routes.size();

    unordered_map<int, vector<int>> buses;                  //----> map of { stop : vector of buses } 
    unordered_map<int, unordered_set<int>> stops;           //----> map of { bus : unordered_set of stops } 

    unordered_set<int> vis_stops;
    unordered_set<int> vis_buses;

    queue<node> q;

    /* 
        Create mappings of bus with their stops
        and Stops with respective buses
    */

    for(int bus=0; bus<total_buses; bus++){
        for(int stop : routes[bus]){
            stops[bus].insert(stop);        
            buses[stop].push_back(bus);
        }                
    }

    /* Push all buses that can be fetched from source in the queue */

    for(int bus : buses[source]){
        node n (bus, 1);
        q.push(n);
        vis_buses.insert(bus);
    }
    vis_stops.insert(source);

    return bfs (buses, stops, q, vis_stops, vis_buses, target);
}
```

