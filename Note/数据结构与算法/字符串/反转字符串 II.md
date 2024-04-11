![[Pasted image 20240129104812.png]]

**当需要固定规律一段一段去处理字符串的时候，要想想在在for循环的表达式上做做文章！**

其实在遍历字符串的过程中，只要让 i += (2 * k)，i 每次移动 2 * k 就可以了，然后判断是否需要有反转的区间。

![[Pasted image 20240129163026.png|500]]

- 本来写了3个for循环加一个if，结果可以放在一个for循环里：
    `int tail = (i + k - 1 < length) ? i + k - 1 : length - 1;`
- 字符串存储临时变量最好用char；
- 最关键的就是：`i += 2 * k`

```c
char* reverseStr(char* s, int k) {
    int i = 0;
    int length = strlen(s);
    for (; i < length; i += 2 * k) {
        int head = i;
        int tail = (i + k - 1 < length) ? i + k - 1 : length - 1;
        while (head < tail) {
            char temp = s[tail];
            s[tail] = s[head];
            s[head] = temp;
            head++;
            tail--;
        }
    }
    return s;
}
```

