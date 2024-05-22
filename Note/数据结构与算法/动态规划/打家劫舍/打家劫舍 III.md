![[Pasted image 20240505213851.png]]
![[Pasted image 20240505213942.png|347]]

## 思路

这道题目和[[打家劫舍Ⅰ]]，[[打家劫舍 II]]也是如出一辙，只不过这个换成了树。

对于树的话，首先就要想到遍历方式，前中后序（深度优先搜索）还是层序遍历（广度优先搜索）。**本题一定是要后序遍历，因为通过递归函数的返回值来做下一步计算**。

与之前两道题一样，关键是要讨论当前节点抢还是不抢。

如果抢了当前节点，两个孩子就不能动，如果没抢当前节点，就可以考虑抢左右孩子（**注意这里说的是“考虑”**）。

### 动态规划

而动态规划其实就是使用状态转移容器来记录状态的变化，这里可以**使用一个长度为2的数组**，**记录当前节点偷与不偷所得到的的最大金钱**。

**这道题目算是树形dp的入门题目，因为是在树上进行状态转移，我们在讲解二叉树的时候说过递归三部曲，那么下面我以递归三部曲为框架，其中融合动规五部曲的内容来进行讲解**。

1. 确定递归函数的参数和返回值

这里我们要求一个节点 偷与不偷的两个状态所得到的金钱，那么返回值就是一个长度为2的数组。这里的返回数组就是dp数组。

所以dp数组（dp table）以及下标的含义：**下标为0记录不偷该节点所得到的的最大金钱**，**下标为1记录偷该节点所得到的的最大金钱**。

**所以本题dp数组就是一个长度为2的数组！**

那么有同学可能疑惑，长度为2的数组怎么标记树中每个节点的状态呢？**别忘了在递归的过程中，系统栈会保存每一层递归的参数**。

2. 确定终止条件

在遍历的过程中，如果遇到空节点的话，很明显，无论偷还是不偷都是0，所以就返回dp。这也相当于dp数组的初始化

3. 确定遍历顺序

首先明确的是使用后序遍历。 因为要通过递归函数的返回值来做下一步计算。通过递归左节点，得到左节点偷与不偷的金钱。通过递归右节点，得到右节点偷与不偷的金钱。

4. 确定单层递归的逻辑

如果是偷当前节点，那么左右孩子就不能偷，`val1 = cur->val + left[0] + right[0];` （**如果对下标含义不理解就再回顾一下dp数组的含义**）

如果不偷当前节点，那么左右孩子就可以偷，至于到底偷不偷一定是选一个最大的，所以：`val2 = max(left[0], left[1]) + max(right[0], right[1]);`

最后当前节点的状态就是`{val2, val1};` 即：`{不偷当前节点得到的最大金钱，偷当前节点得到的最大金钱}`。

5. 举例推导dp数组

以示例1为例，dp数组状态如下：（**注意用后序遍历的方式推导**）。**最后头结点就是取下标0和下标1的最大值就是偷得的最大金钱**。

![|500](https://code-thinking-1253855093.file.myqcloud.com/pics/20230203110031.png)

#### 整体代码

树形dp：

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public int rob(TreeNode root) {
        int[] res = robTree(root);
        return Math.max(res[0], res[1]);
    }
    public int[] robTree(TreeNode root){
        int[] dp = new int[2]; // 0:不偷，1:偷
        if(root == null){
            return dp;
        }
        int[] left = robTree(root.left); //都是数组
        int[] right = robTree(root.right);
        // 不偷：Max(左孩子不偷，左孩子偷) + Max(右孩子不偷，右孩子偷)
        dp[0] = Math.max(left[0], left[1]) + Math.max(right[0], right[1]);
        // 偷：左孩子不偷+ 右孩子不偷 + 当前节点偷
        dp[1] = root.val + left[0] + right[0]; 
        return dp;
    } 
}
```

将树遍历成数组，然后用[[打家劫舍Ⅰ]]的方法，这种方法是行不通的！见下：

![[Pasted image 20240506170544.png|600]]