# 二．二分算法（4.14‑4.20）
## （一）二分查找（数组首先得升序排列）
### 1.有序数组查找元素位置（基于lower_bound）
|需求|C++ 写法|不存在时的结果|
|---|---|---|
|≥ x 的第一个元素的下标|`lower_bound(nums.begin(), nums.end(), x) ‑ nums.begin()`|n|
|> x 的第一个元素的下标|`lower_bound(nums.begin(), nums.end(), x + 1) ‑ nums.begin()`|n|
|< x 的最后一个元素的下标|`lower_bound(nums.begin(), nums.end(), x) ‑ nums.begin() ‑ 1`|-1|
|≤ x 的最后一个元素的下标|`lower_bound(nums.begin(), nums.end(), x + 1) ‑ nums.begin() ‑ 1`|-1|

### 2. 有序数组统计元素个数
|需求|C++ 写法|
|---|---|
|< x 的元素个数|`lower_bound(nums.begin(), nums.end(), x) ‑ nums.begin()`|
|≤ x 的元素个数|`lower_bound(nums.begin(), nums.end(), x + 1) ‑ nums.begin()`|
|≥ x 的元素个数|`n ‑ (lower_bound(nums.begin(), nums.end(), x) ‑ nums.begin())`|
|> x 的元素个数|`n ‑ (lower_bound(nums.begin(), nums.end(), x + 1) ‑ nums.begin())`|

### 三种写法对比
|对比项|闭区间 [left, right]|左闭右开 [left, right)|开区间 (left, right)|
|---|---|---|---|
|初始 right|`nums.size() ‑ 1`（有效下标）|`nums.size()`（哨兵）|`nums.size()`（哨兵）|
|初始 left|`0`（有效下标）|`0`（有效下标）|`‑1`（哨兵，越界）|
|循环条件|`while (left <= right)`|`while (left < right)`|`while (left + 1 < right)`|
|right 更新|`right = mid ‑ 1`（跳过 mid）|`right = mid`（保持右开）|`right = mid`（保持开区间）|
|left 更新|`left = mid + 1`（跳过 mid）|`left = mid + 1`（跳过 mid）|`left = mid`（保持开区间）|
|终止时 left|`left > right`，left 是左边界|`left == right`，left 是左边界|`left + 1 == right`，right 是左边界|

T34（基础题）：闭区间写法
```cpp
class Solution {
public:
    int lower_bound(vector<int>&nums,int target){
        int n=nums.size()‑1;
        int l=0,r=n‑1;
        while(l<=r){
            int mid=l+(r‑l)/2;
            if(nums[mid]<target) l=mid+1;
            else if(nums[mid]>=target) r=mid‑1;
        }
        return l;
    }
    vector<int> searchRange(vector<int>& nums, int target) {
        int n=nums.size();
        int start=lower_bound(nums,target);
        if(start==n||nums[start]!=target) return {-1,‑1};
        int end=lower_bound(nums,target+1)‑1;
        return {start,end};
    }
};
```

T2070（查询最大美丽值）：关键是sort函数对二维数组怎么写：
```cpp
static bool cmp(const vector<int>&a,const vector<int>&b){
    return a[0]<b[0];
}
sort(items.begin(),items.end(),cmp);
```

## （二）二分答案
1. **求最小**，题目求什么就二分什么（`return left;`）（关键是bool check函数怎么写）

T1283（使结果不超过阈值的最小除数）：
```cpp
class Solution {
public:
    bool check(vector<int>& nums, int threshold, int m) {
        int sum = 0;
        for (int x : nums){
            sum += (x + m ‑ 1) / m;
            if (sum > threshold) return false;
        }
        return true;
    }
    int smallestDivisor(vector<int>& nums, int threshold) {
        int left = 1;
        int right = *max_element(nums.begin(), nums.end());
        while (left <= right) {
            int mid = left + (right ‑ left) / 2;
            if (check(nums, threshold, mid)) {
                right = mid‑1;
            } else {
                left = mid+1;
            }
        }
        return left;
    }
};
```
> 上取整运算：`(x+m‑1)/m`

2. **求最大**（`return right;`）
> VS求最小：求最小check成立的话就要`r=mid‑1`;以找到更小的，而求最大则要`left=mid+1`,把区间右移来找到更大的，所以`return right`；

T2576（求出最多标记的下标）：二分+贪心+排序；关键是用前k对与后剩下的进行配对，即排序后前mid个小数的第i个，配后mid个大数的第i个。

3. **最小化最大值**：本质是二分答案求最小，二分的 mid 表示上界，好比用一个盖子（上界）去压住最大值，看看能否压住（check 函数）

e.g:T1760（袋子里最少数目的球）：
```cpp
class Solution {
    bool check(vector<int>nums,int maxOperations,long long mid){
        long long total_ops = 0;
        for (int x : nums) {
            if (x > mid) {
                total_ops += (x ‑ 1) / mid;
                if (total_ops > maxOperations) {
                    return false;
                }
            }
        }
        return total_ops <= maxOperations;
    }
public:
    int minimumSize(vector<int>& nums, int maxOperations) {
        long long r=*max_element(nums.begin(),nums.end());
        long long l=1;
        while(l<=r){
            long long mid=l+(r‑l)/2;
            if(check(nums,maxOperations,mid)) r=mid‑1;
            else l=mid+1;
        }
        return (int)l;
    }
};
```
> check的逻辑是什么？看看那个袋子里要拆多少次才能拆到mid，然后加起来统计总共拆分次数（剪枝优化：如果袋子里本来就比mid小，就不用拆了）

4. **第k小/大**
```cpp
class Solution {
    bool check(int m, int n, int k, int x) {
        long long cnt = 0;
        for (int i = 1; i <= m; i++) {
            cnt += min(x / i, n);
            if (cnt >= k) {
                return true;
            }
        }
        return cnt >= k;
    }
public:
    int findKthNumber(int m, int n, int k) {
        int left = 1;
        int right = m * n;
        while (left <= right){
            int mid = left + (right ‑ left) / 2;
            if (check(m, n, k, mid)) right = mid ‑ 1;
            else left = mid + 1;
        }
        return left;
    }
};
```
> `min(x / i, n)` = 乘法表第 i 行中≤x的数字的实际数量（x是我当前猜的数字的最大值）
