![[Pasted image 20240420122452.png]]

## 思路

其实只要意识到这是切割问题，**切割问题就可以使用回溯搜索法把所有可能性搜出来**，和刚做过的[[分割回文串]]就十分类似了，如图：

![93.复原IP地址|825](https://code-thinking-1253855093.file.myqcloud.com/pics/20201123203735933.png)

### 回溯三部曲

- 递归参数

startIndex一定是需要的，因为不能重复分割，记录下一层递归分割的起始位置。本题我们还需要一个变量pointNum，**记录添加逗点的数量**。

- 递归终止条件

终止条件和[[分割回文串]]情况就不同了，本题明确要求只会分成4段，所以不能用切割线切到最后作为终止条件，而是**分割的段数作为终止条件**。

pointNum表示逗点数量，**pointNum为3**说明字符串分成了4段了。然后**验证一下第四段是否合法**，如果合法就加入到结果集里。

- 单层搜索的逻辑

与[[分割回文串]]相似，在`for (int i = startIndex; i < s.size(); i++)`循环中 `[startIndex, i]` 这个区间就是截取的子串，需要判断这个子串是否合法。

如果合法就在字符串后面加上符号`.`表示已经分割。

如果不合法就结束本层循环，如图中剪掉的分支：

![93.复原IP地址|800](https://code-thinking-1253855093.file.myqcloud.com/pics/20201123203735933-20230310132314109.png)

然后就是递归和回溯的过程：

递归调用时，下一层递归的startIndex要从**i+2开始**（因为需要在字符串中加入了分隔符`.`），同时记录分割符的数量**pointNum 要 +1**。回溯的时候，就将刚刚加入的分隔符`.` 删掉就可以了，pointNum也要-1。
### 判断子串是否合法

最后就是在写一个判断段位是否是有效段位了。

主要考虑到如下三点：

- 段位以0为开头的数字不合法
- 段位里有非正整数字符不合法（题中说了全是整数组成的，故可以忽略）
- 段位如果大于255了不合法

整体代码如下：

```java
class Solution {
    List<String> res = new ArrayList<>();
    public List<String> restoreIpAddresses(String s) {
        StringBuilder t = new StringBuilder(s); // StringBuilder更方便
        backtracking(t, 0, 0);
        return res;
    }
    public void backtracking(StringBuilder s, int index, int pointNum){
        if(pointNum == 3){
            if(isValid(s, index, s.length() - 1)){ // 判断一下第四段是否满足条件
                res.add(s.toString()); // 转化成String类型
            }
            return;
        }
        for(int i = index; i < s.length(); i++){
            if(isValid(s, index, i)){ 
                s.insert(i + 1, '.'); // 传的是i + 1，因为循环变的是i
                pointNum += 1;
                backtracking(s, i + 2, pointNum); // 这里是i + 2
                pointNum -= 1;
                s.deleteCharAt(i + 1); // 回溯要和上面的条件一样
            }
            else{
                break;
            }
        }
    }
    public boolean isValid(StringBuilder s, int start, int end){
        if((end - start >= 1 && s.charAt(start) == '0') || end - start > 3){
            return false;
        }
        String temp = s.substring(start, end + 1);  // 截取对应位置的子串
        if(temp.isEmpty()){  // 字符串判空最好用isEmpty
            return false;
        }
        else{
            if(Integer.parseInt(temp) > 255){ // 转化成int型
                return false;
            }
        }
        return true;
    }
}
```
