![[Pasted image 20241114221403.png|625]]
![[Pasted image 20241114221501.png]]

在[[统计满足 K 约束的子字符串数量 I]]的基础上改了一点，但有几个例子过不了，应该是时间复杂度高了：

```java
class Solution {
    public long[] countKConstraintSubstrings(String s, int k, int[][] queries) {
            long[] res = new long[queries.length];
            for(int i = 0; i < queries.length; i++){
                res[i] = count(s, k, queries[i]);
            }
            return res;
        }
        public long count(String s, int k, int[] query){
            // 将截取的字符串转换为字符数组
            String subStr = s.substring(query[0], query[1] + 1);
            char[] nums = subStr.toCharArray();
            long res = 0;
            int start = 0;
            int[] temp = new int[2];
            for (int i = 0; i < nums.length; i++) {
                temp[nums[i] & 1]++;
                while(temp[0] > k && temp[1] > k){
                    temp[nums[start] & 1]--;
                    start++;
                }
                res += i - start + 1;
            }
            return res;
        }
}
```

![[Pasted image 20241114223414.png]]