![[Pasted image 20240421195317.png|525]]

## 思路

这道题目和[[全排列]]的区别在与**给定一个可包含重复数字的序列**，要返回**所有不重复的全排列**。

这里又涉及到去重了。那么排列问题其实也是一样的套路。**还要强调的是去重一定要对元素进行排序，这样我们才方便通过相邻的节点来判断是否重复使用了**。

以示例中的 `[1,1,2]`为例 （为了方便举例，已经排序）抽象为一棵树，去重过程如图：

![47.全排列II1](https://code-thinking-1253855093.file.myqcloud.com/pics/20201124201331223.png)

图中我们对同一树层，前一位（也就是`nums[i-1]`）如果使用过，那么就进行去重。

**一般来说：组合问题和排列问题是在树形结构的叶子节点上收集结果，而子集问题就是取树上所有节点的结果**。

在[[全排列]]中已经详细讲解了排列问题的写法，在[[组合总和 II]]、[[子集 II]]中详细讲解了去重的写法。

### 拓展

大家发现，去重最为关键的代码为：

```java
if (i > 0 && nums[i] == nums[i - 1] && used[i - 1] == false) {
    continue;
}
```

**如果改成 `used[i - 1] == true`， 也是正确的!**

这是为什么呢，就是上面我刚说的，如果要对**树层**中前一位去重，就用`used[i - 1] == false`，如果要对**树枝**前一位去重用`used[i - 1] == true`。

**对于排列问题，树层上去重和树枝上去重，都是可以的，但是树层上去重效率更高！**

用输入`[1,1,1]` 来举一个例子。

树层上去重(`used[i - 1] == false`)，的树形结构如下：

![47.全排列II2](https://code-thinking-1253855093.file.myqcloud.com/pics/20201124201406192.png)

树枝上去重（`used[i - 1] == true`）的树型结构如下：

![47.全排列II3](https://code-thinking-1253855093.file.myqcloud.com/pics/20201124201431571.png)

大家应该很清晰的看到，树层上对前一位去重非常彻底，效率很高，树枝上对前一位去重虽然最后可以得到答案，但是做了很多无用搜索。

#### 整体代码

```java
class Solution {
    List<Integer> path = new ArrayList<>();
    List<List<Integer>> res = new ArrayList<>();
    public List<List<Integer>> permuteUnique(int[] nums) {
        if(nums.length == 0){
            return res;
        }
        Arrays.sort(nums); // 一定要排序
        int[] used = new int[nums.length];
        backtracking(nums, used);
        return res;
    }
    public void backtracking(int[] nums, int[] used){
        if(path.size() == nums.length){ 
            res.add(new ArrayList<>(path));
            return;
        }
        for(int i = 0; i < nums.length; i++){
            if(i > 0 && nums[i] == nums[i - 1] && used[i - 1] == 0){ // 树层去重
                continue;
            }
            if(used[i] == 1){ // 树枝去重
                continue;
            }
            path.add(nums[i]);
            used[i] = 1;
            backtracking(nums, used);
            used[i] = 0;
            path.remove(path.size() - 1);
        }
    }
}
```

要是想偷懒，可以在[[全排列]]的基础上直接加两行代码：

```java
if(path.size() == nums.length){
    if(!res.contains(path)){ // 看之前是否有相同的path
        res.add(new ArrayList<>(path));
    }
    return;
}
```
