![[Pasted image 20240505211445.png|675]]

## 思路

这道题目和[[打家劫舍Ⅰ]]是差不多的，唯一区别就是成环了。

对于一个数组，成环的话主要有如下三种情况：

- 情况一：考虑不包含首尾元素

![213.打家劫舍II|475](https://code-thinking-1253855093.file.myqcloud.com/pics/20210129160748643-20230310134000692.jpg)

- 情况二：考虑包含首元素，不包含尾元素

![213.打家劫舍II1|475](https://code-thinking-1253855093.file.myqcloud.com/pics/20210129160821374-20230310134003961.jpg)

- 情况三：考虑包含尾元素，不包含首元素

![213.打家劫舍II2|475](https://code-thinking-1253855093.file.myqcloud.com/pics/20210129160842491-20230310134008133.jpg)

**注意我这里用的是"考虑"**，例如情况三，虽然是考虑包含尾元素，但不一定要选尾部元素！ 对于情况三，取`nums[1]` 和 `nums[3]`就是最大的。

**而情况二 和 情况三都包含了情况一了，所以只考虑情况二和情况三就可以了**。 剩下的和[[打家劫舍Ⅰ]]就是一样的了。

#### 整体代码

简化内存，用三个数代替数组：

```java
class Solution {
    public int rob(int[] nums) {
        int len = nums.length;
        if (len == 1) {
            return nums[0];
        }
        return Math.max(robAction(nums, 1, len), robAction(nums, 0, len - 1));
    }
    int robAction(int[] nums, int start, int end) {
        int x = 0, y = 0, z = 0;
        for (int i = start; i < end; i++) {
            y = z;  //保存前前一间房子的金额
            z = Math.max(y, x + nums[i]); //计算当前房子和前一间房子相邻时的最大金额
            x = y;  //返回抢劫该区间的最大金额
        }
        return z;
    }
}
```

用数组，一定要注意是从`start`开始，数组长度是`end-start`：

```java
class Solution {
    public int rob(int[] nums) {
        int len = nums.length;
        if (len == 1) {
            return nums[0];
        }
        if (len == 2) {  // 防止之后越界
            return Math.max(nums[0], nums[1]);
        }
        return Math.max(robAction(nums, 1, len), robAction(nums, 0, len - 1));
    }
    int robAction(int[] nums, int start, int end) {
        int[] dp = new int[end - start];
        dp[0] = nums[start];
        dp[1] = Math.max(nums[start], nums[start + 1]);
        for (int i = 2; i < end - start; i++) {
            dp[i] = Math.max(dp[i - 2] + nums[start + i], dp[i - 1]); 
        }
        return dp[end - start - 1];
    }
}
```
