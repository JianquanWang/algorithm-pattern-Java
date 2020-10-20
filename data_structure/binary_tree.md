# 二叉树

## 知识点

### 二叉树遍历

**前序遍历**：**先访问根节点**，再前序遍历左子树，再前序遍历右子树
**中序遍历**：先中序遍历左子树，**再访问根节点**，再中序遍历右子树
**后序遍历**：先后序遍历左子树，再后序遍历右子树，**再访问根节点**

注意点

- 以根访问顺序决定是什么遍历
- 左子树都是优先右子树
#### 树结构

```java
public class TreeNode {
	int val;
	TreeNode left;
	TreeNode right;
	TreeNode(int x){ val = x; }
}
```
#### 前序递归

```java
public void preOrder(TreeNode root)  {
    if (root == null){
        return;
    }
    // 先访问根再访问左右
    System.out.println(root.val);
    preOrder(root.Left);
    preOrder(root.Right);
}

```

#### 前序非递归

用Deque模拟栈, 因为Stack类是遗留类, 不推荐使用. 需要使用栈的地方都用Deque模拟.

```java
class Solution{
    public List<Integer> preOrderTraversal(TreeNode root){
        if (root == null){
            return new LinkedList<>();
        }
        List<Integer> res = new LinkedList<>();
        Deque<TreeNode> stack = new LinkedList<>();
        stack.addFirst(root);
        while(!stack.isEmpty()){
            TreeNode node = stack.removeFirst();
            res.add(node.val);
            if (node.right != null){
                stack.addFirst(node.right);
            }
            if (node.left != null){
                stack.addFirst(node.left);
            }
        }
        return res;
    }
}
```

#### 中序非递归

```java
// 思路：通过stack 保存已经访问的元素，用于原路返回
class Solution{
    public List<Integer> inOrderTraversal(TreeNode root){
        if(root == null){
            return new LinkedList<>();
        }
        List<Integer> res = new LinkedList<>();
        Deque<TreeNode> stack = new LinkedList<>();
        TreeNode node = root;
        while(node != null || !stack.isEmpty()){
            while(node != null){
                stack.addFirst(node);
                node = node.left;
            }
            node = stack.removeFirst();
            res.add(node.val);
	        node = node.right;
        }
        return res;
    }
}
```

#### 后序非递归

前序是中左右, 后序是左右中, 只要将前序改成中右左, 再倒序出栈就可以变成左右中7

```java
public class solution{
    public List<Integer> postOrderTraversal(TreeNode root){
        if(root == null){
            return new LinkedList<>();
        }
        LinkedList<Integer> res = new LinkedList<>();
        Deque<TreeNode> stack = new LinkedList<>();
        Deque<TreeNode> stack2 = new LinkedList<>();
        stack.addFirst(root);
        while(!stack.isEmpty()){
            TreeNode node = stack.removeFirst();
            stack2.addFirst(node.val);
            if(node.left != null){
                stack.addFirst(node.left);
            }
            if(node.right != null){
                stack.addFirst(node.right);
            }
        }
        while(!stack2.isEmpty()){
            res.add(stack2.removeFirst().val);
        }
        return res;
    }
}
```



#### DFS 深度搜索-从上到下

```java
public class Solution{
    public List<Integer> dfsUpToDown(TreeNode root){
        List<Integer> res = new LinkedList<>();
        dfs(root, res);
        return res;
    }
    
    public void dfs(TreeNode node, List<Integer> res){
        if(node == null) return;
        res.add(node.val);
        dfs(node.left, res);
        dfs(node.right, res);
    }
}
```

#### DFS 深度搜索-从下向上（分治法）

```java
// V2：通过分治法遍历
public class Solution{
    public List<Integer> preOrderTraversal(TreeNode root){
        return divideAndConquer(root);
    }
    public List<Integer> divideAndConquer(TreeNode node){
        List<Integer> result = new LinkedList<>();
        if(node == null){return null;}
        // 分治
        List<Integer> left = divideAndConquer(node.left);
        List<Integer> right = divideAndConquer(node.right);
        // 合并
        result.add(node.val);
        if(left != null) result.addAll(left);
        if(right != null) result.addAll(right);
        return result;
    }
}
```

注意点：

> DFS 深度搜索（从上到下） 和分治法区别：前者一般将最终结果通过指针参数传入，后者一般递归返回结果最后合并

#### BFS 层次遍历

```java
public class Solution{
    public List<Integer> levelOrder(TreeNode root){
        List<Integer> res = new LinkedList<>();
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while(!queue.isEmpty()){
            // size 记录当前层有多少元素(遍历当前层, 再添加下一层)
            int size = queue.size();
            for( int i = 0; i < size; ++i){
                TreeNode node = queue.poll();
                res.add(node.val);
                if(node.left != null) queue.add(node.left);
                if(node.right != null) queue.add(node.right);
            }
        }
        return res;
    }
}
```

### 分治法应用

先分别处理局部，再合并结果

适用场景

- 快速排序
- 归并排序
- 二叉树相关问题

分治法模板

- 递归返回条件
- 分段处理
- 合并结果

```java
// 伪代码模板
public class Solution{
    popublic ResultType traversal(TreeNode root){
        if(root == null){
            // do something and return
        }
        // divide
        ResultType left = traversal(root.left);
        ResultType right = traversal(root.right);
        // conquer
        ResultType result = Merge from left and right;
        
        return result;
    }
}
```

#### 典型示例

```java
// V2：通过分治法遍历二叉树
public class Solution{
    public List<Integer> preOrderTraversal(TreeNode root){
        return divideAndConquer(root);
    }
    public List<Integer> divideAndConquer(TreeNode node){
        List<Integer> result = new LinkedList<>();
        if(node == null) return null;
        // divide
        List<Integer> left = divideAndConquer(node.left);
        List<Integer> right = divideAndConquer(node.right);
        //conquer
        result.add(node.val);
        result.addAll(left);
        result.addAll(right);
    	return result;
    }
    
}
```

#### 归并排序  

```java
public class Solution{
    public int[] mergeSortRoot(int[] nums){
        mergeSort(nums, 0, nums.length - 1);
        return nums;
    }
    private void mergeSort(int[] nums, int l, int r){
        if(l < r){
            int mid = (l + r) / 2;
            mergeSort(nums, l, mid);
            mergeSort(nums, mid, r);
            merge(nums, l, mid, r);
        }
    }
    private void merge(int[] nums, int l, int mid, int r){
        int i = l, j = mid + 1, k = l;
        int[] tmp = new int[nums.length];
        while (i <= mid && j <= r){
            if (nums[i] > nums[j]){
                tmp[k++] = nums[j++];
            }else{
                tmp[k++] = nums[i++];
            }
        }
        while (i <= mid){
            tmp[k++] = nums[i++];
        }
        while (j <= r){
            tmp[k++] = nums[j++];
        }
        for(i = l; i <= r; i++){
            nums[i] = tmp[i];
        }    
    }
} 
```

注意点

> 递归需要返回结果用于合并

#### 快速排序  

```java
public class Solution{
    public int[] quickSort(int[] nums){
        quickSort(nums, 0, nums.length - 1);
        return nums;
    }
    private void quickSort(int[] nums, int start, int end){
        if(start < end){
            int pivot = partition(nums, start, end);
            quickSort(nums, 0, pivot - 1);
            quickSort(nums, pivot + 1, end);
        }
    }
    
    private int partition(int[] nums, int start, int end){
    	int target = nums[end];
        int i = start;
        for(int j = start; j < end; j++){
            if(nums[j] < target){
                swap(nums, i, j);
                i++;
            }
        }
        swap(nums, i, end);
        return i;
    }
    private void swap(int[] nums, int i, int j){
        int tmp = nums[i];
        nums[i] = nums[j];
        nums[j] = tmp;
    }
}
```

注意点：

> 快排由于是原地交换所以没有合并过程
> 传入的索引是存在的索引（如：0、length-1 等），越界可能导致崩溃

常见题目示例

#### maximum-depth-of-binary-tree

[maximum-depth-of-binary-tree](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

> 给定一个二叉树，找出其最大深度。

思路：分治法

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) return 0;
        return Math.max(maxDepth(root.left), maxDepth(root.right)) + 1;
    }
}
```

#### balanced-binary-tree

[balanced-binary-tree](https://leetcode-cn.com/problems/balanced-binary-tree/)

> 给定一个二叉树，判断它是否是高度平衡的二叉树。

思路：分治法，左边平衡 && 右边平衡 && 左右两边高度 <= 1，
遍历一遍，如果有左右两边高度 > 1，则不是高度平衡的二叉树。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    private boolean result = true;
    public boolean isBalanced(TreeNode root) {
        if (root == null) return true;
        maxDepth(root);
        return result;
    }

    private int maxDepth(TreeNode node){
        if(node == null){
            return 0;
        }
        int leftDepth = maxDepth(node.left);
        int rightDepth = maxDepth(node.right);
        if(Math.abs(leftDepth - rightDepth) > 1){
            result = false;
        }
        return 1 + Math.max(leftDepth, rightDepth);
    }
}
```

注意

> 一般工程中，结果通过两个变量来返回，不建议用一个变量表示两种含义

#### binary-tree-maximum-path-sum

[binary-tree-maximum-path-sum](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)

> 给定一个**非空**二叉树，返回其最大路径和。

思路：分治法，递归计算出左右子节点的最大贡献值，只有在最大贡献值大于 0 时，才会选取对应子节点； 节点的最大路径和取决于该节点的值与该节点的左右子节点的最大贡献值；每次递归返回该节点的最大贡献值。

```java
public class Solution {
    private int maxSum = Integer.MIN_VALUE;
    
    public int maxPathSum(TreeNode root){
        maxGain(root);
        return maxSum;
    }
    
    private int maxGain(TreeNode node){
        if (node == null) return 0;
        // 递归计算左右子节点的最大贡献值
        // 只有在最大贡献值大于 0 时，才会选取对应子节点
        int leftGain = Math.max(maxGain(node.left), 0);
        int rightGain = Math.max(maxGain(node.right), 0);
        // 节点的最大路径和取决于该节点的值与该节点的左右子节点的最大贡献值
        int currMaxSum = node.val + leftGain + rightGain;
        // 更新最大路径和
        maxSum = Math.max(maxSum, currMaxSum);
        // 返回节点的最大贡献值
        return node.val + Math.max(leftGain, rightGain);
    }
}
```

#### lowest-common-ancestor-of-a-binary-tree

[lowest-common-ancestor-of-a-binary-tree](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

> 给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

思路：分治法，有左子树的公共祖先或者有右子树的公共祖先，就返回子树的祖先，否则返回根节点

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if(root == null) return root;
        if(root == p || root == q) return root;

        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);

        if(left != null & right != null){
            return root;
        }
        if (left != null) return left;
        if (right != null) return right;
        
        return null;
    }

}
```

### BFS 层次应用

#### binary-tree-level-order-traversal

[binary-tree-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

> 给你一个二叉树，请你返回其按  **层序遍历**  得到的节点值。 （即逐层地，从左到右访问所有节点）

思路：用一个队列记录一层的元素，然后扫描这一层元素添加下一层元素到队列（一个数进去出来一次，所以复杂度 O(logN)）

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        if (root == null){
            return new LinkedList<>();
        }
        List <List<Integer>> res = new ArrayList<>();
        Queue<TreeNode> queue = new LinkedList<>();

        queue.add(root);
        while(!queue.isEmpty()){
            int length =  queue.size();
            List<Integer> subres = new ArrayList<>();
            for(int i = 0; i < length; ++i){
                TreeNode node = queue.poll();
                if(node.left != null) queue.add(node.left);
                if(node.right != null) queue.add(node.right);
                subres.add(node.val);
            }
            res.add(subres);
        }
        return res;

    }
}
```

#### binary-tree-level-order-traversal-ii

[binary-tree-level-order-traversal-ii](https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/)

> 给定一个二叉树，返回其节点值自底向上的层次遍历。 （即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历）

思路：在层级遍历的基础上，翻转一下结果即可

```java
class Solution {
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        if(root == null) return new ArrayList<>();

        List<List<Integer>> res = new ArrayList<>();
        Queue<TreeNode> queue = new LinkedList<>();

        queue.add(root);
        while(!queue.isEmpty()){
            int length = queue.size();
            List<Integer> subres = new ArrayList<>();
            for(int i = 0; i < length; i++) {
                TreeNode node = queue.poll();
                if (node.left != null) queue.add(node.left);
                if (node.right != null) queue.add(node.right);
                subres.add(node.val);
            }
            res.add(subres);
        }
        
        // reverse
        List<List<Integer>> reverseRes = new ArrayList<>();
        for(int i = res.size()-1; i >= 0; i--){
            reverseRes.add(res.get(i));
        }
        return reverseRes;
    }
}
```

#### binary-tree-zigzag-level-order-traversal

[binary-tree-zigzag-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

> 给定一个二叉树，返回其节点值的锯齿形层次遍历。Z 字形遍历

```java
class Solution {
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        if(root == null) return new ArrayList<>();
        List<List<Integer>> res = new ArrayList<>();
        Queue<TreeNode> queue = new LinkedList<>();
        
        boolean fromLeftToRight = true;
        queue.add(root);
        while(!queue.isEmpty()){
            int length = queue.size();
            List<Integer> subRes = new ArrayList<>();
            for(int i = 0; i < length; i++){
                TreeNode node = queue.poll();
                if(node.left != null) queue.add(node.left);
                if(node.right != null) queue.add(node.right);
                subRes.add(node.val);
            }
            if(fromLeftToRight) res.add(subRes);
            else {
                Collections.reverse(subRes);
                res.add(subRes);
            }
            fromLeftToRight = !fromLeftToRight;
        }
        return res;
    }
}
```

### 二叉搜索树应用

#### validate-binary-search-tree

[validate-binary-search-tree](https://leetcode-cn.com/problems/validate-binary-search-tree/)

> 给定一个二叉树，判断其是否是一个有效的二叉搜索树。

思路 1：中序遍历，检查结果列表是否已经有序

思路 2：分治法，判断左 MAX < 根 < 右 MIN

```java
// v1
class Solution {
    public boolean isValidBST(TreeNode root) {
        if (root == null) return true;
        List<Integer> res = new ArrayList<>();
        Deque<TreeNode> stack = new LinkedList<>();

        TreeNode node = root;
        while(node != null || !stack.isEmpty()) {
            while(node != null) {
                stack.offerFirst(node);
                node = node.left;
            }
            node = stack.pollFirst();
            res.add(node.val);

            node = node.right;
            
        }

        for(int i = 0; i < res.size() - 1; i++) {
            if(res.get(i) >= res.get(i+1)) return false;
        }
        return true;
    }
}
// 优化
class Solution {
    public boolean isValidBST(TreeNode root) {
        if (root == null) return true;
        double preVal = - Double.MAX_VALUE;
        Deque<TreeNode> stack = new LinkedList<>();
        TreeNode node = root;
        while(node != null || !stack.isEmpty()) {
            while(node != null) {
                stack.offerFirst(node);
                node = node.left;
            }
            node = stack.pollFirst();
            if (node.val <= preVal) return false;
            preVal = node.val;
            node = node.right;
        }
        return true;
    }
}
```

```java
// v2分治法
class Solution {
    
    public boolean isValidBST(TreeNode root) {
        return helper(root, null, null);
    }
    private boolean helper(TreeNode node, Integer lower, Integer upper){
        if(node == null) return true;
        int val = node.val;
        if((lower != null && lower >= val) || (upper != null && upper <= val)){
            return false;
        }
        return helper(node.left, lower, val) && helper(node.right, val, upper);
    }
}
```

#### insert-into-a-binary-search-tree

[insert-into-a-binary-search-tree](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)

> 给定二叉搜索树（BST）的根节点和要插入树中的值，将值插入二叉搜索树。 返回插入后二叉搜索树的根节点。

思路：找到最后一个叶子节点满足插入条件即可

```java
// DFS查找插入位置
class Solution {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        if (root == null){
            root = new TreeNode(val);
            return root;
        }
        if ( val > root.val) {
            root.right = insertIntoBST(root.right, val);
        }else{
            root.left = insertIntoBST(root.left, val);
        }
        return root;
    }
}
```

## 总结

- 掌握二叉树递归与非递归遍历
- 理解 DFS 前序遍历与分治法
- 理解 BFS 层次遍历

## 练习

- [ ] [maximum-depth-of-binary-tree](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)
- [ ] [balanced-binary-tree](https://leetcode-cn.com/problems/balanced-binary-tree/)
- [ ] [binary-tree-maximum-path-sum](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)
- [ ] [lowest-common-ancestor-of-a-binary-tree](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)
- [ ] [binary-tree-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)
- [ ] [binary-tree-level-order-traversal-ii](https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/)
- [ ] [binary-tree-zigzag-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)
- [ ] [validate-binary-search-tree](https://leetcode-cn.com/problems/validate-binary-search-tree/)
- [ ] [insert-into-a-binary-search-tree](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)
