## 一． 滑动窗口（2026.3.29—4.14）
### （1）定长滑窗套路
窗口右端点在 i 时，由于窗口长度为 k，所以窗口左端点为 i−k+1。

**入‑更新‑出**
1. **入**：下标为 i 的元素进入窗口，更新相关统计量。如果窗口左端点 i−k+1<0，则尚未形成第一个窗口，重复第一步。
2. **更新**：更新答案。一般是更新最大值/最小值。
3. **出**：下标为 i−k+1 的元素离开窗口，更新相关统计量，为下一个循环做准备。

```cpp
class Solution {
public:
    int maxScore(vector<int>& nums, int k) {
        int ans = 0;
        int n = nums.size();
        int sum = 0;
        int l = 0, r = 0;
        while(r < n) {
            sum += nums[r];
            if(r - l + 1 == k) {
                ans = max(ans, sum);
                sum -= nums[l++];
            }
            r++;
        }
        return ans;
    }
};
```

> T2481：map可用来保存数值出现次数`map<int,int>p;` `p[i]=j`表示i出现的次数
> √T2461：关键在于在外面定义`int left=0;`目的是记录上一次左边界的位置，就不能再像前面几道题一样直接`l=r‑k+1`了
> √T1423（可获得的最大点数）：涉及到贪心的问题（但是dp似乎会超时），不能单纯用deque来做，只能得到局部最优解。可视为定长滑动窗口的变式。还可以把数组看做一个环，即因为每次取数字只能在开头和末尾，其实就是一个滑动窗口在`cardPointsSize‑k`到`cardPointsSize+k`的区间滑动。

### （2）不定长滑窗
主要分为三类：求最长子数组，求最短子数组，求子数组个数。
滑动窗口相当于在维护一个队列。右指针的移动可以视作入队，左指针的移动可以视作出队。

> T3：与2481,2461有相似之处，但是不定长滑窗关键是逐渐缩小窗口（注意这里只动left!!没有动right!!!）

```cpp
for (int right = 0; right < n; right++) {
    char c = s[right];
    cnt[c]++;
    while (cnt[c] > 1) { // 窗口内有重复字母
        cnt[s[left]]--; // 移除窗口左端点字母
        left++; // 缩小窗口
    }
}
```

> T2779题目要求的「由相等元素组成的最长子序列」，相当于选出若干闭区间，这些区间的交集不为空。
> 排序后，选出的区间是连续的，我们只需考虑最左边的区间 $[x‑k, x+k]$ 和最右边的区间 $[y‑k, y+k]$，如果这两个区间的交集不为空，那么选出的这些区间的交集就不为空。也就是要满足
> $$x+k \ge y‑k$$
> 即
> $$y‑x \le 2k$$
> 于是原问题等价于：
> - 排序后，找最长的连续子数组，其最大值减最小值 $\le 2k$。由于数组是有序的，相当于子数组的最后一个数减去子数组的第一个数 $\le 2k$。

√T2024(考试的最大困扰度)：关键在于k的使用，如果出现了T与F的值都大于了K证明此时不能再维护修改后的连续性，因此要移动left缩小窗口。

❌T2779（数组的最大美丽值）：本质上是数组求交集，然后用不定窗口就行了。

√T1658（将x减到0的最小操作数）：正难则反（我用正也能做，类似T1423，可以把数组拼接起来让窗口在中间部分滑动）
> 移除的是 nums 最左边或最右边的元素，那么剩下的元素是什么？是 nums 的连续子数组。
> 移除的元素和 x + 剩余的元素和 = nums 的所有元素之和 s。所以剩余的元素和 = s−x。
> 问题变成：从 nums 中找最长的子数组（这样移除的数尽量少），满足子数组的元素和恰好等于 s−x。

T76（最小覆盖子串）VS T3298（子数组型）：关键是如何判断覆盖没有，即窗口里面每一个字符出现次数都大于t里面的，如果涵盖就继续把left向右移动直到无法涵盖为止（！！！要记录ans_left与ans_right）

### （3）子数组个数
与（1），（2）的区别是这个求个数，前两个是求min或者max。

> 遇到子数组问题先思考它是越长越能满足条件还是越短越能！！！

1. **越短越合法**：一般要写 `ans += right ‑ left + 1`。（在while外）（例如乘积规定要小于几问题，数字越少越能满足）
内层循环结束后，`[left,right]` 这个子数组是满足题目要求的。由于子数组越短，越能满足题目要求，所以除了 `[left,right]`，`[left+1,right]`,`[left+2,right]`,…,`[right,right]` 都是满足要求的。也就是说，当右端点固定在 right 时，左端点在`left,left+1,left+2,…,right` 的所有子数组都是满足要求的，这一共有 `right−left+1`。

2. **越长越合法**：一般要写 `ans += left`。（while外）(在子数组中元素至少出现k次问题，数组越长数字出现次数越多越能满足)
内层循环结束后，`[left,right]` 这个子数组是不满足题目要求的，但在退出循环之前的最后一轮循环，`[left−1,right]` 是满足题目要求的。由于子数组越长，越能满足题目要求，所以除了 `[left−1,right]`，还有 `[left−2,right]`,`[left−3,right]`,…,`[0,right]` 都是满足要求的。也就是说，当右端点固定在 right 时，左端点在 `0,1,2,…,left−1` 的所有子数组都是满足要求的，这一共有 `left` 个。我们关注的是 `left−1` 的合法性，而不是 `left`。

### （4）恰好型滑动窗口
例如，要计算有多少个元素和恰好等于 k 的子数组，可以把问题变成：
计算有多少个元素和 ≥k 的子数组，计算有多少个元素和≥k+1 的子数组。（一定是有等号的！！！）
答案就是元素和 ≥k 的子数组个数，减去元素和 ≥k+1 的子数组个数。这里把 > 转换成 ≥，从而可以把滑窗逻辑封装成一个函数 solve，然后用 `solve(k)−solve(k+1)` 计算。

总结：**「恰好」可以拆分成两个「至少」**，也就是两个「越长越合法」的滑窗问题。

> 注：也可以把问题变成 ≤k 减去 ≤k−1，即两个「至多」。可根据题目选择合适的变形方式。
> 注：也可以把两个滑动窗口合并起来，维护同一个右端点 right 和两个左端点 left1和 left2，这种写法叫做三指针滑动窗口。

### 其他
T825（适龄的朋友）：关键是如果发现`cntWindow>0` ，说明存在可以发送好友请求的用户：
- 当前这`cnt[ageX]`个用户可以与`cntWindow`个用户发送好友请求，根据乘法原理，这有`cnt[ageX]*cntWindow`个。
- 其中有`cnt[ageX]`个好友请求是自己发给自己的，不符合题目要求，要减去。

```cpp
class Solution {
public:
    int numFriendRequests(vector<int>& ages) {
        int cnt[121]{};
        for (int age : ages) {
            cnt[age]++;
        }
        int ans = 0, cnt_window = 0, age_y = 0;
        for (int age_x = 0; age_x < 121; age_x++) {
            cnt_window += cnt[age_x];
            if (age_y * 2 <= age_x + 14) { // 不能发送好友请求
                cnt_window -= cnt[age_y];
                age_y++;
            }
            if (cnt_window > 0) { // 存在可以发送好友请求的用户
                ans += cnt[age_x] * cnt_window - cnt[age_x];
            }
        }
        return ans;
    }
};
```

## 二．双指针
P1471(数组中的k个最强值）：思路：
1. 首先对数组进行排序，确定中位数
2. 初始化两个指针，分别指向数组的最左端和最右端
3. 比较左右指针指向的元素与中位数差值的绝对值（即强度）：
    - 如果左指针元素强度更大，选择左指针元素并将左指针右移
    - 如果右指针元素强度更大或相等，选择右指针元素并将右指针左移
4. 重复步骤 3 直到收集到 k 个元素

原理：排序后离中位数越近强度越大，所以从`l=0,r=n‑1`相中靠拢时强度都在减弱，所以只要从两侧判断就好。

P633（平方数之和）：超出时间限制是因为找到解后没有立即跳出循环！！！（同时为了避免溢出要用`long long!`）

P2824（统计和小于目标的下标对数的数目）：关键是l,r从两侧向中间滑动，遇见`temp<target`直接加`（r‑l）`！！！（因为数组经过了排序）

P2563（统计公平数对数目）：三指针（关键是先确定一个，不要三个一起动，不然很难做）

P15（三数之和）：其实和P2563挺像的，关键都在于固定一个数，把三数之和看做两数之和，但是关键在于剪枝优化！

P1577（数的平方等于两数乘积的方法和）：有点像P15变式，但是重复数字需要判断！！！不然会计数记漏掉。。（而且存在多种情况，这个题多思考）（不能用哈希，思考为什么）

```cpp
long long Find(vector<int>& nums1, vector<int>& nums2){
    int n1=nums1.size(),n2=nums2.size();
    sort(nums2.begin(),nums2.end());
    long long ans=0;
    for(int i=0;i<n1;i++){
        long long target = (long long)nums1[i] * nums1[i];
        int j=0,k=n2-1;
        while(j<k){
            long long sum = (long long)nums2[j] * nums2[k];
            if(sum > target){
                k--;
            }else if(sum < target){
                j++;
            }else{
                int cntL = 1;
                while(j+cntL < k && nums2[j+cntL] == nums2[j]) cntL++;
                int cntR = 1;
                while(k‑cntR > j && nums2[k‑cntR] == nums2[k]) cntR++;
                if(nums2[j] == nums2[k]){
                    long long total = cntL + cntR;
                    ans += total * (total ‑ 1) / 2;
                    break;
                }else{
                    ans += (long long)cntL * cntR;
                }
            }
        }
    }
    return ans;
}
```
> 中间的`while(j<k)`用于循环计数，即防止找到一种j和k就退出，会导致漏计

T1616（分割两个字符串得回文串）：错点在于看见前后缀匹配就直接true了，但是还要判断中间的一串是否是回文的！！！（可以写个函数封装起来判断某一个区间是否是回文串）

T1574（删除最短子数组使数组有序）：前缀和后缀分别进行维护，即从各方向都要是非递减的，否则就删去（可以固定一端）

```cpp
int findLengthOfShortestSubarray(vector<int>& arr) {
    int n = arr.size();
    // 1．找最长非递减后缀，right为后缀起点
    int right = n ‑ 1;
    while (right > 0 && arr[right ‑ 1] <= arr[right]) {
        right‑‑;
    }
    // 整个数组已经非递减，直接返回0
    if (right == 0) return 0;

    int ans = right; // 初始答案：删除前缀[0, right‑1]
    // 2．枚举前缀终点left，匹配后缀起点right
    for (int left = 0; left < n; left++) {
        // 前缀必须非递减，否则break
        if (left > 0 && arr[left] < arr[left ‑ 1]) break;
        // 找到第一个满足arr[left] <= arr[right]的位置
        while (right < n && arr[left] > arr[right]) {
            right++;
        }
        // 删除[left+1, right‑1]，长度为right ‑ left ‑ 1
        ans = min(ans, right ‑ left ‑ 1);
    }
    return ans;
}
```

## 三．分组循环
适用场景：按照题目要求，数组会被分割成若干组，且每一组的判断/处理逻辑是一样的。

核心思想：
- 外层循环负责遍历组之前的准备工作（记录开始位置），和遍历组之后的统计工作（更新答案最大值）。
- 内层循环负责遍历组，找出这一组最远在哪结束。

模版：
```cpp
n = nums.size();
int i = 0;
while(i < n){
    int start = i;
    while（i<n&&......）{
        i+=1;
        ......
    }
    // 从 start 到 i‑1 是一组
    // 下一组从 i 开始，无需 i += 1
}
```

T2760（最长奇偶子数组）：
```cpp
class Solution {
public:
    int longestAlternatingSubarray(vector<int>& nums, int threshold)
    {
        int n = nums.size();
        int ans = 0, i = 0;
        while (i < n) {
            if (nums[i] > threshold || nums[i] % 2) {
                i++; // 直接跳过
                continue;
            }
            int start = i; // 记录这一组的开始位置
            i++; // 开始位置已经满足要求，从下一个位置开始判断
            while (i < n && nums[i] <= threshold && nums[i] % 2 != nums[i‑1] % 2) {
                i++;
            }
            // 从 start 到 i‑1 是满足题目要求的（并且无法再延长的）子数组
            ans = max(ans, i ‑ start);
        }
        return ans;
    }
};
```

T2110（平滑下降的股票）：有时候不一定需要`if（......）{...... continue;}`因为有时候只有一个字符时也要放进去，所以while下面直接 `int start=i;i++;`就可以了，直接标记当前的开始，再判断条件。

T3350（检测相邻递增子串）：去看灵神解法！！！太牛逼了（关键是找到全局的递增与记录pre_cnt以及ans怎么取很重要）



