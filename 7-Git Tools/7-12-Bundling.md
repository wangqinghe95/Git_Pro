# 打包

## 1. 打包

打包命令 `git bundle` 该命令是将 git push 命令所传输的所有内容打包成一个二进制文件

使用场景是当我们无法连接服务器时，如网路中断，没有权限，又希望通过邮件发送给别人时。
虽然 format-patch 也能达到以上场景的使用效果，但是如果提交的 commit 一旦过多就很麻烦。

`git bundle create <file_name> <reference_name> <branch_name>`

例如
`git bundle create repo.bundle HEAD master`

有一个名为 repo.bundle 的文件，该文件包含了所有重建 master 仓库分支所需的所有数据，HEAD 是该仓库被重建时的引用

这样就可以通过邮件或者 U 盘将 master 仓库分支传给别人了。

## 2. 克隆打包

拿到步骤1 的打包文件后，我们需要从该打包中拿出仓库分支

`git clone repo.bundle repo`

如果在打包时没有设置引用，那么还需要再命令后加 -b master 或者其他被引入的分支名，否则 Git 不知道应该检出哪个分支

### 3. 打包回数据

当我们在上述过程中克隆出来的分支中做了修改后，如果想要用邮件或 U 盘将新的提交用打包方式传回去该如何操作？

假设有个 3 个新的提交想要打包送回去

1. 确定打包区间

打包需要手动计算最小数据集，虽然也可以整个分支打包回去，但是最好仅打包变更的部分，也就是上述提到的 3 个新的提交

我们使用 origin/master..master 或者 master ^origin/master 方法来获取那 3 个在我们的 master 分支而不在原始仓库中的提交。我们可以使用 git log 来测试以下
`git log --oneline master ^origin/master`

2. 打包提交

打包命令格式如下：
`git bundle create <bundle_name> <range_bundle>`

示例：
`git bundle create commits.bundle master ^origin/master`

第二种打包命令是：

假设当前的提交历史如下：
```
$ git log --oneline
71b84da last commit - second repo
c99cf5b fourth commit - second repo
7011d3d third commit - second repo
9a466c5 second commit
b1ec324 first commit
```

那么我们使用命令以下命令也能达到同样的效果
`git bundle create commits.bundle master ^9a466c5`

接下来，我们就可以把 commits.bundle 这个文件发送给我们的合作者。

3. 导入包内容

当我们的合作者拿到我们发送过去的提交包，或者说当我们收到我们的合作者发送过来的合作包，应该如何处理？

以步骤 2 发过来的包为例，假设包文件的存放位置就是当前目录

首先，先检查一下 Git 包是否合法
`git bundle verify commits.bundle`

bundle verify 命令可以检查这个文件是否是一个合法的 Git 包，是否拥有共同的祖先来导入。

如果检查结果是有问题的，那么该命令会报错，如果是合法的，我们就可以从该包中提取提交，并导入分支

导入之前，我们先列出顶端
`git bundle list-heads commits.bundle`

使用 fetch 或者 pull 命令从包中导出提交，假设我们从包中取出 master 分支到我们本地仓库的 'other-master' 分支
`git fetch commits.bundle master:other-master`

4. 查看导入后的记过
   
`git log --oneline -decorate --graph --all`