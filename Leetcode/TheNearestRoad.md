## Djikstra 算法(单源最短路径)

**朴素版：**
```C++
//choose: 在还没访问过的顶点中，找到 distance 最小的那个顶点下标
int choose(const vector<int>& distance,
           const vector<int>& found,
           int vertex_num)
{
    int minVal = MAX;
    int minPos = -1;
    for (int i = 0; i < vertex_num; i++) {
        if (!found[i] && distance[i] < minVal) {
            minVal = distance[i];
            minPos = i;
        }
    }
    return minPos;   // 返回下标，找不到返回 -1
}


int vertex_num;
void dijkstra(const Mat_Grph& G, int start)
{
    int n = G.vertex_num;
    vector<int> found(n, 0);       // 是否已确定最短路
    vector<int> path(n, -1);       // 前置节点
    vector<int> distance(n, MAX);  // start 到各点的最短距离
    distance[start] = 0;
    for (int i = 0; i < n; i++) {
        int next = choose(distance, found, n);
        if (next == -1) break;       // 剩下的点都不可达
        found[next] = 1;             // 确定 next 的最短路
        // 用 next 松弛其他点
        for (int j = 0; j < n; j++) {
            if (!found[j] && G.arc[next][j] != MAX) {
                // 防止 MAX + x 溢出，所以先判断 arc[next][j] != MAX
                if (distance[next] + G.arc[next][j] < distance[j]) {
                    distance[j] = distance[next] + G.arc[next][j];
                    path[j] = next;   // j 的前置节点是 next
                }
            }
        }
    }
}
```

**队列+邻接表优化版：**
```C++
int vertex_num;
vector<vector<pair<int,int>>> adj;   // adj[u] = { {v, w}, ... }
void dijkstra(int start)
{
    int n = vertex_num;
    vector<int> found(n, 0);
    vector<int> path(n, -1);
    vector<int> distance(n, MAX);
    // 小根堆：pair<当前距离, 顶点>
    priority_queue<pair<int,int>,vector<pair<int,int>>,greater<pair<int,int>>> heap;

    distance[start] = 0;
    heap.push({0, start});

    while (!heap.empty()) {
        auto [d, next] = heap.top();
        heap.pop();

        if (found[next]) continue;
        found[next] = 1;

        // 遍历 next 的所有出边
        for (auto [j, w] : adj[next]) {
            if (!found[j] && distance[next] + w < distance[j]) {
                distance[j] = distance[next] + w;
                path[j] = next;
                heap.push({distance[j], j});
            }
        }
    }
}
```
> *易错：小根堆是按 first 排序的，所以要先放{距离, 点}*
> 小根堆里存的是"待处理的候选状态"，每个状态包含：(到达该点的距离, 点的信息)。
> 每次取出堆顶元素，就是当前"待处理的候选状态"，然后更新其他状态，再放回堆中。

**网格图与djikstra综合：**
```C++
int dx[] = {-1, 1, 0, 0};
int dy[] = {0, 0, -1, 1};
int dijkstraGrid(vector<vector<int>>& grid) {
    int m = grid.size(), n = grid[0].size();
    const int INF = INT_MAX;
    // 距离数组：dist[i][j] = 从起点到 (i, j) 的最短距离
    vector<vector<int>> dist(m, vector<int>(n, INF));
    // 小根堆：{距离, x, y}
    priority_queue<tuple<int,int,int>,vector<tuple<int,int,int>>,greater<tuple<int,int,int>>> heap;
    // 起点 (0, 0)
    dist[0][0] = grid[0][0];        // 起点代价按题目定
    heap.push({dist[0][0], 0, 0});
    while (!heap.empty()) {
        auto [d, x, y] = heap.top();
        heap.pop();
        // 懒删除：如果已经有更短的路径到这里，跳过
        if (d > dist[x][y]) continue;
        // 到达终点，直接返回
        if (x == m - 1 && y == n - 1) return d;
        // 枚举 4 个方向
        for (int k = 0; k < 4; k++) {
            int nx = x + dx[k];
            int ny = y + dy[k];
            // 越界检查
            if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;
            int newDist = d + grid[nx][ny];// 计算新距离（边权按题目定义）
            if (newDist < dist[nx][ny]) {
                dist[nx][ny] = newDist;
                heap.push({newDist, nx, ny});
            }
        }
    }
    
    return dist[m-1][n-1];
}
```
例题：
> T1631,T3341

## 记忆化搜索
```C++
// 1. 准备备忘录数组，初始填 -1，代表“没算过”
vector<int> dp(n, -1); 
int dfs(int u) {
    // 第一步：递归出口（终点）
    if (u == 终点) return 1;
    // 第二步：查表！如果算过，直接抄答案！
    if (dp[u] != -1) return dp[u];
    // 第三步：如果没算过，正常去算答案
    int res = 0;
    for (邻居 v : u的邻居们) {
        res += dfs(v);
    }
    // 第四步：把算出来的答案【记在备忘录里】，然后返回
    return dp[u] = res;
}
```

## Floyd 算法(多源最短路径)
```C++
int vertex_num;
vector<vector<int>> dist;
vector<vector<int>> path;
void floyd()
{
    for (int k = 0; k < vertex_num; k++) {// 中间点
        for (int i = 0; i < vertex_num; i++) {// 起点
            for (int j = 0; j < vertex_num; j++) {// 终点
                if (dist[i][k] != MAX && dist[k][j] != MAX && dist[i][j] > dist[i][k] + dist[k][j]) {
                    dist[i][j] = dist[i][k] + dist[k][j];
                    path[i][j]=path[i][k];
                }
            }
        }
    }
}
```

**最小环问题**
```C++
int ans = INF;
for (int k = 0; k < n; k++) {
    // 先用 k-1 之前的中间点，找经过 k 的最小环
    for (int i = 0; i < k; i++)
        for (int j = i + 1; j < k; j++)
            ans = min(ans, dist[i][j] + g[i][k] + g[k][j]);
    // 再更新
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);
}
```

**传递闭包**
```C++
for (int k = 0; k < n; k++)
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            reach[i][j] = reach[i][j] || (reach[i][k] && reach[k][j]);
```

## Bellman-Ford 算法(单源最短路径)
最终结果算出的是start到所有点的最短距离（和floyd不一样）
```C++
int n, m;
vector<vector<pair<int,int>>> adj;   // adj[u] = { {v, w}, ... }
vector<int> dist;
bool bellmanFord(int start) {
    dist.assign(n, INF);
    dist[start] = 0;
    // 松弛 n - 1 轮
    for (int i = 0; i < n - 1; i++) {
        bool updated = false;
        // 遍历所有边：外层枚举 u，内层枚举 u 的出边
        for (int u = 0; u < n; u++) {
            if (dist[u] == INF) continue;   // 起点未到达，跳过
            for (auto [v, w] : adj[u]) {
                if (dist[u] + w < dist[v]) {
                    dist[v] = dist[u] + w;
                    updated = true;
                }
            }
        }
        
        if (!updated) break;   // 优化：本轮无更新，提前结束
    }
    // 第 n 轮：还能松弛 → 有负权回路
    for (int u = 0; u < n; u++) {
        if (dist[u] == INF) continue;
        for (auto [v, w] : adj[u]) {
            if (dist[u] + w < dist[v]) {
                return false;   // 存在负权回路
            }
        }
    }
    return true;
}
```
*最多K条边的最短路：* Dijkstra 做不到，但 Bellman-Ford 可以
只需要松弛 k 轮！
**T787**
```C++
int findCheapestPrice(int n, vector<vector<int>>& flights, int src, int dst, int K) {
    vector<int> dist(n, INT_MAX);
    dist[src] = 0;
    // 最多 K+1 条边（中转 K 站 = 走 K+1 条边）
    for (int i = 0; i <= K; i++) {
        vector<int> tmp = dist;   // ⚠️ 必须复制，防止一轮内多次更新
        for (auto& f : flights) {
            int u = f[0], v = f[1], w = f[2];
            if (dist[u] != INT_MAX && dist[u] + w < tmp[v]) {
                tmp[v] = dist[u] + w;
            }
        }
        dist = tmp;
    }
    return dist[dst] == INT_MAX ? -1 : dist[dst];
}
```
## SPFA(Bellman-Ford + 队列优化)
>核心观察:Bellman-Ford 的问题是：每轮都盲目遍历所有边，很多边根本不需要松弛。
SPFA 的想法：只有 dist 被更新过的点，才可能去松弛它的邻居。用一个队列存"需要检查的点"，就像 BFS 一样。

```C++
int n, m;
vector<pair<int,int>> adj[505];   // adj[u] = {(v, w), ...}
int dist[505];
bool inQueue[505];

void spfa(int start) {
    memset(dist, 0x3f, sizeof dist);
    memset(inQueue, false, sizeof inQueue);
    dist[start] = 0;
    
    queue<int> q;
    q.push(start);
    inQueue[start] = true;
    
    while (!q.empty()) {
        int u = q.front();
        q.pop();
        inQueue[u] = false;
        
        for (auto [v, w] : adj[u]) {
            if (dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                if (!inQueue[v]) {
                    q.push(v);
                    inQueue[v] = true;
                }
            }
        }
    }
}
```
> SPFA如何处理负权回路？
```C++
int cnt[N];   // 记录每个点入队次数
// 入队时
cnt[v]++;
if (cnt[v] >= n) return false;   // 有负权回路
```