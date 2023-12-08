# 撤销操作

1. `git commit --amend`
   1. 补充上次提交。完全用一个新的提交替换旧的提交

2. `git reset HEAD <file>`
   1. 回退 file，即撤销对 file 的操作，将文件 <file> 回退到工作区中

3. `git chechout -- <file>`
   1. 删除 file 的修改，git 会使用最近提交的版本覆盖掉它