# BFS

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