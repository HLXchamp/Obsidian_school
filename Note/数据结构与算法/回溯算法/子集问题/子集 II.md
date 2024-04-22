![[Pasted image 20240420184935.png|625]]
## 思路

这道题目和[[子集问题]]区别就是集合里有重复元素了，而且求取的子集要去重。

那么关于回溯算法中的去重问题，**在[[组合总和 II]]中已经详细讲解过了，和本题是一个套路，加一个used数组就行**。

**后期要讲解的排列问题里去重也是这个套路，所以理解“树层去重”和“树枝去重”非常重要**。

用示例中的`[1, 2, 2]` 来举例，如图所示： （**注意去重需要先对集合排序**）

![90.子集II](https://code-thinking-1253855093.file.myqcloud.com/pics/20201124195411977.png)

从图中可以看出，同一树层上重复取2 就要过滤掉，同一树枝上就可以重复取2，因为同一树枝上元素的集合才是唯一子集！

#### 整体代码

```java
class Solution {    
    List<Integer> path = new ArrayList<>();
    List<List<Integer>> res = new ArrayList<>();
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        Arrays.sort(nums);
        int[] used = new int[nums.length];
        backtracking(nums, 0, used);
        return res;
    }
    public void backtracking(int[] nums, int index, int[] used){
        res.add(new ArrayList<>(path));
        for(int i = index; i < nums.length; i++){
            if(i > 0 && nums[i] == nums[i - 1] && used[i - 1] == 0){
                continue; // 同一树层去重
            }
            used[i] = 1;
            path.add(nums[i]);
            backtracking(nums, i + 1, used);
            used[i] = 0;
            path.remove(path.size() - 1);
        }
        return;
    }
}
```
