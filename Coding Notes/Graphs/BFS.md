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

    q.push({-1, -1});      // ----> will act as a ending delimeter for bds levels

    int time = bfs(grid, q, row, col, rotten_oranges);

    if(total_oranges==rotten_oranges) return time;        // ----> Don't forget to check this condition
    else return -1;
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

