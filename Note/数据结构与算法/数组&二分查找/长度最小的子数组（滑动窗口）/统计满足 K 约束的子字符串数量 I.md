![[Pasted image 20241112221619.png|500]]
![[Pasted image 20241112221642.png|600]]

#### 方法一：不成熟的滑动窗口

满足 k 约束，应该从2k+2长度的子串开始找（<=2k+1的子串都一定满足）。

这段代码没有有效地使用滑动窗口的技术。在滑动窗口的算法中，一般会在窗口移动时更新窗口中的内容，而不需要每次都重新计算窗口内的值。但在这里，代码中的 `for` 循环每次从头到尾重新遍历了 `start` 到 `end` 的区间（即每次重新计算窗口内的 `count0` 和 `count1`），并没有使用滑动窗口的特性。

```java
class Solution {
    public int countKConstraintSubstrings(String s, int k) {
        int length = s.length();
        if(length <= 2 * k + 1){
            return length * (length + 1) / 2;
        }
        int res = (2 * length - 2 * k) * (2 * k + 1) / 2;
        int distance = 2 * k + 2;
        int start = 0;
        while(distance <= length){
            int count0 = 0;
            int count1 = 0;
            int end = start + distance;
            for (int i = start; i < end; i++) {
                if(s.charAt(i) == '0'){
                    count0++;
                } else {
                    count1++;
                }
            }
            if(count0 <= k || count1 <= k){
                res++;
            }
            start++;
            if (start + distance <= length) {
                distance = distance; // 保持不变
            } else {
                distance++; // 增加距离
                start = 0;
            }
        }
        return res;
    }
}
```

- **时间复杂度**：O(length^2)
- **空间复杂度**：O(1)

#### 方法二：用滑动窗口

如果要使用滑动窗口，可以避免每次重新计算窗口内容。具体方法如下：

1. 在第一次循环时计算出第一个窗口的 `count0` 和 `count1`。
2. 在窗口右移时，只需更新边界值：
   从窗口移出的字符值（`s.charAt(start - 1)`）以及新进入窗口的字符值（`s.charAt(end)`）来更新 `count0` 和 `count1`。

```java
public int countKConstraintSubstrings(String s, int k) {
    int length = s.length();
    if (length <= 2 * k + 1) {
        return length * (length + 1) / 2;
    }
    
    int res = (2 * length - 2 * k) * (2 * k + 1) / 2;
    int distance = 2 * k + 2;
    int start = 0;
    int count0 = 0, count1 = 0;
    
    // 初始窗口计数
    for (int i = 0; i < distance; i++) {
        if (s.charAt(i) == '0') {
            count0++;
        } else {
            count1++;
        }
    }
    if (count0 <= k || count1 <= k) {
        res++;
    }
    
    // 滑动窗口
    for (start = 1; start + distance <= length; start++) {
        // 移除窗口左边的元素
        if (s.charAt(start - 1) == '0') {
            count0--;
        } else {
            count1--;
        }
        
        // 添加窗口右边的新元素
        if (s.charAt(start + distance - 1) == '0') {
            count0++;
        } else {
            count1++;
        }
        
        if (count0 <= k || count1 <= k) {
            res++;
        }
    }
    
    return res;
}
```

- **时间复杂度**：这种滑动窗口方法避免了每次都重新计算窗口内字符的数量，使得 `for` 循环中的更新操作可以在 O(1) 时间内完成，将整体时间复杂度降低为 **O(n)**
- **空间复杂度**：**O(1)**

#### 方法三：更简洁的滑动窗口

最关键的是：`res += end - start + 1;` 即符合条件的子字符串数为窗口长度。
还有定义了一个数组来统计 0 和 1 的个数，使用位运算来统计 `s[end]` 是 '0' 还是 '1'。

```java
class Solution {
    public int countKConstraintSubstrings(String S, int k) {
        char[] s = S.toCharArray(); 
        int res = 0; 
        int start = 0; 
        int[] nums = new int[2]; // 用于存储当前窗口中 '0' 和 '1' 的计数
        for (int end = 0; end < s.length; end++) {
            nums[s[end] & 1]++; // 使用位运算来统计 s[end] 是 '0' 还是 '1'
            while (nums[0] > k && nums[1] > k) {
                nums[s[start] & 1]--; // 减少窗口左边界字符的计数
                start++; // 移动左指针缩小窗口
            }
            // 符合条件的子字符串数为窗口长度
            res += end - start + 1;
        }
        return res;
    }
}
```
