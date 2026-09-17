# 回溯

### 子集型回溯

**选或不选**(从输入出发)
```C++
    void dfs(int i) {
        int n = nums.size();
        if (i == n) {// 递归边界：处理完所有元素
            ans.push_back(path);  // 将当前子集加入答案
            return;
        }
        // 分支1：不选 nums[i]
        dfs(i + 1);
        // 分支2：选 nums[i]
        path.push_back(nums[i]);  // 选择当前元素
        dfs(i + 1);              // 递归处理下一个
        path.pop_back();         // 恢复现场（撤销选择）
    }
```

**枚举型**（从答案出发）
```C++
// 独立函数 dfs：枚举从下标 i 到 n-1 中选哪个数
    void dfs(int i) {
        int n = nums.size();
        // 关键：每次进入 dfs，都将当前 path 作为一个子集加入答案
        // 这代表了"不选"任何从 i 开始的元素
        ans.emplace_back(path);
        
        // 枚举选哪个：在 i 到 n-1 中选一个数 nums[j] 加入 path
        for (int j = i; j < n; j++) {
            path.push_back(nums[j]);  // 选择 nums[j]
            dfs(j + 1);              // 递归：下一个元素只能从 j+1 开始选
            path.pop_back();         // 恢复现场，撤销选择 nums[j]
        }
    }
```

**T39 组合总和**：
```C++
auto dfs = [&](this auto&& dfs, int i, int left) {
            if (left == 0) {
                // 找到一个合法组合
                ans.push_back(path);
                return;
            }
            if (i == candidates.size() || left < 0) {
                return;
            }
            // 不选
            dfs(i + 1, left);
            // 选
            path.push_back(candidates[i]);
            dfs(i, left - candidates[i]);// 和上面那些不可重复选的不同，这里i不变，因为还可以重复选
            path.pop_back(); // 恢复现场
        };
```

## 划分型回溯
**T131 分割回文串**：
选或不选解法：*关键是要记录start*
```C++
    // 参数 i: 当前正在考虑的位置（可以理解为当前子串的结束位置候选）
    // 参数 start: 当前待分割子串的起始位置
    void dfs(int i, int start) {
        // 递归终止条件：已经处理完整个字符串
        if (i == n) {
            ans.push_back(path); // 将当前分割方案加入答案
            return;
        }
        // 决策1：不在 i 和 i+1 之间分割,即让 s[i] 与后面的字符组成更长的子串
        // 注意：当 i 是最后一个字符 (i == n-1) 时，必须分割，否则会无限递归
        if (i < n - 1) {
            dfs(i + 1, start);
        }
        // 决策2：在 i 和 i+1 之间分割,即截取子串 [start, i]，如果它是回文串，则将其加入路径
        if (is_palindrome(start, i)) {
            path.push_back(s.substr(start, i - start + 1));
            // 递归处理剩余部分，剩余部分的起始位置是 i+1
            dfs(i + 1, i + 1);
            path.pop_back();
        }
    }
```



枚举型解法：*不用记录start，因为隐含在j里面了*
```C++
// 参数 i: 当前待分割子串的起始位置
void dfs(int i) {
        // 递归终止条件：起始位置 i 已经到达字符串末尾，说明找到了一种完整分割
        if (i == n) {
            ans.push_back(path);
            return;
        }

        // 枚举从当前位置i开始的所有可能子串的结束位置j
        for (int j = i; j < n; j++) {
            // 只有当子串 s[i..j] 是回文串时，才考虑这种分割
            if (isPalindrome(i, j)) {
                path.push_back(s.substr(i, j - i + 1));
                // 递归处理剩余部分，剩余部分的起始位置是 j+1
                dfs(j + 1);
                path.pop_back();
            }
        }
    }
```

*补充：如果加上一个字符串，怎么回溯回去？*：
```C++
    int oldlen=x.length();
    x+=temp;//temp是新加入的字符串
    dfs(s,i+1,i+1,x,p);
    x.resize(oldlen);
```

## 排列型回溯
**T46 全排列**：
```C++
    sort(nums.begin(), nums.end()); 
    vector<vector<int>>ans;
    do{
        ans.push_back(nums);
    }while (next_permutation(nums.begin(), nums.end()));
```
*使用要求:序列必须按升序初始，否则不会输出所有全排列,所以调用库时一般先排序（sort）*
一般写法（不用库函数）：
```C++
     void dfs(vector<int>& nums,vector<int>& path,vector<bool>& vis){
        if(path.size()==nums.size()){
            ans.push_back(path);
            return ;
        }
        for(int i=0;i<nums.size();i++){
            if(!vis[i]){
                vis[i]=true;
                path.push_back(nums[i]);
                dfs(nums,path,vis);
                path.pop_back();
                vis[i]=false;
            }
        }
    }
```
**T47 全排列 II**：如果含重复元素呢？->在dfs每一层内单独去重，即同一层内相同的值只用选一次！

**N皇后问题**：本质上是列的全排列问题，关键是如何判断是否在同一斜线上（*斜率相同*）

## 含重复元素的回溯
**T90 子集 II**：
```C++
 void dfs(vector<int>& path,vector<int>& nums,int i){
        if(i==nums.size()){
            ans.push_back(path);
            return ;
        }
        //选
        path.push_back(nums[i]);
        dfs(path,nums,i+1);
        path.pop_back();
        int x=nums[i];
        //不选（即所有与x相同的元素都跳过）
        i++;
        while(i<nums.size()&&nums[i]==x){
            i++;
        }
        dfs(path,nums,i);
    }
```
**T1079 活字印刷**：和子集不一样！！！因为他要求各种顺序都可以，而子集是只能按照原有的顺序

*总结：看到"子集/子序列"→ 选或不选
看到"排列/顺序不同算不同"→ for 循环 + vis 数组
局部去重中如果是每一层dfs拥有的set，无需erase，若是外部传入，则需要*
## 搜索
dfs中的主要部分：
```C++
    for(int i=0;i<4;i++){
            int nx=x+dx[i];
            int ny=y+dy[i];
            if(nx>=0&&ny>=0&&nx<m&&ny<n){
                if(board[nx][ny]==word[k]&&!vis[nx][ny]){
                    vis[nx][ny]=true;
                    dfs(word,board,nx,ny,k+1);
                    vis[nx][ny]=false;
                }
            }
        }
```
**T1255 单词搜索**：本质也是选或不选的问题，但是这里选或不选针对的是word[i];
**T2002 两个回文子序列的最大乘积**：暴力三选一回溯，是否回文后续可以再判断
```C++
// 不选：什么都不做，直接递归
dfs(s, i + 1);
// 放入 a：加 → 递归 → 撤销
a.push_back(s[i]);
dfs(s, i + 1);
a.pop_back();
// 放入 b：加 → 递归 → 撤销
b.push_back(s[i]);
dfs(s, i + 1);
b.pop_back();
```

## 网格图
**T695 岛屿最大面积：** 岛屿类问题的标记不需要回溯！
```C++
    int dfs(vector<vector<int>>& grid, int x, int y) {
        int m = grid.size();
        int n = grid[0].size();
        int area = 1; //读值
        grid[nx][ny] = 0;   //清0,这两步同时进行避免混淆 （主函数和dfs中循环都不用管值归零了，因为dfs自己会管）
        for (int i = 0; i < 4; i++) {
            int nx = dx[i] + x;
            int ny = dy[i] + y;
            if (nx >= 0 && ny >= 0 && nx < m && ny < n && grid[nx][ny] == 1) {
                area += dfs(grid, nx, ny); 
            }
        }
        return area;
    }
```
**T463 岛屿周长：** 
```C++
    void dfs(vector<vector<int>>& grid,int x,int y){
        grid[x][y]=2;// 用 2 标记已访问，避免与 0(水) 混淆
        int m=grid.size();
       int n=grid[0].size();
        for(int i=0;i<4;i++){
            int nx=x+dx[i];
            int ny=y+dy[i];
            // 邻居越界或是水 → 贡献一条边(这样能避免多算或者少算的问题)
            if(nx<0||ny<0||nx>=m||ny>=n|| grid[nx][ny] == 0){
                ans++;
                continue;
            }
            if(grid[nx][ny]==1){
                dfs(grid,nx,ny);
            }
        }
    }
```
**T2684 移动的最大步数：** 记忆化DFS
```C++
    vector<vector<int>> memo;// 记忆化数组
    int dfs(vector<vector<int>>& grid, int x, int y) {
        if (memo[x][y] != -1) return memo[x][y];
        int m = grid.size();
        int n = grid[0].size();
        int best = 0;//假设走不动
        for (int i = 0; i < 3; i++) {
            int nx = x + dx[i];
            int ny = y + dy[i];
            if (nx < 0 || ny < 0 || nx >= m || ny >= n) continue;
            if (grid[nx][ny] <= grid[x][y]) continue;
            best = max(best, 1 + dfs(grid, nx, ny));//从3个方向里面找到能走的最远的方向
        }
        return memo[x][y] = best;
    }
```
**T1391 检查有效路径:** 接口的定义！
```C++
    // 4 个方向：0=下, 1=上, 2=右, 3=左
    int dx[4] = {1, -1, 0, 0};
    int dy[4] = {0, 0, 1, -1};
    // 反方向（如果我往方向 i 走了，对方相当于从反方向 opp[i] 接收）
    int opp[4] = {1, 0, 3, 2};
    // canGo[类型][方向]：当前街道能否往某个方向出去
    // 方向编号同上：0=下, 1=上, 2=右, 3=左
    bool canGo[7][4] = {
        {false, false, false, false},  
        {false, false, true,  true},   // 1: 左右
        {true,  true,  false, false},  // 2: 上下
        {true,  false, false, true},   // 3: 左下
        {true,  false, true,  false},  // 4: 右下
        {false, true,  false, true},   // 5: 左上
        {false, true,  true,  false},  // 6: 右上
    };
    vector<vector<bool>> vis;
    bool dfs(vector<vector<int>>& grid, int x, int y) {
        int m = grid.size(), n = grid[0].size();
        if (x == m - 1 && y == n - 1) return true;   // 到达终点
        vis[x][y] = true;
        int type = grid[x][y];
        for (int i = 0; i < 4; i++) {
            if (!canGo[type][i]) continue;           // 当前格子这个方向没接口
            int nx = x + dx[i];
            int ny = y + dy[i];
            if (nx < 0 || ny < 0 || nx >= m || ny >= n) continue;
            if (vis[nx][ny]) continue;
            int nextType = grid[nx][ny];
            if (!canGo[nextType][opp[i]]) continue;  // 对方没有对应的接口
            if (dfs(grid, nx, ny)) return true;
        }
        return false;
    }
```

### BFS
```C++
class Solution {
public:
    int dx[4] = {0, 0, -1, 1};   // 左, 右, 上, 下
    int dy[4] = {-1, 1, 0, 0};
    // 返回从 (start_x, start_y) 出发到其余每个格子的最短距离
    // 走不通的格子距离为 -1
    vector<vector<int>> bfsGrid(vector<vector<char>>& grid, int start_x, int start_y) {
        int m = grid.size();
        int n = grid[0].size();
        vector<vector<int>> dis(m, vector<int>(n, -1)); // -1 表示还没访问
        queue<pair<int, int>> q;
        
        dis[start_x][start_y] = 0; // 起点距离为 0
        q.push({start_x, start_y});
        
        while (!q.empty()) {
            auto [x, y] = q.front();
            q.pop();
            for (int i = 0; i < 4; i++) {
                int nx = x + dx[i];
                int ny = y + dy[i];
                if (nx < 0 || ny < 0 || nx >= m || ny >= n) continue; // 越界
                if (grid[nx][ny] != '.') continue; // 不可走（根据题意改）
                if (dis[nx][ny] != -1) continue; // 已访问过
                dis[nx][ny] = dis[x][y] + 1; // 距离 = 父节点 + 1
                q.push({nx, ny});
            }
        }
        return dis;
    }
};
```

**多源BFS** VS **单源BFS**
单源 BFS：你只在一个地方点了一堆篝火。火势均匀地向四周蔓延（一层层往外扩散）。你要问的是“某个地点距离这堆火有多远”。

多源 BFS：你在好几个地方同时点起了篝火。所有的火堆都以相同的速度同时向外蔓延。你要问的是“某个地点距离最近的那堆火有多远”。

>核心区别：单源是求到一个*固定起点的距离*，多源是求到所有起点中*最近的那个的距离*。

> 核心的判断：
原题："对每个 A，找最近的 B"
反问："从所有 B 一起出发，第一次到达每个 A 的距离是多少？"
如果两个问题**等价**（因为距离是对称的），就可以用多源 BFS。

>信号词 1："每个 X 到最近 Y 的距离"
关键词：每个、最近、最短距离
例子：每个1到最近0的距离（LC 542）

> 信号词 2："最远的最近距离" / "最大化最小距离"
先算每个点的"最近距离"，再取全局最大值。
关键词：最远的最近、最大化最小
例子：找一个海洋，使它到最近陆地的距离最大（LC 1162）

> 信号词 3："多少步 / 多少时间能覆盖所有"
多个源同时向外扩散，问最后一个点被覆盖的时间。
关键词：多少分钟、多少步覆盖、多久变化完
例子：腐烂橘子多少分钟全部烂完（LC 994）


**T562 01矩阵：**
原思路：
> 对每个 1，找最近的 0 的距离

多源BFS思路：
> 从所有 0 同时出发扩散，第一次到达每个 1 时的距离就是"1 到最近 0 的距离"

**T1293 网格中的最小路径：** 三维数组应用
```C++
    int dx[4] = {0, 0, 1, -1};
    int dy[4] = {1, -1, 0, 0};
    int shortestPath(vector<vector<int>>& grid, int k) {
        int m = grid.size(), n = grid[0].size();
        if (m == 1 && n == 1) return 0;   // 起点即终点
        // 剪枝：k 上限就是 m+n-3
        k = min(k, m + n - 3);
        // 三维 vis：vis[x][y][rest] 表示 (x,y) 剩余 rest 次消除机会时是否访问过
        vector<vector<vector<bool>>> vis(m, vector<vector<bool>>(n, vector<bool>(k + 1, false)));
        // 队列元素：{x, y, rest}
        queue<tuple<int, int, int>> q;
        q.push({0, 0, k});
        vis[0][0][k] = true;
        int step = 0;
        while (!q.empty()) {
            step++;                              // 即将处理下一层
            int sz = q.size();                   // 本层节点数
            for (int cnt = 0; cnt < sz; cnt++) {
                auto [x, y, rest] = q.front();
                q.pop();
                for (int i = 0; i < 4; i++) {
                    int nx = x + dx[i];
                    int ny = y + dy[i];
                    if (nx < 0 || ny < 0 || nx >= m || ny >= n) continue;
                    
                    if (grid[nx][ny] == 0) {
                        // 空地：剩余次数不变
                        if (vis[nx][ny][rest]) continue;
                        if (nx == m - 1 && ny == n - 1) return step;
                        vis[nx][ny][rest] = true;
                        q.push({nx, ny, rest});
                    } else {
                        // 障碍：需要消除一次
                        if (rest == 0) continue;                  // 没次数了
                        if (vis[nx][ny][rest - 1]) continue;      // 已访问过
                        if (nx == m - 1 && ny == n - 1) return step;
                        vis[nx][ny][rest - 1] = true;
                        q.push({nx, ny, rest - 1});
                    }
                }
            }
        }
        return -1;   // BFS 结束还没到终点
    }
```
