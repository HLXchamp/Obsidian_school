![[Pasted image 20240424100553.png|400]]
### 普通思路

每次取反都找数组中最小的一个元素取反：

```java
class Solution {
    public int largestSumAfterKNegations(int[] nums, int k) {
        int res = 0;
        for(int i = 0; i < k; i++){
            nums[min(nums)] *= -1;
        }
        for(int i = 0; i < nums.length; i++){
            res += nums[i];
        }
        return res;
    }
    public int min(int[] nums){
        int min = nums[0];
        int index = 0;
        for(int i = 0; i < nums.length; i++){
            if(nums[i] < min){
                index = i;
                min = nums[i];
            }
        }
        return index;
    }
}
```

- 时间复杂度: O(n<sup>2</sup>)
- 空间复杂度: O(n)
### 贪心思路

贪心的思路，局部最优：让绝对值大的负数变为正数，当前数值达到最大，整体最优：整个数组和达到最大。

局部最优可以推出全局最优。

那么如果将负数都转变为正数了，K依然大于0，此时的问题是一个有序正整数序列，如何转变K次正负，让 数组和 达到最大。

那么**又是一个贪心**：局部最优：**只找数值最小的正整数进行反转**，当前数值和可以达到最大（例如正整数数组{5, 3, 1}，反转1），全局最优：整个数组和达到最大。**这么一道简单题，就用了两次贪心！**

那么本题的解题步骤为：

- 第一步：将数组按照绝对值大小从大到小排序，**注意要按照绝对值的大小**
- 第二步：从前向后遍历，遇到负数将其变为正数，同时K--
- 第三步：如果K还大于0，那么反复转变数值最小的元素，将K用完
- 第四步：求和

整体代码如下：

```java
class Solution {
    public int largestSumAfterKNegations(int[] nums, int k) {
        nums = Arrays.stream(nums) //使用Streams和Lambda表达式按绝对值降序排列
                .boxed() // 将int流转换为Integer流
                .sorted((a, b) -> Integer.compare(Math.abs(b), Math.abs(a)))
                .mapToInt(Integer::intValue).toArray(); // 转换为int数组
        for(int i = 0; i < nums.length; i++){
            if(nums[i] < 0 && k > 0){
                nums[i] *= -1;
                k--;
            }
        }
        if (k % 2 == 1){ // 如果K还大于0，那么反复转变数值最小的元素，将K用完
            nums[nums.length - 1] *= -1;
        } 
        return Arrays.stream(nums).sum(); //int数组转换为IntStream，sum计算IntStream中所有元素的和
    }
}
```

- 时间复杂度: O(nlogn)
- 空间复杂度: O(1)