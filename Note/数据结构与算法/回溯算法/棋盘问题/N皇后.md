![[Pasted image 20240422125212.png|675]]

## 思路

首先来看一下皇后们的约束条件：

1. 不能同行
2. 不能同列
3. 不能同斜线

确定完约束条件，来看看究竟要怎么去搜索皇后们的位置，其实搜索皇后的位置，可以抽象为一棵树。一个 3 * 3 的棋盘，将搜索过程抽象为一棵树，如图：

![51.N皇后|625](https://code-thinking-1253855093.file.myqcloud.com/pics/20210130182532303.jpg)

从图中，可以看出，二维矩阵中矩阵的高就是这棵树的高度，矩阵的宽就是树形结构中每一个节点的宽度。

那么我们用皇后们的约束条件，来回溯搜索这棵树，**只要搜索到了树的叶子节点，说明就找到了皇后们的合理位置了**。

### 回溯三部曲

- 递归函数参数

依然是定义全局变量二维列表res来记录最终结果。参数n是棋盘的大小，然后用row来记录当前遍历到棋盘的第几层了。

- 递归终止条件

![51.N皇后|625](https://code-thinking-1253855093.file.myqcloud.com/pics/20210130182532303-20230310122134167.jpg)

可以看出，当递归到棋盘最底层（也就是叶子节点）的时候，就可以收集结果并返回了。

- 单层搜索的逻辑

递归深度就是row控制棋盘的行，每一层里for循环的col控制棋盘的列，一行一列，确定了放置皇后的位置。每次都是要从新的一行的起始位置开始搜，所以都是从0开始。

- 验证棋盘是否合法

按照如下标准去重：

1. 不能同行
2. 不能同列
3. 不能同斜线 （45度和135度角）

为什么**没有在同行进行检查**呢？因为在单层搜索的过程中，每一层递归，只会选for循环（也就是同一行）里的一个元素，所以不用去重了。

#### 整体代码

```java
class Solution {
    List<List<String>> res = new ArrayList<>();
    public List<List<String>> solveNQueens(int n) {
        char[][] chessboard = new char[n][n];
        for (char[] i : chessboard){
            Arrays.fill(i, '.');
        }
        backtracking(n, 0, chessboard);
        return res;
    }
    public void backtracking(int n, int row, char[][] chessboard){
        if(row == n){
            res.add(arrayToList(chessboard)); // 将二维数组转化成List<String>
            return;
        }
        for(int i = 0; i < n; i++){
            if(isValid(i, row, chessboard, n)){
                chessboard[row][i] = 'Q';
                backtracking(n, row + 1, chessboard); // 参数别传错了
                chessboard[row][i] = '.';
            }
        }
    }
    public boolean isValid(int col, int row, char[][] chessboard, int n){
        // for(int i = 0; i < col; i++){ 行不用检查，因为前面一定没有元素
        //     if(chessboard[row][i] == 'Q'){
        //         return false;
        //     }
        // }
        for(int i = 0; i < row; i++){
            if(chessboard[i][col] == 'Q'){
                return false;
            }
        }
        for(int i = row, j = col; i > 0 && j > 0; i--, j--){ // 行row列col别看错了
            if(chessboard[i - 1][j - 1] == 'Q'){
                return false;
            }
        }
        for(int i = row - 1, j = col + 1; i >= 0 && j < n; i--, j++){ //检查135°角，j最大值应该是n-1（n是列数）
            if(chessboard[i][j] == 'Q'){
                return false;
            }
        }
        return true;
    }
    public List<String> arrayToList(char[][] chessboard){
        List<String> temp = new ArrayList<>();
        for(char[] row : chessboard){
            temp.add(new String(row));
        }
        return temp;
    }
}
```

- 时间复杂度: O(n!)
- 空间复杂度: O(n)

注：
- 要学会快速填充二维数组；
- 要会将二维数组转化成`List<String>`类型；
- 检查45°和135°角情况时要注意 i 和 j 的范围！