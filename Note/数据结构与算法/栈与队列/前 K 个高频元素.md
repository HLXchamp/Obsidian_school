![[Pasted image 20240321105449.png|650]]
### 解法一：粗暴排序法

最简单粗暴的思路就是 使用**排序算法对元素按照频率**由高到低进行排序，然后再取前k个元素。

![[Pasted image 20240321114424.png|575]]

可以发现，使用常规的诸如 冒泡、选择、甚至快速排序都是不满足题目要求，它们的时间复杂度都是大于或者等于 `O(nlog⁡n)`，而题目要求算法的时间复杂度必须优于 `O(nlogn)`。
### 解法二：最小堆

#### 堆

**堆是一棵完全二叉树，树中每个结点的值都不小于（或不大于）其左右孩子的值。** 如果父亲结点是大于等于左右孩子就是大顶堆，小于等于左右孩子就是小顶堆。

所以大家经常说的大顶堆（堆头是最大元素），小顶堆（堆头是最小元素），如果懒得自己实现的话，就直接用priority_queue（优先级队列）就可以了，底层实现都是一样的，从小到大排就是小顶堆，从大到小排就是大顶堆。

如果定义一个大小为k的大顶堆，在每次移动更新大顶堆的时候，每次弹出都把最大的元素弹出去了，那么怎么保留下来前K个高频元素呢。

**所以我们要用小顶堆，因为要统计最大前k个元素，只有小顶堆每次将最小的元素弹出，最后小顶堆里积累的才是前k个最大元素。**

#### 思路

题目最终需要返回的是前 k 个频率最大的元素，可以想到借助堆这种数据结构，对于 k 频率之后的元素不用再去处理，进一步优化时间复杂度。

![[Pasted image 20240321114754.png|600]]

具体操作为：
- 借助 **哈希表** 来建立数字和其出现次数的映射，遍历一遍数组统计元素的频率
- 维护一个元素数目为 k 的最小堆
- 每次都将新的元素与堆顶元素（堆中频率最小的元素）进行比较
- 如果新的元素的频率比堆顶端的元素大，则弹出堆顶端的元素，将新的元素添加进堆中
- 最终，堆中的 k 个元素即为前 k 个高频元素

![[1561712388100.gif|500]]

结果为：

![[Pasted image 20240321114945.png]]

[[四数相加 Ⅱ#^7f2d9d]]

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> res = new HashMap<>();
        for(int i : nums){
            res.put(i, res.getOrDefault(i,0) + 1);
        }
        PriorityQueue<int[]> queue = new PriorityQueue<>((i, j) -> i[1] - j[1]);
        for(Map.Entry<Integer, Integer> entry : res.entrySet()){
            if(queue.size() < k){//小顶堆元素个数小于k个时直接加
                queue.add(new int[]{entry.getKey(), entry.getValue()});
            }else{
                if(entry.getValue() > queue.peek()[1]){//当前元素出现次数大于小顶堆的根结点(这k个元素中出现次数最少的那个)
                    queue.poll();//弹出队头(小顶堆的根结点),即把堆里出现次数最少的那个删除,留下的就是出现次数多的了
                    queue.add(new int[]{entry.getKey(),entry.getValue()});
                }
            }
        }
        int[] ans = new int[k];
        for(int i = k - 1; i >= 0; i--){//依次弹出小顶堆,先弹出的是堆的根,出现次数少,后面弹出的出现次数多
            ans[i] = queue.poll()[0];
        }
        return ans;
    }
}
```
