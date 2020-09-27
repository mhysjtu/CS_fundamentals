## 问题描述
给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。

## 题解
- 利用二叉搜索树的特性，用节点与根的差是否异号来判断公共祖先
  - 若异号，则根节点为最近公共祖先
  - 若同号，则递归继续查找
- 后续可看236-二叉树的最近公共祖先
### 方法1：递归版本
- **注意递归前要加return**
```cpp
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        //利用二叉搜索树的特性，用节点与根的差是否异号来判断公共祖先
        //若异号，则根节点为最近公共祖先
        //若同号，则递归继续查找
        if((root->val - p->val)*(root->val - q->val)<=0){
            return root;
        }else if((root->val - p->val)<0 && (root->val - q->val)<0){
            return lowestCommonAncestor(root->right, p, q);
        }else{
            return lowestCommonAncestor(root->left, p, q);
        }
    }
};

//简洁版本
//后面两个else可以合并为一个
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        //利用二叉搜索树的特性，用节点与根的差是否异号来判断公共祖先
        //若异号，则根节点为最近公共祖先
        //若同号，则递归继续查找
        if((root->val - p->val)*(root->val - q->val)<=0)
            return root;
        else 
            return lowestCommonAncestor(((root->val-p->val)<0)?(root->right):(root->left), p, q);
    }
};
```

### 方法2：迭代版本
```cpp
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        //利用二叉搜索树的特性，用节点与根的差是否异号来判断公共祖先
        //若异号，则根节点为最近公共祖先
        //若同号，则递归继续查找
        //迭代版本
        while((root->val - p->val)*(root->val - q->val)>0)
            root = ((root->val-p->val)<0)?(root->right):(root->left);
        return root;
    }
};
```