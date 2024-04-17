![[Pasted image 20240417205620 1 1.png|625]]
![[Pasted image 20240417205748 1 1.png]]

**提示:**
- `2 <= k <= 9`
- `1 <= n <= 60`

## 思路

相对于[[组合问题+剪枝]]，无非就是多了一个求和等于k的限制，而整个集合已经是固定的了`[1,...,9]`。

本题k相当于树的深度，9（因为整个集合就是9个数）就是树的宽度。

例如 k = 2，n = 4的话，选取过程如图，只有最后取到集合（1，3）和为4 符合条件：

![216.组合总和III|775](https://code-thinking-1253855093.file.myqcloud.com/pics/20201123195717975.png)

### 回溯三部曲

- **确定递归函数参数**

和[[组合问题+剪枝]]一样，先定义定义path 和 result。接下来还需要如下参数：

- targetSum（int）目标和，也就是题目中的n。
- k（int）就是题目中要求k个数的集合。
- sum（int）为已经收集的元素的总和，也就是path里元素的总和。
- index（int）为下一层for循环搜索的起始位置。

其实这里**sum这个参数也可以省略**，每次targetSum减去选取的元素数值，然后判断如果targetSum为0了，说明收集到符合条件的结果了，我这里为了直观便于理解，还是加一个sum参数。

- 确定终止条件

所以如果`path.size() == k`了，就终止，用result收集当前的结果。

- **单层搜索过程**

处理过程就是 path收集每次选取的元素，相当于树型结构里的边，sum来统计path里元素的总和。

**别忘了处理过程 和 回溯过程是一一对应的，处理有加，回溯就要有减！**

### 剪枝

其实和[[组合问题+剪枝]]差不多：

![216.组合总和III1|725](https://code-thinking-1253855093.file.myqcloud.com/pics/2020112319580476.png)

已选元素总和如果已经大于n（图中数值为4）了，那么往后遍历就没有意义了，直接剪掉。

那么剪枝的地方可以放在递归函数开始的地方，剪枝代码如下：

```java
if (sum > targetSum) { // 剪枝操作
    return;
}
```

当然这个剪枝也可以放在 调用递归之前，即放在这里，只不过要记得 要回溯操作给做了。

```java
for(int i = index; i <= 9 - (k - path.size()) + 1; i++){ // 剪枝
    path.add(i);
    sum += i;
    if (sum > targetSum) { // 剪枝操作
        path.removeLast(); // 剪枝之前先把回溯做了
        sum -= i; // 剪枝之前先把回溯做了
        return;
    }
    backtracking(k, i + 1, target);
    path.removeLast();
    sum -= i;
}
```

和[[组合问题+剪枝]]一样，for循环的范围也可以剪枝，`i <= 9 - (k - path.size()) + 1`就可以了。

传sum的整体代码：

```java
class Solution {
    List<List<Integer>> res = new ArrayList<List<Integer>>();
    List<Integer> path = new ArrayList<Integer>(); 
    public List<List<Integer>> combinationSum3(int k, int n) {
        backtracking(k, 1, n, 0);
        return res;
    }
    public void backtracking(int k, int index, int target, int sum){
        if(sum > target){
            return;
        }
        if(path.size() == k && sum == target){
            res.add(new ArrayList<>(path));
            return;
        }
        for(int i = index; i <= 9 - (k - path.size()) + 1; i++){ 
            path.add(i);
            sum += i;
            backtracking(k, i + 1, target, sum);
            path.removeLast();
            sum -= i;
        }
    }
}
```

如果定义sum是全局变量，就可以不传sum，但一定要**记得回溯sum**！！！：

```java
class Solution {
    List<List<Integer>> res = new ArrayList<List<Integer>>();
    List<Integer> path = new ArrayList<Integer>(); 
    int sum = 0;
    public List<List<Integer>> combinationSum3(int k, int n) {
        backtracking(k, 1, n);
        return res;
    }
    public void backtracking(int k, int index, int target){
        if(sum > target){
            return;
        }
        if(path.size() == k && sum == target){
            res.add(new ArrayList<>(path));
            return;
        }
        for(int i = index; i <= 9 - (k - path.size()) + 1; i++){ 
            path.add(i);
            sum += i;
            backtracking(k, i + 1, target);
            path.removeLast();
            sum -= i;
        }
    }
}
```
