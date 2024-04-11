![[Pasted image 20240411144305.png]]
![[Pasted image 20240411144327.png|575]]
![[Pasted image 20240411144343.png|316]]

这个在[[路径总和]]的基础上多了储存路径的操作！所以还要**多一个回溯**！

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
    public List<List<Integer>> pathSum(TreeNode root, int targetSum) {
        List<Integer> path = new ArrayList<>();
        List<List<Integer>> res = new ArrayList<>();
        if(root == null){
            return res;
        }
        getPath(root, targetSum, path, res);
        return res;
    }
    public void getPath(TreeNode root, int targetSum, List<Integer> path, List<List<Integer>> res){
        path.add(root.val); // 先加节点
        int count = targetSum;
        if(root.left == null && root.right == null && root.val == count){
            res.add(new ArrayList<>(path));  // 新列表
            return;
        }
        if(root.left != null){
            getPath(root.left, count - root.val, path, res);  // 回溯1
            path.remove(path.size() - 1);  // 回溯2
        }
        if(root.right != null){
            getPath(root.right, count - root.val, path, res);
            path.remove(path.size() - 1);
        }
    }
}
```

注：
- 使用 `new ArrayList<>(path)` 的目的是创建一个新的列表对象，并将当前路径 `path` 的内容复制到新的列表中，因为`path`的值会随时变化；
- `path`中找到一条合适的路径或者遍历完一条路径后要原路返回，所以一定要回溯：`path.remove(path.size() - 1);` 和[[二叉树的所有路径]]的回溯是一样的；
- [[路径总和]]就不一样了，它只要求判断，没有记录路径，只用回溯一个`count`值。