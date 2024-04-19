![[Pasted image 20240419094455.png|675]]

## 思路

这道题目和[[组合总和]]如下区别：

1. 本题candidates 中的每个数字在每个组合中只能使用一次。
2. 本题数组candidates的元素是有重复的，而[[组合总和]]是无重复元素的数组candidates

最后本题和[[组合总和]]要求一样，解集不能包含重复的组合。

**本题的难点在于区别2中：集合（数组candidates）有重复元素，但还不能有重复的组合**。

一些同学可能想了：我把所有组合求出来，再用set或者map去重，这么做很容易超时！

都知道组合问题可以抽象为树形结构，那么“使用过”在这个树形结构上是有两个维度的，一个维度是同一树枝上使用过，一个维度是同一树层上使用过。**没有理解这两个层面上的“使用过” 是造成大家没有彻底理解去重的根本原因。**

回看一下题目，元素在同一个组合内是可以重复的，怎么重复都没事，但两个组合不能相同。**所以我们要去重的是同一树层上的“使用过”，同一树枝上的都是一个组合里的元素，不用去重**。

为了理解去重我们来举一个例子，`candidates = [1, 1, 2], target = 3,`（方便起见candidates已经排序了）**强调一下，树层去重的话，需要对数组排序！** 选择过程树形结构如图所示：

![40.组合总和II|725](https://code-thinking-1253855093.file.myqcloud.com/pics/20230310000918.png)

可以看到图中，每个节点相对于[[组合总和]]多加了**used数组**，这个used数组下面会重点介绍。

### 回溯三部曲

- **递归函数参数**

与[[组合总和]]套路相同，还需要加一个bool型数组used，用来记录同一树枝上的元素是否使用过。这个集合去重的重任就是used来完成的。

- **递归终止条件**

与[[组合总和]]相同，终止条件为 `sum > target` 和 `sum == target`。

- **单层搜索的逻辑**

这里与[[组合总和]]最大的不同就是要去重了。

前面我们提到：要去重的是“同一树层上的使用过”，如何判断同一树层上元素（相同的元素）是否使用过了呢。

**如果`candidates[i] == candidates[i - 1]` 并且 `used[i - 1] == false`，就说明：前一个树枝，使用了`candidates[i - 1]`，也就是说同一树层使用过`candidates[i - 1]`。

此时for循环里就应该做continue的操作。这块比较抽象，如图：

![40.组合总和II1](https://code-thinking-1253855093.file.myqcloud.com/pics/20230310000954.png)

我在图中将used的变化用橘黄色标注上，可以看出在`candidates[i] == candidates[i - 1]`相同的情况下：

- `used[i - 1] == true`，说明**同一树枝**`candidates[i - 1]`使用过
- `used[i - 1] == false`，说明**同一树层**`candidates[i - 1]`使用过

可能有的录友想，为什么 `used[i - 1] == false` 就是**同一树层**呢，因为同一树层，`used[i - 1] == false` 才能表示，当前取的 `candidates[i]` 是从 `candidates[i - 1]` 回溯而来的。

而 `used[i - 1] == true`，说明是进入下一层递归，去下一个数，所以**是树枝上**，如图所示：

![|700](https://code-thinking-1253855093.file.myqcloud.com/pics/20221021163812.png)

**这块去重的逻辑很抽象，网上搜的题解基本没有能讲清楚的，如果大家之前思考过这个问题或者刷过这道题目，看到这里一定会感觉通透了很多！**

整体代码：

```java
class Solution {
    List<Integer> path = new ArrayList<>();
    List<List<Integer>> res = new ArrayList<>();
    int sum = 0;
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        Arrays.sort(candidates);
        int[] used = new int[candidates.length]; // 判断同一树层的元素是否使用过
        backtracking(candidates, target, 0, used);
        return res;
    }
    public void backtracking(int[] candidates, int target, int index, int[] used){
        if(sum > target){
            return;
        }
        if(sum == target){
            res.add(new ArrayList<>(path));
            return;
        }
        for(int i = index; i < candidates.length && sum + candidates[i] <= target; i++){ // 和上一道题一样的剪枝
            if(i > 0 && candidates[i] == candidates[i - 1] && used[i - 1] == 0){
                continue; // 同一树层去重
            }
            path.add(candidates[i]);
            sum += candidates[i];
            used[i] = 1;
            backtracking(candidates, target, i + 1, used); // index变为i + 1
            used[i] = 0;
            sum -= candidates[i];
            path.removeLast();
        }
    }
}
```

也可以不用used数组：

```java
class Solution {
    List<Integer> path = new ArrayList<>();
    List<List<Integer>> res = new ArrayList<>();
    int sum = 0;
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        Arrays.sort(candidates);
        backtracking(candidates, target, 0);
        return res;
    }
    public void backtracking(int[] candidates, int target, int index){
        if(sum > target){
            return;
        }
        if(sum == target){
            res.add(new ArrayList<>(path));
            return;
        }
        for(int i = index; i < candidates.length && sum + candidates[i] <= target; i++){ // 和上一道题一样的剪枝
            if(i > index && candidates[i] == candidates[i - 1]){
                continue; // 同一树层去重
            }
            path.add(candidates[i]);
            sum += candidates[i];
            backtracking(candidates, target, i + 1); // index变为i + 1
            sum -= candidates[i];
            path.removeLast();
        }
    }
}
```

只用满足这个条件就行：`i > index && candidates[i] == candidates[i - 1]`。