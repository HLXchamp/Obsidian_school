注：此题和Leetcode的[[所有可能的路径]]基本是一样的。

![[Pasted image 20241121095131.png]]
![[Pasted image 20241121095206.png|600]]

我们分为邻接表和邻接矩阵两种写法，每个写法有一点区别。

### 邻接矩阵

有n个节点，矩阵可以写成**n+1 x n+1**的，这样的话就可以把节点和矩阵下标对应；如果像我下面写成**n x n**的话要考虑节点和索引的关系（**索引等于节点-1**）。

一定要注意输入输出的条件！空格要用双引号，注意换行！

```java
import java.util.*;
 
public class Main {
    static List<List<Integer>> res = new ArrayList<>();
    static List<Integer> path = new ArrayList<>();
 
    public static void dfs(int[][] graph, int x, int length) {
        // 当遍历到最后一个节点时就返回，最后一个节点的索引是length-1
        if (x == length - 1) {
            res.add(new ArrayList<>(path));
            return;
        }
        for (int i = 0; i < length; i++) {
            if (graph[x][i] == 1) {
                path.add(i + 1);
                dfs(graph, i, length);
                path.remove(path.size() - 1);
            }
        }
    }
 
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int n = scanner.nextInt(); // 节点数
        int m = scanner.nextInt(); // 边数
        int[][] graph = new int[n][n]; // 邻接矩阵生成
        while(m-- > 0){
            int i = scanner.nextInt();
            int j = scanner.nextInt();
            graph[i - 1][j - 1] = 1; // 是有向图
        }
        path.add(1); // 先加入1这个节点
        dfs(graph, 0, n); // 1节点对应的索引是0
        if(res.isEmpty()){
            System.out.println(-1);
        }
        for (List<Integer> temp : res) {
            for (int i = 0; i < temp.size(); i++) {
                // 如果是最后一个节点，直接输出，不加空格
                if (i == temp.size() - 1) {
                    // 注意区分print和println
                    System.out.print(temp.get(i));
                } else {
                    System.out.print(temp.get(i) + " ");
                }
            }
            System.out.println(); // 换行
        }
    }
}
```

### 邻接表

邻接表就要用**列表**而不是数组表示了（每个节点关联的边的数量未知）。

用列表的话要**初始化列表**！这里创建n+1个，以后索引就直接用节点的值，不用减一了。

```java
for (int i = 0; i <= n; i++) {
    graph.add(new ArrayList<>());
}
```

整体代码：

```java
import java.util.*;
 
public class Main<p> {
    static List<List<Integer>> res = new ArrayList<>();
    static List<Integer> path = new ArrayList<>();
 
    public static void dfs(List<List<Integer>> graph, int x, int n) {
        if (x == n) {
            res.add(new ArrayList<Integer>(path));
            return;
        }
        for (int i = 0; i < graph.get(x).size(); i++) {
            path.add(graph.get(x).get(i));
            dfs(graph, graph.get(x).get(i), n);
            path.remove(path.size() - 1);
        }
    }
 
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int n = scanner.nextInt(); // 节点数
        int m = scanner.nextInt(); // 边数
        List<List<Integer>> graph = new ArrayList();
        // 初始化图
        for (int i = 0; i <= n; i++) {
            graph.add(new ArrayList<>());
        }
        while (m-- > 0) {
            int i = scanner.nextInt();
            int j = scanner.nextInt();
            graph.get(i).add(j);
        }
        path.add(1);
        dfs(graph, 1, n);
        if (res.isEmpty()) {
            System.out.println(-1);
        } else {
            for (List<Integer> temp : res) {
                for (int i = 0; i < temp.size(); i++) {
                    if (i == temp.size() - 1) {
                        System.out.print(temp.get(i));
                    } else {
                        System.out.print(temp.get(i) + " ");
                    }
                }
                System.out.println();
            }
        }
    }
}
```
