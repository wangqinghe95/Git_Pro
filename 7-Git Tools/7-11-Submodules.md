# 子模块

有种情况我们经常会遇到：某个工作中的项目需要包含并使用另一个项目。 也许是第三方库，或者你独立开发的，用于多个父项目的库。 现在问题来了：你想要把它们当做两个独立的项目，同时又想在一个项目中使用另一个。

子模块的意思就是，我现在的一个 git 项目，需要用到另外一个 git 项目，那么我就可以将要用到的另外一个 git 项目作为子模块放到我现在的 git 项目中。

## 开始使用子模块

在已存在的 git 仓库中添加一个子模块
`git submodule add <git_branch>`

例如
`git submodule add https://github.com/chaconinc/DbConnector`

默认情况下，子模块会将子项目放到一个和仓库同名的目录中，如上例是 "DbConnetor"，可以在命令结尾添加一个地址指定放到不同目录

拉取之后，查看一下 git 状态，
```
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
  new file: .gitmodules
  new file: DbConnector
```

1. 文件 .gitmodules
会发生有一个 .gitmodules 文件，里面保留项目 URL 与已经拉取本地目录之间的映射

```
[submodule "DbConnector"]
  path = DbConnector
  url = https://github.com/chaconinc/DbConnector
```

如果包含了多个子模块，该文件就会有多条记录。该文件和 .gitignore 文件一样会收到版本控制。
它会和该项目的其他部分一同被拉取推送。这就是克隆该项目的人知道去哪获得子模块的原因

**注意：**
>> 
由于 .gitmodules 文件中的 URL 是人们首先尝试克隆/拉取的地方，因此请尽可能确保你使用
的 URL 大家都能访问。 例如，若你要使用的推送 URL 与他人的拉取 URL 不同，那么请使用
他人能访问到的 URL。 你也可以根据自己的需要，通过在本地执行 git config
submodule.DbConnector.url <私有URL> 来覆盖这个选项的值。 如果可行的话，一个相
对路径会很有帮助。


2. 文件 DbConnector

git status 显示出的另外一个新文件就是项目文件夹记录，运行   `git diff`

```
$ git diff --cached DbConnector
diff --git a/DbConnector b/DbConnector
new file mode 160000
index 0000000..c3f01dc
--- /dev/null
+++ b/DbConnector
@@ -0,0 +1 @@
+Subproject commit c3f01dc8862123d317dd46284b05b6892c7b29bc
```

`git diff --cached` 是比较 git 中暂存区 和 给定提交 ID 之间的差异，用在以下场景
1. 当已经修改内容添加到暂存区，这时候运行 `git diff` 已经看不到差异了
2. 如果手速太快，已经将工作区的文件通过 `git add` 添加到暂存区，但是又想知道暂存区的变更和给定提交点的差异

只需要加上 --cached 选项即可。例如，我现在将上面的修改已经添加到了暂存区，运行 `git diff --cached HEAD` 即可将暂存区内容与最新一次提交进行比较1。如果省略 commit，那么，就是默认指 HEAD1。

虽然 DbConnector 是工作目录中的一个子目录，但 Git 还是会将它视作一个子模块。当你不在那个目录中时，Git 并不会跟踪它的内容， 而是将它看作子模块仓库中的某个具体的提交。

如果你想看到更漂亮的差异输出，可以给 git diff 传递 --submodule 选项。

```
$ git diff --cached --submodule
diff --git a/.gitmodules b/.gitmodules
new file mode 100644
index 0000000..71fc376
--- /dev/null
+++ b/.gitmodules
@@ -0,0 +1,3 @@
+[submodule "DbConnector"]
+ path = DbConnector
+ url = https://github.com/chaconinc/DbConnector
Submodule DbConnector 0000000...c3f01dc (new submodule)
```

当你提交时，会看到类似下面的信息：
```
$ git commit -am 'added DbConnector module'
[master fb9093c] added DbConnector module
 2 files changed, 4 insertions(+)
 create mode 100644 .gitmodules
 create mode 160000 DbConnector
```

注意 DbConnector 记录的 160000 模式。 这是 Git 中的一种特殊模式，它本质上意味着你是将一次提交记作一项目录记录的，而非将它记录成一个子目录或者一个文件。

最后推送该笔修改
`git push origin master`

## 克隆含有子模块的项目

1. 显示的更新子模块
当我们克隆含有子模块的项目时，默认会包含该模块的目录，但是内部没有任何文件。所以需要运行以下两个命令来抓取子模块的内容
```
git submodule init
git submodule update
```

命令运行成功后，子模块的内容就和远程中该模块里的内容是一致的了。

以上两条命令可以二合一为
`git submodule update --init`

如果还存在嵌套子模块的话，可以使用 
`git submodule update --init --recursive`

2. 递归克隆所有模块
还有一个更简单的方式是在克隆时加上参数 --recurse-submodules ，它会自动初始化并更新仓库中的每一个子模块，包括可能存在的嵌套子模块。

` git clone --recurse-submodules https://github.com/chaconinc/MainProject`


## 在包含子模块的项目上工作

如果我们同时在主项目和子项目上与队员协作，该如何操作。

### 在子模块的远端拉取上游修改

1. 拉取子模块
只使用不修改时使用子模块的最简单的方式。在子模块的文件夹中，运行以下命令来合并上游分支更新本地代码
```
git fetch
git merge origin/master
```

在目录中运行 `git diff --submodule` 查看子模块被更新的同时获得一个包含新添加提交的列表
如果不想每次运行 `git diff` 时都要输入 --submodule，那么可以将 diff.submodule 设置为 log 将其作为默认行为
`git config --global diff.submodule log`

如果不想在子目录中手动抓取与合并，可以使用以下命令，让 Git 自动抓取并更新子模块
`git submodule update --remote <git_name>`

本例中，使用命令
`git submodule update --remote DbConnector`

Git 会默认检出子模块仓库里的 master 分支，如果想要获取其他分支，可以在 .gitmodules 文件中设置，也可以在 .git/config 中设置
本例在 .gitmodules 文件中设置
`git config -f .gitmodules submodule.DbConnector.branch stable`

设置子模块 DbConnector 检出分支为 stable

`-f .gitmodules` 是为了将本项设置记录，并且其他拉取了该项提交的人也能得到同样的项目，如果只是单纯作用于自己的本地项目，那么可以不加

2. 查看子模块设置变动
`git config status.submodulesumary 1` 设置 Git 显示子目录的更改摘要

该项设置完成后可以使用 `git diff` 查看修改的 .gitmodules 文件内容

该项修改提交后，可以使用 `git log -p --submodule` 查看子模块内容的提交

### 在项目远端拉取上游更改

另一个主项目的协同开发者在你提交了子项目的修改后，该如何同步？

单单一个 git pull 命令是不够的，因为默认情况下 git pull 会递归地抓取子模块的更改，但是不会更新子模块。

想要完成更新，还需要运行
`git submodule update --init --recursive`

如果想要自动化完成这个过程，可以配置 git pull 命令，Git 2.14 版本以后支持 `git pull --recursive-submodules`
这个命令会让 Git 在拉取后运行 `git submodule update`，将子模块置为正确的状态。

此外，如果想要 Git 一直以 --recursive-submodules 拉取，可以配置选项 submodule.recurse 设置为 true（Git 2.15 版本以后可以作用于 git pull），此选项会让 Git 为所有支持 --recursive-submodules 的命令使用该选项（除 clone）

在父项目在拉取更新时，如果 .gitmodules 文件中的子模块的 URL 发生了变化，比如改变了它的托管平台。
此时执行拉取或者更新子模块项目时就会发生错误。
结局方法是使用下方更新命令，更新子模块的 URL
```
git submodule sync --recursive
git submodule update --init --recursive
```

### 在子模块上工作

如何既能在主项目上工作的同时，又能在子模块中工作呢？

我们在运行 `git submodule update ` 从子模块仓库中抓取修改，Git 将会获得这些改动并且更新子目录中的文件，但是会将子仓库留在一个称作“游离的 HEAD”状态。这意味着没有本地工作分支追踪改动。
如果没有工作分支追踪改动，也就意味着即便将本地的更改提交到字模块中，这些改动也会在下次运行 `git submodule update ` 时丢失。

所以若想要在一个主项目的子模块中做修改提交，可以按照以下步骤执行：

1. 设置子模块

在子模块的目录中：切换到对应的分支
`git switch branch`

在父项目目录下：拉取子模块远端分支内容
`git submodule update --remote --merge`

2. 修改提交

修改子模块的内容，在子模块目录下执行 添加，推送命令：
```
git commit -am "<comment>"
git push -u origin <origin_branch>
```

这样在主项目中的子模块修改已经成功的提交到远端了。

### 发布子模块改动

如果我们子模块做了一些改动，而主项目中的某些修改依赖子模块的改动，那么如果只推送主项目中的提交而没有推送子模块的改动的话，就会导致其他协作的人检出我们的本次改动会产生问题。

为了确保这种情况不会发生，我们可以在 Git 推送主项目之前，检查所有子模块的内容是否已经被推送。

git push 命令可以介绍设置为 “check” 或者 “on-demand” 的 --recursive-submodules 参数。
如果任何提交的子模块改动没有推送，那么“check”选项会直接使 push 操作失败。

`git push --recurse-submodules=check`


最简单的选项是进入每一个子模块中然后手动推送到远程仓库，确保它们能被外部访问到，之后再次尝试这次推送

如果你想要对所有推送都执行检查，那么可以通过设置 `git config push.recurseSubmodules check` 让它成为默认行为。

另一个选项是使用 “on-demand” 值，它会尝试为你这样做。
`git push --recurse-submodules=on-demand`
Git 会进入子模块中然后再推送主项目之前推送它

也可以通过设置 `git config push.recurseSubmodules on-demand` 让它成为默认行为。


### 合并子模块改动

如果两个协作者都对主项目中的子模块产生了改动，那么必会有一个人需要为此做一些合并操作。


#### 方法1
如果子模块提交已经分叉需要合并，那么在 git pull 拉取的时候会产生提示冲突的提示：
```
$ git pull
remote: Counting objects: 2, done.
remote: Compressing objects: 100% (1/1), done.
remote: Total 2 (delta 1), reused 2 (delta 1)
Unpacking objects: 100% (2/2), done.
From https://github.com/chaconinc/MainProject
  9a377d1..eb974f8 master -> origin/master
Fetching submodule DbConnector
warning: Failed to merge submodule DbConnector (merge following commits
not found)
Auto-merging DbConnector
CONFLICT (submodule): Merge conflict in DbConnector
Automatic merge failed; fix conflicts and then commit the result.
```

Git 在这里指出了子模块历史中的两个分支记录点已经分叉并且需要合并。 它将其解释为 “merge following commits not found” （未找到接下来需要合并的提交）

当遇到这种情况时，先使用 `git diff` 查看试图合并的两个分支中记录的提交的 SHA-1 值

```
$ git diff
diff --cc DbConnector
index eb41d76,c771610..0000000
--- a/DbConnector
+++ b/DbConnector
```

上述输出中，eb41d76 是两笔冲突提交的共同祖先，而 c771610 是上游拥有的提交

如果我们进入子模块目录中，它应该已经在 eb41d76 上了，因为合并没有动过它。 如果不是的话，无论什么原因，你都可以简单地创建并检出一个指向它的分支。

来自另一边的提交的 SHA-1 值比较重要。 它是需要你来合并解决的。 你可以尝试直接通过 SHA-1 合并，也可以为它创建一个分支然后尝试合并。 我们建议后者，哪怕只是为了一个更漂亮的合并提交信息。

所以，我们将会进入子模块目录，基于 git diff 的第二个 SHA-1 创建一个分支然后手动合并。
```
cd DbConnector
git rev-parse HEAD
git branch try-merge c771610
```

我们在这儿得到了一个真正的合并冲突，所以如果想要解决并提交它，那么只需简单地通过结果来更新主项目。

1. 首先解决冲突
2. 然后返回到主项目目录中
3. 再次检查 SHA-1 值
4. 解决冲突的子模块记录
5. 提交我们的合并

如果子模块目录中存在着这样一个合并提交，它的历史中包含了的两边的提交，那么 Git 会建议你将它作为一个可行的解决方案。 它看到有人在子模块项目的某一点上合并了包含这两次提交的分支，所以你可能想要那个。

#### 方法2

如果子模块目录中存在着这样一个合并提交，它的历史中包含了的两边的提交，那么 Git 会建议你将它作为一个可行的解决方案。 它看到有人在子模块项目的某一点上合并了包含这两次提交的分支，所以你可能想要那个。

这就是为什么前面的错误信息是 “merge following commits not found”，因为它不能这样做。 它让人困惑是因为谁能想到它会尝试这样做？

如果它找到了一个可以接受的合并提交，你会看到类似下面的信息：
```
$ git merge origin/master
warning: Failed to merge submodule DbConnector (not fast-forward)
Found a possible merge resolution for the submodule:
 9fd905e5d7f45a0d4cbc43d1ee550f16a30e825a: > merged our changes
If this is correct simply add it to the index for example
by using:
  git update-index --cacheinfo 160000
9fd905e5d7f45a0d4cbc43d1ee550f16a30e825a "DbConnector"
which will accept this suggestion.
Auto-merging DbConnector
CONFLICT (submodule): Merge conflict in DbConnector
Automatic merge failed; fix conflicts and then commit the result.
```

Git 建议的命令是更新索引，就像你运行了 git add 那样，这样会清除冲突然后提交。 不过你可能不应该这样做。你可以轻松地进入子模块目录，查看差异是什么，快进到这次提交，恰当地测试，然后提交它。

```
$ cd DbConnector/
$ git merge 9fd905e
Updating eb41d76..9fd905e
Fast-forward
$ cd ..
$ git add DbConnector
$ git commit -am 'Fast forwarded to a common submodule child'
```

这些命令完成了同一件事，但是通过这种方式你至少可以验证工作是否有效，以及当你在完成时可以确保子模块目录中有你的代码。

## 子模块的技巧

你可以做几件事情来让用子模块工作轻松一点儿。

### 子模块的遍历

有一个 foreach 子模块命令，它能在每一个子模块中运行任意命令。 如果项目中包含了大量子模块，这会非常有用。
例如，假设我们想要开始开发一项新功能或者修复一些错误，并且需要在几个子模块内工作。 我们可以轻松地保存所有子模块的工作进度。

`git submodule foreach 'git stash'`

然后我们可以创建一个新分支，并将所有子模块都切换过去。
`git submodule foreach 'git checkout -b featureA'`

你应该明白。 能够生成一个主项目与所有子项目的改动的统一差异是非常有用的。

### 有用的别名

你可能想为其中一些命令设置别名，因为它们可能会非常长而你又不能设置选项作为它们的默认选项。 我们在 Git 别名 介绍了设置 Git 别名， 但是如果你计划在 Git 中大量使用子模块的话，这里有一些例子。
```
$ git config alias.sdiff '!'"git diff && git submodule foreach 'git diff'"
$ git config alias.spush 'push --recurse-submodules=on-demand'
$ git config alias.supdate 'submodule update --remote --merge'
```

这样当你想要更新子模块时可以简单地运行 git supdate，或 git spush 检查子模块依赖后推送。

## 子模块的问题

### 切换分支
例如，使用 Git 2.13 以前的版本时，在有子模块的项目中切换分支可能会造成麻烦。 如果你创建一个新分支，在其中添加一个子模块，之后切换到没有该子模块的分支上时，你仍然会有一个还未跟踪的子模块目录。

移除那个目录并不困难，但是有一个目录在那儿会让人有一点困惑。 如果你移除它然后切换回有那个子模块的分支，需要运行 submodule update --init 来重新建立和填充。

再说一遍，这真的不难，只是会让人有点儿困惑。

新版的 Git（>= 2.13）通过为 git checkout 命令添加 --recurse-submodules 选项简化了所有这些步骤，它能为了我们要切换到的分支让子模块处于的正确状态。

当你在父级项目的几个分支上工作时，对 git checkout 使用 --recurse-submodules 选项也很有用， 它能让你的子模块处于不同的提交上。确实，如果你在记录了子模块的不同提交的分支上切换， 那么在执行 git status 后子模块会显示为“已修改”并指出“新的提交”。
这是因为子模块的状态默认不会在切换分支时保留。

这点非常让人困惑，因此当你的项目中拥有子模块时，可以总是使用 `git checkout --recurse-submodules`。 （对于没有 --recurse-submodules 选项的旧版 Git，在检出之后可使用 `git submodule update --init --recursive` 来让子模块处于正确的状态）。

幸运的是，你可以通过 `git config submodule.recurse true` 设置 submodule.recurse 选项， 告诉Git（>=2.14）总是使用 --recurse-submodules。 如上所述，这也会让 Git 为每个拥有 --recurse -submodules 选项的命令（除了 git clone） 总是递归地在子模块中执行。

### 从子目录切换到子模块

另一个主要的告诫是许多人遇到了将子目录转换为子模块的问题。 如果你在项目中已经跟踪了一些文件，然后想要将它们移动到一个子模块中，那么请务必小心，否则 Git 会对你发脾气。 假设项目内有一些文件在子目录中，你想要将其转换为一个子模块。 如果删除子目录然后运行 submodule add，Git 会朝你大喊：

```
$ rm -Rf CryptoLibrary/
$ git submodule add https://github.com/chaconinc/CryptoLibrary
'CryptoLibrary' already exists in the index
```

你必须要先取消暂存 CryptoLibrary 目录。 然后才可以添加子模块：

```
$ git rm -r CryptoLibrary
$ git submodule add https://github.com/chaconinc/CryptoLibrary
Cloning into 'CryptoLibrary'...
remote: Counting objects: 11, done.
remote: Compressing objects: 100% (10/10), done.
remote: Total 11 (delta 0), reused 11 (delta 0)
Unpacking objects: 100% (11/11), done.
Checking connectivity... done.
```

现在假设你在一个分支下做了这样的工作。 如果尝试切换回的分支中那些文件还在子目录而非子模块中时——你会得到这个错误：

```
$ git checkout master
error: The following untracked working tree files would be overwritten by
checkout:
  CryptoLibrary/Makefile
  CryptoLibrary/includes/crypto.h
  ...
Please move or remove them before you can switch branches.
Aborting
```

当你切换回来之后，因为某些原因你得到了一个空的 CryptoLibrary 目录，并且 git submodule update 也无法修复它。 你需要进入到子模块目录中运行 git checkout . 来找回所有的文件。 你也可以通过 submodule foreach 脚本来为多个子模块运行它。

你可以通过 checkout -f 来强制切换，但是要小心，如果其中还有未保存的修改，这个命令会把它们覆盖掉。

```
$ git checkout -f master
warning: unable to rmdir CryptoLibrary: Directory not empty
Switched to branch 'master'
```

当你切换回来之后，因为某些原因你得到了一个空的 CryptoLibrary 目录，并且 git submodule update 也无法修复它。 你需要进入到子模块目录中运行 git checkout . 来找回所有的文件。 你也可以通过submodule foreach 脚本来为多个子模块运行它。

要特别注意的是，近来子模块会将它们的所有 Git 数据保存在顶级项目的 .git 目录中，所以不像旧版本的Git，摧毁一个子模块目录并不会丢失任何提交或分支。

拥有了这些工具，使用子模块会成为可以在几个相关但却分离的项目上同时开发的相当简单有效的方法。

