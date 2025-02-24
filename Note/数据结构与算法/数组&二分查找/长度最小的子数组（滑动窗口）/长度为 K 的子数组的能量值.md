![[Pasted image 20241107215705.png]]
![[Pasted image 20241107215749.png|348]]

这道题直接用**枚举法**就行：

```java
class Solution {
    public int[] resultsArray(int[] nums, int k) {
        int resLength = nums.length + 1 - k;
        int[] res = new int[resLength];
        for (int i = 0; i < resLength; i++) {
            res[i] = count(nums, i, k);
        }
        return res;
    }

    public int count(int[] nums, int begin, int k) {
        for (int i = 0; i < k - 1; i++) { //注意是k - 1，因为k=3=[1,2,3]的话只用比两次
            if (nums[i + begin + 1] - nums[i + begin] != 1) {
                return -1;
            }
        }
        return nums[begin + k - 1];
    }
}
```

- **时间复杂度**：**O(n×k)**，n 表示给定数组的长度，k 表示给定的数字。我们需要枚举每个长度为 k 的连续子数组，一共存在 n−k+1 个长度为 k 的连续子数组，对于每个长度为 k 的子数组都需要判断是否满足连续上升，需要的时间复杂度为 O(k)，总的时间复杂度为 O(n×k)。

- **空间复杂度**：**O(1)**，除返回值外，不需要额外的空间。


![[Pasted image 20241107220728.png]]
![[Pasted image 20241107220819.png|338]]

这道题和上一道题唯一区别就是数组长度由500变到了10<sup>5</sup>，所以时间复杂度O(n x k)的枚举法就不行了，会超时，应该采用**滑动窗口**的思想！

![[YI6(S7AG_37G{H}Z05AKRSG.png]]

```java
class Solution {
    public int[] resultsArray(int[] nums, int k) {
        int length = nums.length;
        if(length == 0 || length == 1 || k == 1){
            return nums;
        }
        int[] res = new int[length - k + 1];
        Arrays.fill(res, -1);
        int start = 0; // 当前子数组的起始位置
        int count = 1; // 连续递增的元素数量，从第一个元素开始算起
        for(int end = 1; end < length; end++){
            if(nums[end] - nums[end - 1] != 1){
                count = 1; // 重置计数为 1
                start = end; // 起始位置更新为当前元素的位置
            }
            else{
                count++; // 连续递增，则计数加1
            }
            if(count == k){
                res[start] = nums[end]; 
                start++; 
                count--; // 连续计数减1，因为我们跳过了一个元素，重新计算
            }
        }
        return res;
    }
}
```

注意事项：

1. 把数组初始化为-1，只用关注一直递增的元素就行；
2. end最开始不要和front一样，不然`k=2`时不能判断；
3. 当不是连续递增时，count要重置，同时窗口的开始start也要变成end！！而不是+1！
4. 当满足`count == k`后，记得要把`count--`，相当于跳过一个元素重新计算。

- **时间复杂度**：综合来看，整个算法仅仅是一次遍历，且每次迭代的操作复杂度是常数级别。因此，时间复杂度是 **`O(n)`**;

- **空间复杂度**：**O(n)**