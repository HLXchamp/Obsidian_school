![[Pasted image 20250102124111.png]]
![[Pasted image 20250102124124.png|425]]

#### 方法一：直接遍历

要注意可能会覆盖全部，也可能覆盖一部分，所以判断条件是`startTime < temp[1] && endTime > temp[0]`。

```java
class MyCalendar {
    List<int[]> calendar;

    public MyCalendar() {
        this.calendar = new ArrayList<>();
    }

    public boolean book(int startTime, int endTime) {
        if(calendar.isEmpty()){
            calendar.add(new int[]{startTime, endTime});
            return true;
        } else {
            for (int[] temp : calendar) {
                if(startTime < temp[1] && endTime > temp[0]){
                    return false;
                }
            }
            calendar.add(new int[]{startTime, endTime});
            return true;
        }
    }
}

/**
 * Your MyCalendar object will be instantiated and called as such:
 * MyCalendar obj = new MyCalendar();
 * boolean param_1 = obj.book(startTime,endTime);
 */
```

- 时间复杂度：O(n^2), 其中 n 表示日程安排的数量。由于每次在进行预订时，都需要遍历所有已经预订的行程安排。

#### 方法二：TreeSet

- 定义了比较器，`TreeSet` 中的元素会按照 **数组第一个元素的大小** 排序。
- `floor()` 和 `ceiling()` 也是基于这个排序规则工作的。

```java
class MyCalendar {

    TreeSet<int[]> calendar;

    public MyCalendar() {
        this.calendar = new TreeSet<int[]>((a, b) -> a[0] - b[0]);
    }
    
    public boolean book(int startTime, int endTime) {
         // 找到与当前区间可能冲突的上下界
        int[] floor = calendar.floor(new int[]{startTime, 0});  // 开始时间 <= startTime 的最大区间
        int[] ceiling = calendar.ceiling(new int[]{startTime, 0}); // 开始时间 >= startTime 的最小区间
        if (floor != null && floor[1] > startTime) {
            return false; 
        }
        if (ceiling != null && ceiling[0] < endTime) {
            return false; 
        }
        calendar.add(new int[]{startTime, endTime});
        return true;
    }
}
```


 - `floor()` 和 `ceiling()` 操作的时间复杂度为 **O(log n)**，其中 `n` 是当前集合中存储的区间数。
 - **添加区间 `calendar.add()`** 插入操作的时间复杂度同样为 **O(log n)**，因为插入也需要在红黑树中保持平衡。

- 进行 `n` 次日程预定，总时间复杂度：O(n log n)。

#### 方法三：二分查找+TreeSet

见 https://leetcode.cn/problems/my-calendar-i/solutions/1643942/wo-de-ri-cheng-an-pai-biao-i-by-leetcode-nlxr

#### 方法四：线段树

见 https://leetcode.cn/problems/my-calendar-i/solutions/1643942/wo-de-ri-cheng-an-pai-biao-i-by-leetcode-nlxr