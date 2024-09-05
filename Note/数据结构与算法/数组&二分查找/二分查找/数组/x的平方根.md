![[Pasted image 20240116103442.png|475]]

方法一：逐步逼近，时间复杂度为O(√x)

```c
int mySqrt(int x){
    long j = x/2;
    if(x == 1){
        return 1;
    }
    else{
        while(1){
            if(j*j == x || ((j+1)*(j+1) > x && j*j < x)){
                break; 
            }
            else if(j*j > x){
                j = j/2;
            }
            else{
                j++;
            }
        }
    }
    return j;
}
```

方法二：二分查找，时间复杂度为O(log n)

```c
int mySqrt(int x){
    int left = 1;
    int right = x;
    long middle = 0;
    while(left <= right){
        middle = left + (right-left)/2;
        if(middle == x/middle){
            return middle;
        }
        else if(middle < x/middle){
            left = middle + 1;
        }
        else{
            right = middle - 1;
        }
    }
    return right;
}
```

Java：

一定要注意数据类型用long，不然会越界！

```java
class Solution {
    public int mySqrt(int x) {
        if (x == 0) {
            return 0;
        }
        
        long left = 1;
        long right = x;
        long middle = 0;
        
        while (left <= right) {
            middle = left + (right - left) / 2;
            if (middle * middle == x) {
                return (int) middle;
            } else if (middle * middle < x) {
                left = middle + 1;
            } else {
                right = middle - 1;
            }
        }
        // 当无法找到精确平方根时，返回最接近平方根的整数值，这时候返回left或者right都行
        return (int) right;
    }
}
```

也不用乘法，用除法更好，不会越界可以用int：

```java
class Solution {
    public int mySqrt(int x) {
        int left = 1;
        int right = x;
        int middle = 0;
        while (left <= right) {
            middle = left + (right - left) / 2;
            if (middle == x / middle) {
                return middle;
            } else if (middle < x / middle) {
                left = middle + 1;
            } else {
                right = middle - 1;
            }
        }
        return right;
    }
}
```
