## 二叉树
自底向上写法：
```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if (root == nullptr) {
            return 0;
        }
        int l_depth = maxDepth(root‑>left);
        int r_depth = maxDepth(root‑>right);
        return max(l_depth, r_depth) + 1;
    }
};
```

自顶向下写法：前序遍历+深度记录。

T112（路径总和）：dfs极简版
```cpp
bool hasPathSum(TreeNode* root, int targetSum) {
    // 1．当前节点为空，这条路径走不通，直接false
    if (!root) return false;
    // 2．用减法代替累加，targetSum是值传递，每次生成新副本
    targetSum ‑= root‑>val;
    // 3．判断是否是叶子节点（左右都没孩子）
    if (!root‑>left && !root‑>right) {
        // 走到叶子，如果减完刚好等于0 → 路径和等于原targetSum，返回true
        return targetSum == 0;
    }
    // 4．递归左、右子树，只要任意一边存在合法路径就返回true
    return hasPathSum(root‑>left, targetSum) || hasPathSum(root‑>right, targetSum);
}
```

T965（单值二叉树）
```cpp
class Solution {
public:
    bool dfs(TreeNode *root,int x){
        if(!root) return true;
        if(root‑>val!=x) return false;
        bool leftOk = dfs(root‑>left,x);
        bool rightOk = dfs(root‑>right,x);
        return leftOk && rightOk;
    }
    bool isUnivalTree(TreeNode* root) {
        return dfs(root,root‑>val);
    }
};
```
> 核心：理解`&&`操作，只要一边是false其他全是false!!!

T951（翻转等价二叉树）
```cpp
class Solution {
public:
    bool flipEquiv(TreeNode* root1, TreeNode* root2) {
        if (!root1 && !root2) return true;
        if (!root1 || !root2) return false;
        if (root1‑>val != root2‑>val) return false;
        bool same = flipEquiv(root1‑>left, root2‑>left) && flipEquiv(root1‑>right, root2‑>right);
        bool swap = flipEquiv(root1‑>left, root2‑>right) && flipEquiv(root1‑>right, root2‑>left);
        return same || swap;
    }
};
```

T110（平衡二叉树）
```cpp
class Solution {
    int get_height(TreeNode* node) {
        if (node == nullptr) {
            return 0;
        }
        int left_h = get_height(node‑>left);
        int right_h = get_height(node‑>right);
        if (left_h == ‑1 || right_h == ‑1 || abs(left_h ‑ right_h) > 1) {
            return ‑1;
        }
        return max(left_h, right_h) + 1;
    }
public:
    bool isBalanced(TreeNode* root) {
        return get_height(root) != ‑1;
    }
};
```
> 中间进行剪枝操作

T2265(统计值等于子树平均值的节点数）
```cpp
class Solution {
public:
    int ans=0;
    pair<long long, int> dfs(TreeNode* root) {
        if (!root) return {0, 0};
        auto l=dfs(root‑>left);
        auto r=dfs(root‑>right);
        long long sum=root‑>val+l.first+r.first;
        int cnt=1+l.second+r.second;
        if (sum/cnt==root‑>val) {
            ans++;
        }
        return {sum, cnt};
    }
    int averageOfSubtree(TreeNode* root) {
        ans=0;
        dfs(root);
        return ans;
    }
};
```
> 关键是用pair统计自底向上有多少个节点！

T814（二叉树剪枝）：本质上是后序遍历
```cpp
class Solution {
public:
    TreeNode* pruneTree(TreeNode* root) {
        if (!root) {
            return nullptr;
        }
        root‑>left = pruneTree(root‑>left);
        root‑>right = pruneTree(root‑>right);
        if (!root‑>left && !root‑>right && !root‑>val) {
            return nullptr;
        }
        return root;
    }
};
```

T437（路径总和III）：关键是想起有个东西叫前缀和！

T236（二叉树的公共祖先）：
> 递归理解：因为是递归，使用函数后可认为左右子树已经算出结果，这句话要记住，道出了递归的精髓
- 当前节点是空节点 → 返回空
- 当前节点是 p → 返回当前节点
- 当前节点是 q → 返回当前节点
- 其它：
    - 左右子树都找到：返回当前节点
    - 只有左子树找到：返回递归左子树的结果
    - 只有右子树找到：返回递归右子树的结果
    - 左右子树都没有找到：返回空节点

二叉搜索树：一般是中序遍历。

T530（二叉搜索树中的最小绝对值差）：通过中序遍历记录数值，把数组从小到大依次排好，再比较相邻两个数（因此记录pre是很有必要的）

T501（二叉搜索树的众数）：
```cpp
// 正确遍历哈希表：迭代器，不用下标i
for(auto& pair : p){
    mx=max(pair.second, mx);
}
for(auto& pair : p){
    if(pair.second == mx) ans.push_back(pair.first);
}
```
> T98&&T99！!！
