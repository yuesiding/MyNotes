## 2013
### C最大的矩形
**单调栈:** 当你在一个一维数组中，需要寻找每个元素的“左/右边界（即左边或右边第一个比它大/小的元素）”，并借此计算区间极值时，就该用单调栈了。
- 三大适用场景：
    - 找边界：下一个更大/更小元素（如：每日温度）。
    - 算区间极值：以当前元素为最值（如最矮）向两边延伸的最大面积（如：直方图最大矩形、接雨水）。
    - 删元素保序：构造字典序最小序列（如：移掉 K 位数字）。
- 核心口诀（避坑指南）：
    - **存下标** ：栈里永远存下标，不存数值（算宽度必须用下标）。
    - 单调性：
        - 找右边**更大**元素，维护**递减栈**（遇到大的就弹出栈顶结算）。
        - 找右边**更小**元素，维护**递增栈**（遇到小的就弹出栈顶结算）。
    - 宽度公式：当前下标 - 弹出后的新栈顶下标 - 1。
    - 哨兵技巧：数组**首尾加0**，省去判空和收尾，代码极简。

```C++
#include<bits/stdc++.h>
typedef long long ll; // 注意：typedef 末尾有分号
using namespace std;

int main(){
    int n;
    cin >> n;
    // 在首尾插入两个高度为0的哨兵，极其好用！
    vector<ll> h(n + 2, 0);
    for(int i = 1; i <= n; i++){
        cin >> h[i];
    }
    
    stack<int> st; // 栈里存的是【下标】，不是高度！
    st.push(0);
    ll ans = 0;
    
    for(int i = 1; i <= n + 1; i++){
        // 当遇到比栈顶矮的柱子时，说明栈顶柱子的右边界确定了
        while(h[i] < h[st.top()]){
            int cur = st.top(); // 当前要计算面积的柱子下标
            st.pop();
            
            // 关键公式：宽度 = 右边界(i) - 左边界(弹出后的新栈顶) - 1
            int width = i - st.top() - 1;
            ll area = h[cur] * width;
            ans = max(ans, area);
        }
        st.push(i);
    }
    
    cout << ans << endl;
    return 0;
}
```

T42 接雨水
```C++
class Solution {
public:
    int trap(vector<int>& height) {
        int n=height.size();
        stack<int>st;
        st.push(0);
        int ans=0;
        for(int i=1;i<n;i++){
            while(!st.empty()&&height[i]>=height[st.top()]){
                int cur=st.top();
                st.pop();
                if(st.empty()) break;
                int left=st.top();//当前左边界
                int h=min(height[left],height[i])-height[cur];//当前小矩形高度
                ans+=h*(i-left-1);//i-left-1是当前小矩形宽度
            }
            st.push(i);
        }
        return ans;
    }
};
```