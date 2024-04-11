![[Pasted image 20240328105132.png|450]]
![[Pasted image 20240328105249.png]]

注意看N叉树的定义：孩子节点都是**children**，和二叉树不同！
```java
// Definition for a Node.
class Node {
    public int val;
    public List<Node> children;

    public Node() {}

    public Node(int _val) {
        val = _val;
    }

    public Node(int _val, List<Node> _children) {
        val = _val;
        children = _children;
    }
};
```

和二叉树的层序遍历只变了个加入孩子节点的方法：

```java
class Solution {
    public List<List<Integer>> levelOrder(Node root) {
        List<List<Integer>> resList = new ArrayList<List<Integer>>();
        int size = 0;
        Queue<Node> queue = new ArrayDeque<>();
        if(root == null){
            return resList;
        }
        else{
            queue.offer(root);
        }
        Node node = root;
        while(!queue.isEmpty()){
            size = queue.size();
            List<Integer> res = new ArrayList<>();
            while(size > 0){
                node = queue.poll();
                res.add(node.val);
                if(node.children != null){
                    for (Node child : node.children) {
                        queue.offer(child); // 将子节点入队
                    }
                }
                size--;
            }
            resList.add(res);
        }
        return resList;
    }
}
```

要学会用for循环加入所有孩子节点的方式：
```java
for (Node child : node.children) {
    queue.offer(child); // 将子节点入队
}
```
