![[Pasted image 20240423192819.png|675]]

## 思路

本题首先要清楚两点：

- 只有一只股票！
- 当前只有买股票或者卖股票的操作。

想获得利润至少要两天为一个交易单元。

### 贪心算法

这道题目可能我们只会想，选一个低的买入，再选个高的卖，再选一个低的买入.....循环反复。

**如果想到其实最终利润是可以分解的，那么本题就很容易了！**如何分解呢？

假如第 0 天买入，第 3 天卖出，那么利润为：`prices[3] - prices[0]`。相当于`(prices[3] - prices[2]) + (prices[2] - prices[1]) + (prices[1] - prices[0])`。

**此时就是把利润分解为每天为单位的维度，而不是从 0 天到第 3 天整体去考虑！**

![122.买卖股票的最佳时机II|500](https://code-thinking-1253855093.file.myqcloud.com/pics/2020112917480858-20230310134659477.png)

从图中可以发现，其实我们需要收集每天的正利润就可以，**收集正利润的区间，就是股票买卖的区间，而我们只需要关注最终利润，不需要记录区间**。

那么**只收集正利润**就是贪心所贪的地方！

**局部最优：收集每天的正利润，全局最优：求得最大利润**。局部最优可以推出全局最优，找不出反例，试一试贪心！
#### 整体代码

```java
class Solution {
    public int maxProfit(int[] prices) {
        int res = 0;
        for(int i = 0; i < prices.length - 1; i++){
            int temp = prices[i + 1] - prices[i];
            if(temp > 0){
                res += temp;
            }
        }
        return res;
    }
}
```

还可以简化：

```java
class Solution {
    public int maxProfit(int[] prices) {
        int res = 0;
        for(int i = 0; i < prices.length - 1; i++){
            res += Math.max(0, prices[i + 1] - prices[i]);
        }
        return res;
    }
}
```

- 时间复杂度：O(n)
- 空间复杂度：O(1)

### 动态规划

见[[数据结构与算法/动态规划/股票问题/买卖股票的最佳时机 II|买卖股票的最佳时机 II]]~