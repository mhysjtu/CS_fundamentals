## 问题描述
根据一棵树的前序遍历与中序遍历构造二叉树。

## 题解
+ 一个结论，前序/后序+中序序列可以唯一确定一棵二叉树，所以自然而然可以用来建树。
- [c++] 递归、区间分治。
- 这种区间分治的题目，使用左闭右闭区间更加方便
- 重建二叉树的基本思路就是先构造根节点，再构造**左子树**，接下来构造**右子树**，其中，构造**左子树**和**右子树**是一个子问题，递归处理即可。因此我们只关心如何构造根节点，以及如何递归构造左子树和右子树。
- 递归函数的设计上，仍旧采用**左闭右闭**对数组局部进行描述。即一个数组，使用 3 个变量描述：
  - 数组本身 arr
  - 数组的起始位置 lo
  - 数组的结束位置 hi
- 因此，数组实际元素就是 arr[lo..hi] 这个范围（注意这是一个左闭右闭的区间，如果你喜欢左闭右开区间，那你可能重新考虑一下 lo、hi 的值如何计算）。在有些语言里，使用切片的方式表示，会更加方便，如python的list[:1]。
- 递归函数原型 TreeNode* buildTree(vector<int>& preorder, int lo1, int hi1, vector<int>& inorder, int lo2, int hi2)，它的主要参数相比原来题目中的原型**多了数组范围描述变量，因为要做区间分治嘛**。
- 后面的故事就很简单了，三元组 (preorder, lo1, hi1) 描述的前序遍历数组，以及三元组 (inorder, lo2, hi2) 描述的中序遍历数组，如何从它们重建二叉树？递归的说法就是：
  - 创建根节点
  - 递归创建左子树
  - 递归创建右子树
- 递归创建左子树，无非就是再构造一个新的前序遍历的三元组 (preorder, lo1+1, lo1+mid-lo2) 以及 (inorder, lo2, mid-1)，其中 mid 是当前 inorder 中 root 的位置。

### 方法1 递归 C++
- **递归结束的终止条件，一定要加！！**，否则stackoverflow
```cpp
class Solution {
public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        return buildTree(preorder, 0, preorder.size()-1, inorder, 0, inorder.size()-1);
    }
private:
    TreeNode* buildTree(vector<int>& preorder, int lo1, int hi1, vector<int>& inorder, int lo2, int hi2){
        //递归结束的终止条件，一定要加！！
        if(lo1>hi1 || lo2>hi2) return nullptr;
        int root_val = preorder[lo1];
        int mid_idx = lo2;
        for(int i = lo2; i<=hi2; ++i){
            if(inorder[i]==root_val){
                mid_idx = i;
                break;
            }
        }
        //while (inorder[mid] != preorder[l1]) ++mid;
        TreeNode* result = new TreeNode(root);

        //         left,    root,   right
        //inorder [mid-lo2,    1, hi2-mid]
        //         root,    left,   right
        //preorder[1,    mid-lo2, hi2-mid]
        result->left = buildTree(preorder, lo1+1, lo1+mid_idx-lo2, inorder, lo2, mid_idx-1);
        result->right = buildTree(preorder, lo1+mid_idx-lo2+1, hi1, inorder, mid_idx+1, hi2);
    }
};
```

### 方法2 递归 python
```python
class Solution(object):
    def buildTree(self, preorder, inorder):
        """
        :type preorder: List[int]
        :type inorder: List[int]
        :rtype: TreeNode
        """
        if not preorder: 
            return None
        
        x = preorder.pop(0) #删除第0个，默认为-1最后一个
        node = TreeNode(x) 
        i = inorder.index(x) # inorder中root的位置，index方法很简洁
        
        #注意preorder删除了一个，切片时要注意
        #取一种情况画出来区间便可知道如何切片
        node.left = self.buildTree(preorder[:i], inorder[:i]) #python中递归要加self.
        node.right = self.buildTree(preorder[i:], inorder[i+1:])
        return node
```