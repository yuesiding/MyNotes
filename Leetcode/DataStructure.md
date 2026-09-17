# 三． 数据结构（4.20）
## （一） 常用枚举
1.双变量问题可以枚举右边的，转换成单变量问题，也就是在左边查找是否有，这可以用哈希表维护。**枚举右，维护左**

> Q：如何避免哈希表覆盖问题:边枚举边写入（如T1两数之和）
```cpp
vector<int> twoSum(vector<int>& nums, int target) {
    int n=nums.size();
    unordered_map<int,int>p;
    for(int i=0;i<n;i++){
        int x=target‑nums[i];
        if(p.count(x)){
            return {i,p[x]};
        }
        p[nums[i]]=i;
    }
    return {0,0};
}
```

T624（数组列表中的最大距离）：
```cpp
int maxDistance(vector<vector<int>>& arrays) {
    // 用INT_MAX/2和INT_MIN/2，防止减法溢出（比如INT_MAX ‑ INT_MIN会溢出）
    int mn = INT_MAX / 2, mx = INT_MIN / 2;
    int ans = 0;
    for(auto& a : arrays) {
        // 这一步是核心：当前数组和前面的所有数组计算最大可能差
        ans = max(ans, a.back() ‑ mn, mx ‑ a[0]);
        // 更新全局的最小值和最大值（为下一轮循环做准备）
        mn = min(mn, a[0]);
        mx = max(mx, a.back());
    }
    return ans;
}
```

T2342（数位和相等数对的最大值）：
```cpp
int maximumSum(vector<int>& nums) {
    int ans=0;
    unordered_map<int,int>p;
    for(int i=0;i<nums.size();i++){
        int x=GetSum(nums[i]);
        if(p.count(x)){
            ans=max(ans,p[x]+nums[i]);
            if(nums[i]>p[x]) p[x]=nums[i];
        }else{
            p[x]=nums[i];
        }
    }
    return ans==0 ? ‑1 :ans;
}
```
> ！！！用哈希维护最大值！！！

T1128（等价多米诺骨牌数量）：怎么维护二维的domino？用`cnt[][]`就可以了（同时由于题目前置可调换domino中a,b的顺序，因此可以用`auto [a,b] = minmax(x,y)`  // 即把x,y传进去，小的给a，大的给b）

3. **枚举中间**（主要对于三个或四个变量问题）
下面T2909这个方法主要用于三元组！

Eg:T2909：（元素和最小的山形三元组）
```cpp
int minimumSum(vector<int>& nums) {
    int n = nums.size();
    vector<int> suf(n); // 后缀最小值
    suf[n ‑ 1] = nums[n ‑ 1];
    for (int i = n ‑ 2; i > 1; i‑‑) {
        suf[i] = min(suf[i + 1], nums[i]);
    }
    int ans = INT_MAX;
    int pre = nums[0]; // 前缀最小值
    for (int j = 1; j < n ‑ 1; j++) {
        if (pre < nums[j] && nums[j] > suf[j + 1]) { // 山形
            ans = min(ans, pre + nums[j] + suf[j + 1]); // 更新答案
        }
        pre = min(pre, nums[j]);
    }
    return ans == INT_MAX ? ‑1 : ans;
}
```
> 关键是枚举中间以及用前后缀记录最大/最小值（注意后缀遍历方向！！！）

类似：T3583（先记录一边的哈希，然后在遍历过程中撤销一边同时更新另一边哈希）

## （二） 前缀和
```cpp
pre_min[0]=sum[0];
for(int i=1;i<=n;i++){
    pre_min[i]=min(pre_min[i‑1],sum[i]);
}
suf_max[n]=sum[n];
for(int i=n‑1;i>=0;i‑‑){
    suf_max[i]=max(suf_max[i+1],sum[i]);
}
```
> 注意后缀和那里是`i+1`!!!并且遍历范围顺序不一样

T974
```cpp
vector<int>sum(nums.size()+1);
for(int i=0;i<nums.size();i++){
    sum[i+1]=sum[i]+nums[i];
}
unordered_map<int,int>p;
p[0]=1;
int ans=0;
for(int i=1;i<=nums.size();i++){
    int x = (sum[i] % k + k) % k;
    if(p.count(x)) {
        ans+=p[x];
    }
    p[x]++;
}
return ans;
```
> 易忘记！！记录前缀和为0的情况，初始为1（同时注意负数取模情况）

T525（连续数组）：关键是把0看做‑1因此呢当就可以用前缀和来计算（有点像同余定理，但不完全是）

T1477（找到两个和为目标值且不重复的子数组）：关键是不能重复！！！因此要用一个min_len来记录【到每个位置为止，最短的子数组长度】意思是如果有不重叠的，就记录下来。

T2602（使数组元素全部相等的最小操作数）：排序+二分+前缀和优化！！！（T2615）

```cpp
ranges::sort(nums);
int n = nums.size();
vector<long long> sum(n + 1); // 前缀和
for (int i = 0; i < n; i++) {
    sum[i + 1] = sum[i] + nums[i];
}
int m = queries.size();
vector<long long> ans(m);
for (int i = 0; i < m; i++) {
    int q = queries[i];
    long long j = ranges::lower_bound(nums, q) ‑ nums.begin();
    long long left = q * j ‑ sum[j]; // 蓝色面积
    long long right = sum[n] ‑ sum[j] ‑ q * (n ‑ j); // 绿色面积
    ans[i] = left + right;
}
return ans;
```

### 二维前缀和
定义 `sum[i+1][j+1]`表示左上角为`a[0][0]`，右下角为`a[i][j]`的子矩阵元素和。
采用这种定义方式，无需单独处理第一行/第一列的元素和。
$$sum[i+1][j+1] = sum[i+1][j] + sum[i][j+1] - sum[i][j] + a[i][j]$$

计算任意子矩阵元素和：
设子矩阵左上角为 $a[r_1][c_1]$，右下角为 $a[r_2][c_2]$。
$$子矩阵元素和 = sum[r_2+1][c_2+1] - sum[r_2+1][c_1] - sum[r_1][c_2+1] + sum[r_1][c_1]$$

## （三） 差分
对于数组 $a$，定义其差分数组（difference array）为
$$
d[i]=
\begin{cases}
a[0],& i = 0\\
a[i] - a[i‑1],& i \ge 1
\end{cases}
$$

**性质1**：从左到右累加 $d$ 中的元素，可以得到数组 $a$。

**性质2**：如下两个操作是等价的。
- 把 $a$ 的子数组 $a[i],a[i+1],\dots,a[j]$ 都加上 $x$。
- 把 $d[i]$ 增加 $x$，把 $d[j+1]$ 减少 $x$。

利用性质 2，我们只需要 $O(1)$ 的时间就可以完成对 $a$ 的子数组的操作。最后利用性质 1 从差分数组复原出数组 $a$。

> 注：也可以这样理解，$d[i]$ 表示把下标 $\ge i$ 的数都加上 $d[i]$。

同时对于原数组 $a$ 是差分数组 $d$ 的前缀和：
- $a[0] = d[0]$
- $a[i] = d[0] + d[1] + ... + d[i]$

**区间修改的关键操作**
如果要对原数组 $a$ 的区间 $[l, r]$ 加上 $k$，只需要：
1. $d[l] += k$：表示从 $l$ 开始，所有后续元素都增加 $k$
2. $d[r+1] -= k$：表示从 $r+1$ 开始，不再增加 $k$，抵消前面的影响

最后要求得原数组只需要对$d[i]$求前缀和即可。

T2381（字母移位）：关键是区分清楚什么时候才需要初始化差分数组什么时候不需要！！！
> 一般来说，查询区间修改需要初始化，而记录偏移量等则不太需要。

T995（K连续位的最小翻转次数）：巧妙差分，记录翻转的奇偶性即可。

## （四） 栈
T2216（美化数组的最小删除数）：根据栈的奇偶性决定删除/保留多少个元素
- 栈为偶数时：下一个位置是奇数下标，只需要保留 1 个元素（满足`nums[偶数下标] != nums[奇数下标]`），其余 `l‑1` 个元素删除，栈变为奇数。
- 栈为奇数且只有 1 个元素时：保留这个元素，栈变为偶数，不影响后续条件。
- 栈为奇数且有多个元素时：可以保留 2 个元素（一个放在奇数下标，一个放在偶数下标），其余 `l‑2` 个元素删除，栈的奇偶性不变。

T856（括号的分数）：这个题不用直接对括号进行入栈出栈，关键在记录每一层的分数，如此一来才能得知每一层分数以及是否该翻倍。

T1249（移除无效的括号）：别忘了最后栈不为空时要从后往前删去多余的左括号！！！（而不是直接输出空）

T1209（删除字符串中所有相邻重复元素）：想到了把要操作字符串中相邻重复元素删除，但是没有想到可以一起把之前在栈里面出现过的相同字符串也加进去，这样子更方便处理！！！

T394（字符串解码）:递归最简单。

## （五） 队列
### 单调队列
样题T239（滑动窗口最大值）
```cpp
// 计算 nums 的每个长为 k 的窗口的最大值
// 时间复杂度 O(n)，其中 n 是 nums 的长度
vector<int> maxSlidingWindow(const vector<int>& nums, int k) {
    int n = nums.size();
    vector<int> ans(n ‑ k + 1); // 窗口个数
    deque<int> q; // 双端队列

    for (int i = 0; i < n; i++) {
        // 1．右边入
        while (!q.empty() && nums[q.back()] <= nums[i]) {
            q.pop_back(); // 维护 q 的单调性
        }
        q.push_back(i); // 注意保存的是下标，这样下面可以判断队首是否离开窗口

        // 2．左边出
        int left = i ‑ k + 1; // 窗口左端点
        if (q.front() < left) { // 队首离开窗口
            q.pop_front();
        }

        // 3．在窗口左端点处记录答案
        if (left >= 0) {
            // 由于队首到队尾单调递减，所以窗口最大值就在队首
            ans[left] = nums[q.front()];
        }
    }
    return ans;
}
```

T1438（绝对值不超过限制的最长连续子数组）：不同的是这里窗口移动里面是while（类T2764，T3835）
```cpp
int longestSubarray(vector<int>& nums, int limit) {
    int n=nums.size();
    int left=0,ans=0;
    deque<int>q_min,q_max;
    for(int i=0;i<n;i++){
        while(!q_min.empty()&&nums[i]<=nums[q_min.back()]){
            q_min.pop_back();
        }
        q_min.push_back(i);
        while(!q_max.empty()&&nums[i]>=nums[q_max.back()]){
            q_max.pop_back();
        }
        q_max.push_back(i);

        while (nums[q_max.front()] ‑ nums[q_min.front()] > limit) {
            left++;
            if (q_min.front() < left) { // 队首不在窗口中
                q_min.pop_front();
            }
            if (q_max.front() < left) { // 队首不在窗口中
                q_max.pop_front();
            }
        }
        ans=max(ans,i‑left+1);
    }
    return ans;
}
```

T862（和至少为k的最短子数组）：这道题坑点在于数组中有负数，因此一般的滑窗用不了，故而采取前缀和+双端队列（双端队列里存前缀和的下标！！！） 这道题告诉我前缀和这个东西不能忘记！！!

## (六) 堆（优先队列）
如果是数值+索引怎么设置小顶堆？
```cpp
priority_queue<pair<int,int>, vector<pair<int,int>>,greater<pair<int,int>>>heap;
```

T3296（移山所需最小秒数）：tuple（pair的通用版）怎么用？Auto[]里面是啥：结构化绑定
```cpp
long long minNumberOfSeconds(int mountainHeight, vector<int>& workerTimes) {
    long long ans=0;
    priority_queue<tuple<long long, long long, int>, vector<tuple<long long, long long, int>>, greater<>> q;
    for(int i=0;i<workerTimes.size();i++){
        q.push({workerTimes[i],workerTimes[i],workerTimes[i]});
    }
    while(mountainHeight‑‑){
        // 工作后总用时，当前工作（山高度降低 1）用时，workerTimes[i]
        auto [total, cur, base] = q.top(); q.pop();
        ans = total;
        q.emplace(total + cur + base, cur + base, base);
    }
    return ans;
}
```

T2931（购买物品的最大开销）：由于数组内部是排序好了的，所以就可以堆堆进行边走边处理！
```cpp
class Solution {
public:
    long long maxSpending(vector<vector<int>>& values) {
        int m = values.size(), n = values[0].size();
        priority_queue<pair<int, int>, vector<pair<int, int>>, greater<>> pq;
        for (int i = 0; i < m; i++) {
            pq.emplace(values[i].back(), i);
        }
        long long ans = 0;
        for (int d = 1; d <= m * n; d++) {
            auto [v, i] = pq.top();
            pq.pop();
            ans += (long long) v * d;
            values[i].pop_back();
            if (!values[i].empty()) {
                pq.emplace(values[i].back(), i);
            }
        }
        return ans;
    }
};
```
> 类似题目T1705（吃苹果）

T264（丑数）：暴力枚举会超出时间限制！！要用小根堆+set集合去重的方式降低时间复杂度（因为丑数是由`{2,3,5}`所生成的，所以可以一边出队一边入队）

> 优先队列是弱项！！！多刷几次多做错题！！！！！！

## （七） 链表
基础：如何遍历一个链表？

T2181（合并零之间的节点）：
法一：虚拟头结点法
```cpp
ListNode* mergeNodes(ListNode* head) {
    // 1．创建虚拟头结点，构建新链表
    ListNode* dummy = new ListNode(0);
    ListNode* tail = dummy;
    ListNode* cur = head‑>next;
    int sum = 0;
    while (cur != nullptr) {
        if (cur‑>val == 0) {
            tail‑>next = new ListNode(sum);
            tail = tail‑>next;
            sum = 0;
        } else {
            sum += cur‑>val;
        }
        cur = cur‑>next;
    }
    ListNode* newHead = dummy‑>next;
    delete dummy;
    return newHead;
}
```

法二：直接在原链表上进行修改，随后进行截断
```cpp
class Solution {
public:
    ListNode* mergeNodes(ListNode* head) {
        auto tail = head;
        for (auto cur = head‑>next; cur‑>next; cur = cur‑>next) {
            if (cur‑>val) {
                tail‑>val += cur‑>val;
            } else {
                tail = tail‑>next;
                tail‑>val = 0;
            }
        }
        // 注：这里没有 delete 剩余节点，可以自行补充
        tail‑>next = nullptr;
        return head;
    }
};
```

T82（删除重复节点II）主要是怎么让代码更简便！！！
```cpp
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        ListNode dummy(0, head);
        auto cur = &dummy;
        while (cur‑>next && cur‑>next‑>next) {
            int val = cur‑>next‑>val;
            if (cur‑>next‑>next‑>val == val) { // 后两个节点值相同
                // 值等于 val 的节点全部删除
                while (cur‑>next && cur‑>next‑>val == val) {
                    cur‑>next = cur‑>next‑>next;
                }
            } else {
                cur = cur‑>next;
            }
        }
        return dummy.next;
    }
};
```

T237(删除指定节点node)，没给出head怎么办？
> PS：如何让自己在世界上消失，但又不死？ —— 将自己完全变成另一个人，再杀了那个人就行了。

T147（插入节点）
```cpp
class Solution {
public:
    ListNode* insertionSortList(ListNode* head) {
        if (head == nullptr) {
            return head;
        }
        ListNode* dummyHead = new ListNode(0);
        dummyHead‑>next = head;
        ListNode* lastSorted = head;
        ListNode* curr = head‑>next;
        while (curr != nullptr) {
            if (lastSorted‑>val <= curr‑>val) {
                lastSorted = lastSorted‑>next;
            } else {
                ListNode *prev = dummyHead;
                while (prev‑>next‑>val <= curr‑>val) {
                    prev = prev‑>next;
                }
                lastSorted‑>next = curr‑>next;
                curr‑>next = prev‑>next;
                prev‑>next = curr;
            }
            curr = lastSorted‑>next;
        }
        return dummyHead‑>next;
    }
};
```
> 关键是用lastSorted维护已排序的最后值
- 若 `lastSorted.val <= curr.val`，说明 `curr` 应该位于 `lastSorted` 之后，将 `lastSorted` 后移一位，`curr` 变成新的 `lastSorted`。
- 否则，从链表的头节点开始往后遍历链表中的节点，寻找插入 `curr` 的位置。令 `prev` 为插入 `curr` 的位置的前一个节点，进行如下操作，完成对 `curr` 的插入：
```cpp
lastSorted‑>next = curr‑>next
curr‑>next = prev‑>next
prev‑>next = curr
```
