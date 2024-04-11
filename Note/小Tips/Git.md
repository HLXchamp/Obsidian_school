在Git中更改全局用户名和电子邮件地址：

```bash
git config --global user.name "新用户名"
git config --global user.email "新邮箱地址"
```

Idea上若想**删除分支**需要提前移动到另一个分支。
- 可以直接在窗口上删；
- 也可以用命令：
步骤一：查看本地所有分支  
首先，我们可以使用`git branch`命令查看当前本地所有的分支。这将列出所有本地分支，当前分支会有一个星号 `*` 标记。

步骤二：选择要删除的分支  
根据查看的结果，选择要删除的分支。

步骤三：删除本地分支  
使用`git branch -d`加分支名 删除本地分支。如果遇到删除分支的时候出现错误信息，可以使用`-D`选项强制删除。`git branch -D branchname`  `

删除分支后，可以使用`git branch`命令再次确认分支是否已被删除。

步骤四：巧妙删除多个分支  
如果需要一次性删除多个分支，可以使用`git branch -d`命令的参数`-d`结合分支名一起使用。或者，也可以使用`-D`选项来删除多个分支。`git branch -D branchname1 branchname2 branchname3`  


