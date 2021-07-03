# Dijkstra Algo

Used for finding shortest path (or cheapest) in weightes graph.

## @ Dijkstra on Adjacency List

#### 1. Network Delay Time
You are given a network of n nodes, labeled from 1 to n. You are also given a list of travel times as directed edges times[i] = (ui, vi, wi), where ui is the source node, vi is the target node, and wi is the time it takes for a signal to travel from source to target. We will send a signal from a given node k. Return the time it takes for all the n nodes to receive the signal. If it is impossible for all the n nodes to receive the signal, return -1.

![img](https://assets.leetcode.com/uploads/2019/05/23/931_example_1.png)

Input: times = [[2,1,1],[2,3,1],[3,4,1]], n = 4, k = 2

Output: 2

```cpp
void dijkstra (unordered_map<int, vector<pair<int, int>>>& graph, vector<int> & cost, int src, int n){
    vector<bool> vis (n+1, false);

    set<pair<int, int>> s;                                 // ---> set of <cost, vertex>
    s.insert({cost[src], src});

    while(!s.empty()){

        /* Pop node with lowest cost */

        auto itr = s.begin();
        int c = itr->first, v = itr->second;
        s.erase(itr);

        /* Iterating for all nbr */

        for(auto nbr : graph[v]){
            int nbr_v = nbr.first, nbr_c = nbr.second;

            if(vis[nbr_v]) continue;

            int old_cost = cost[nbr_v];
            int new_cost = c + nbr_c;

            /* Relaxation */

            if(new_cost < old_cost){
                auto ptr = s.find({old_cost, nbr_v});
                if(ptr!=s.end())
                    s.erase(ptr);

                s.insert({new_cost, nbr_v});
                cost[nbr_v] = new_cost;
            }
        }
        
        vis[v] = true;
    }
}

int networkDelayTime(vector<vector<int>>& times, int n, int k) {
    unordered_map<int, vector<pair<int, int>>> graph;        //   v --> [list of (nbr, time)]
    for(auto e : times)
        graph[e[0]].push_back({e[1], e[2]});

    vector<int> cost (n+1, INT_MAX);                 // ----> here time will be our cost
    cost[k] = 0;

    dijkstra(graph, cost, k, n);

    /* Total time will be equals to maxtime among all vertex */

    int total_time = 0;
    for(int i=1; i<=n; i++){
        if(cost[i]==INT_MAX) 
            return -1;
        total_time = max(total_time,cost[i]);
    }

    return total_time;
}
```


## @ Dijkstra on Matrix

#### 1. Path With Minimum Effort
You are given a 2D array of size rows x columns, where heights[row][col] represents the height of cell (row, col). You are situated in the top-left cell, (0, 0), and you hope to travel to the bottom-right cell, (rows-1, columns-1) (i.e., 0-indexed). You can move up, down, left, or right. A route's effort is the maximum absolute difference in heights between two consecutive cells of the route. Return the minimum effort required to travel from the top-left cell to the bottom-right cell.

![img](https://assets.leetcode.com/uploads/2020/10/04/ex1.png)

Input: heights = [[1,2,2],[3,8,2],[5,3,5]]

Output: 2

```cpp
bool isInside(int x, int y, int row, int col){
    return x>=0 and y>=0 and x<row and y<col;
}

int dijkstra (vector<vector<int>>& heights, vector<vector<int>>& cost, int row, int col){
    vector<vector<bool>> vis (row, vector<bool> (col, false));
    
    int dx[] = {1, 0, -1, 0};
    int dy[] = {0, 1, 0, -1};

    set<pair<int, pair<int,int>>> s;      // ----> set <cost, <x,y>> 
    s.insert({0,{0,0}});

    vis[0][0] = true;

    while(!s.empty() and !vis[row-1][col-1]){

        /* Find min element from set */

        auto src = s.begin();                             
        int x = src->second.first, y = src->second.second, c = src->first;

        /* Erase min element from set */

        s.erase(src);                                  

        /* Now Iterate for all 4 nbr of above cell */

        for(int i=0; i<4; i++){
            int xnew = x + dx[i];
            int ynew = y + dy[i];                          

            /* If cell is valid and not vis */

            if(isInside(xnew, ynew, row, col) and !vis[xnew][ynew]){

                /* Find old_cost from cost matrix and compute new_cost by max (c, abs difference of cells) */

                int old_cost = cost[xnew][ynew];
                int new_cost = max(c, abs(heights[xnew][ynew] - heights[x][y]));

                /* If new_cost<old_cost ----> do Relaxation */

                if(new_cost<old_cost){

                    /* update value in cost_matrix */

                    cost[xnew][ynew] = new_cost;

                    /* find that nbr in set */

                    pair<int, pair<int, int>> nbr = {old_cost, {xnew, ynew}};
                    auto ptr = s.find(nbr);

                    /* If nbr exist ----> erase it so we can insert it again with updated values */

                    if(ptr != s.end())
                        s.erase(ptr);

                    /* Insert new or updated nbr into set */

                    s.insert({new_cost, {xnew, ynew}});
                }
            }
        }

        vis[x][y] = true;
    }

    return cost[row-1][col-1];
}


int minimumEffortPath(vector<vector<int>>& heights) {
    int row = heights.size(), col = heights[0].size();

    /* cost matrix is required for finding nbr cell (if exist) in the set */

    vector<vector<int>> cost (row, vector<int> (col, INT_MAX));
    cost[0][0] = 0;

    return dijkstra(heights, cost, row, col);
}
```

## @ Dijkstra without Relaxation

#### 1. Cheapest Flights Within K Stops
There are n cities connected by some number of flights. You are given an array flights where flights[i] = [fromi, toi, pricei]. You are also given three integers src, dst, and k, return the cheapest price from src to dst with at most k stops. If there is no such route, return -1.

![img](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/02/16/995.png)

**Intuition behind the solution**

The key difference with the classic Dijkstra algo is, we don't maintain the global optimal distance to each node (i.e. Relaxation should not be done), because there could be routes which their length is shorter but pass more stops, and those routes don't necessarily constitute the best route in the end. To deal with this, the solution simply put all possible routes into the multiset (or priority queue), so that all of them has a chance to be processed. IMO, this is the most brilliant part.
And the solution simply returns the first qualified route.

```cpp
struct vertex {
    int val, cost, k;

    vertex (int val, int cost, int k){
        this->val = val;
        this->cost = cost;
        this->k = k;
    }

    /* overriding < operator, This function is used by set to order vertices based on cost */

    bool operator< (const vertex & vt) const{
        return (this->cost < vt.cost);
    }
};

int dijkstra (unordered_map<int, vector<pair<int, int>>>& graph, int src, int dst, int k){
    multiset<vertex> s;
    s.insert(vertex (src, 0, k));

    while(!s.empty()){

        /* Take out one node with minimum cost from the set */

        auto itr = s.begin();
        vertex vt = *itr;
        s.erase(itr);

        /* If node==dst, simply return its cost */

        if(vt.val==dst) return vt.cost;

        /* If curr popped vertex is not destination and k<0 then do not explore that vertex further */

        if(vt.k<0) continue;    

        /* Else explore its neighbours */

        for(auto nbr : graph[vt.val]){

            /* 
                compute new_cost and insert new_nbr with new_cost and new value for stops 
                We will not do Relaxation here, i.e. we will not update old value,
                instead we always insert new nbr thats why cost array is not required in this question
            */

            vertex new_nbr (nbr.first, vt.cost + nbr.second, vt.k-1);
            s.insert(new_nbr);
        }
    }

    return -1;
}

int findCheapestPrice(int n, vector<vector<int>>& flights, int src, int dst, int k) {
    unordered_map<int, vector<pair<int, int>>> graph;

    for(auto e : flights)
        graph[e[0]].push_back({e[1], e[2]});

    return dijkstra(graph, src, dst, k);
}
```
