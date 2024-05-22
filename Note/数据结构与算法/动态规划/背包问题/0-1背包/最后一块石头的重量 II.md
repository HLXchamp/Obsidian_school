![[Pasted image 20240430145608.png|700]]

## 思路

本题其实就是**尽量让石头分成重量相同的两堆**，相撞之后剩下的石头最小，**这样就化解成0-1背包问题了**。其实和上一道题[[分割等和子集]]非常像。

也是先求出全部重量sum，然后这里直接**取`target = sum/2`作为我们装背包的最大重量**，因为要碰撞后的最小，其实就是找两堆重量最相近的。最后返回`sum - 2 * dp[target]`就行。

举例，输入：`[2,4,1,1]`，此时target = (2 + 4 + 1 + 1)/2 = 4 ，dp数组状态图如下：

![1049.最后一块石头的重量II|500](https://code-thinking-1253855093.file.myqcloud.com/pics/20210121115805904.jpg)

**在计算target的时候，target = sum / 2 因为是向下取整，所以`sum - dp[target]` 一定是大于等于`dp[target]`的**。那么相撞之后剩下的最小石头重量就是 `(sum - dp[target]) - dp[target]`。

#### 整体代码

```java
class Solution {
    public int lastStoneWeightII(int[] stones) {
        int sum = 0;
        for(int i : stones){
            sum += i;
        }
        int target = sum / 2; // 向下取整的
        int[] dp = new int[target + 1];
        for(int i = 0; i < stones.length; i++){
            for(int j = target; j >= stones[i]; j--){
                dp[j] = Math.max(dp[j], dp[j - stones[i]] + stones[i]);
            }
        }
        return (sum - 2 * dp[target]); // 相当于(sum - target[i])-target[i]
    }
}
```
