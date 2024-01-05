# 贮藏和清理

有时，当你在项目的一部分上已经工作一段时间后，所有东西都进入了混乱的状态， 而这时你想要切换到另一个分支做一点别的事情。 问题是，你不想仅仅因为过会儿回到这一点而为做了一半的工作创建一次提交。 针对这个问题的答案是 git stash 命令。

贮藏（stash）会处理工作目录的脏的状态——即跟踪文件的修改与暂存的改动——然后将未完成的修改保存到一个栈上， 而你可以在任何时候重新应用这些改动（甚至在不同的分支上）。

## 贮藏工作

` git stash push -m "<commnet>" <stash_file1> <stash_file2>`
将当前修改保存起来

`git stash list`
查看贮藏列表

`git stash apply`
应用贮藏列表中第一个贮藏，等同于
`git stash pop`
如果想要应用一个非栈顶的贮藏，使用
`git stash apply stash@{<index>}`

`git stash drop stash@{<index>}`
移除指定的贮藏

`git stash pop` 会应用栈顶的贮藏之后从贮藏列表中抛弃该贮藏

## 创意使用

第一个非常流行的选项是 git stash 命令的 --keep-index 选项。 它告诉 Git 不仅要贮藏所有已暂存的内容，同时还要将它们保留在索引中。
`git stash --keep-index`

默认情况下，git stash 只会贮藏已修改和暂存的 已跟踪 文件。 如果指定 --include-untracked 或 -u 选项，Git 也会贮藏任何未跟踪文件。
`git stash -u`

然而，在贮藏中包含未跟踪的文件仍然不会包含明确 忽略 的文件。 要额外包含忽略的文件，请使用 --all 或 -a选项
`git stash -a`

如果指定了 --patch 标记，Git 不会贮藏所有修改过的任何东西，但会交互式地提示哪些改动想要贮藏、哪些改动需要保存到工作目录

`git stash --patch`

## 从贮藏创建一个分支

如果贮藏了一些工作，将它留在那儿了一会儿，然后继续在贮藏的分支上工作，在重新应用工作时可能会有问题。 如果应用尝试修改刚刚修改的文件，你会得到一个合并冲突并不得不解决它。 如果想要一个轻松的方式来再次测试贮藏的改动，可以运行 `git stash branch <new branchname>` 以你指定的分支名创建一个新分支，检出贮藏工作时所在的提交，重新在那应用工作，然后在应用成功后丢弃贮藏.

## 清理工作目录

对于工作目录中一些工作或文件，你想做的也许不是贮藏而是移除。 git clean 命令就是用来干这个的。

清理工作目录有一些常见的原因，比如说为了移除由合并或外部工具生成的东西， 或是为了运行一个干净的构建而移除之前构建的残留。

你需要谨慎地使用这个命令，因为它被设计为从工作目录中移除未被追踪的文件。 如果你改变主意了，你也不一定能找回来那些文件的内容。 一个更安全的选项是运行 git stash --all 来移除每一样东西并存放在栈中。

你可以使用 git clean 命令去除冗余文件或者清理工作目录。 使用 git clean -f -d 命令来移除工作目录中所有未追踪的文件以及空的子目录。 -f 意味着“强制（force）”或“确定要移除”，使用它需要 Git 配置变量 clean.requireForce 没有显式设置为 false。

如果只是想要看看它会做什么，可以使用 --dry-run 或 -n 选项来运行命令， 这意味着“做一次演习然后告诉你 将要 移除什么”。

`git clean -d -n`

默认情况下，git clean 命令只会移除没有忽略的未跟踪文件。 任何与 .gitignore 或其他忽略文件中的模式匹配的文件都不会被移除。 如果你也想要移除那些文件，例如为了做一次完全干净的构建而移除所有由构建生成的 .o 文件， 可以给 clean 命令增加一个 -x 选项。

```
$ git status -s
 M lib/simplegit.rb
?? build.TMP
?? tmp/
$ git clean -n -d
Would remove build.TMP
Would remove tmp/
$ git clean -n -d -x
Would remove build.TMP
Would remove test.o
Would remove tmp/
```

如果不知道 git clean 命令将会做什么，在将 -n 改为 -f 来真正做之前总是先用 -n 来运行它做双重检查。 另一个小心处理过程的方式是使用 -i 或 “interactive” 标记来运行它。
这将会以交互模式运行 clean 命令。

```
$ git clean -x -i
Would remove the following items:
  build.TMP test.o
*** Commands ***
  1: clean 2: filter by pattern 3: select by numbers
4: ask each 5: quit
  6: help
What now>
```

这种方式下可以分别地检查每一个文件或者交互地指定删除的模式。