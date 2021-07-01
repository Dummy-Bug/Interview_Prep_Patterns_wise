# Topological Sort

It is a linear ordering of vertices such that for every directed edge u --> v, vertex u comes before vertex v in the ordering.

#### 1. Course Schedule II

**Method 1: DFS + Stack**
```cpp
bool dfs (unordered_map<int, vector<int>> & graph, vector<bool> & vis, vector<bool> & curr_path, stack<int> & stk, int src){
    vis[src] = true;
    curr_path[src] = true;

    for(int nbr : graph[src]){

        if(curr_path[nbr]) return false;

        if(!vis[nbr]){
            bool res = dfs(graph, vis, curr_path, stk, nbr);
            if(!res) return false;
        }
    }

    curr_path[src] = false;
    stk.push(src);
    return true;
}

vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
    unordered_map<int, vector<int>> graph;
    vector<bool> vis (numCourses, false);
    vector<bool> curr_path (numCourses, false);

    stack<int> stk;
    vector<int> res;

    for(auto e : prerequisites)
        graph[e[1]].push_back(e[0]);        // ----> create edges u --> v such that u comes before v 

    for(int i=0; i<numCourses; i++){
        if(!vis[i]){
            bool order_exist = dfs(graph, vis, curr_path, stk, i);
            if(!order_exist) return res;
        }
    }

    while(!stk.empty()){
        res.push_back(stk.top());
        stk.pop();
    }

    return res;
}
```

**Method 2: BFS + indegre array (Kahns Algo)**

Approach: 
1. Push all vertices with indegre 0 to queue
2. Iterate till queue becomes empty
3. Pop one element from queues --> Relax all its nbrs (decrement all its nbr's indegree by 1) --> and add that popped element to order vector.
4. While doing relaxing, If indegre of any nbr becomes 0, push it in the queue

```cpp
int bfs (unordered_map<int, vector<int>>& graph, vector<int>& indegre, queue<int>& q, vector<int>& order){
    int vis_count = 0;          // ---> It keeps track for processed vertices

    while(!q.empty()){
        int node = q.front(); q.pop();

        for(int nbr : graph[node]){
            indegre[nbr]--;
            if(indegre[nbr]==0)
                q.push(nbr);
        }

        order.push_back(node);
        vis_count++;
    }

    return vis_count;
}

vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
    unordered_map<int, vector<int>> graph;
    vector<int> indegre (numCourses, 0);
    vector<int> order;

    for(auto e : prerequisites){
        graph[e[1]].push_back(e[0]);   // ----> creating edge  u-->v
        indegre[e[0]]++;               // ----> update indegre for v
    }

    queue<int> q;

    for(int i=0; i<numCourses; i++)
        if(indegre[i]==0)
            q.push(i);

    int vis_count = bfs (graph, indegre, q, order);

    if(vis_count<numCourses)
        return vector<int>();

    return order;
}
```
