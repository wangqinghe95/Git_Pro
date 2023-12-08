# 查看远程仓库

1. `git clone <URL>`
   1. 从已经配置好的远程仓库克隆 git 仓库到本地

2. `git remote -v`
   1. 显示需要读写远程仓库使用的 git 保存的简写与其对应的 URL

3. `git remote add <shortname> <url>`
   1. 添加一个新的远程 Git 仓库，并同时指定一个方便使用的简写

4. `git fetch <shortname>`
   1. 拉取 <shortname> 代表的远方仓库，效果等同于
   2. `git fetch <url>`

5. `git push <remote> <branch>`
   1. 将 master 分支推送到 origin 服务器上
   2. 前置条件，有克隆服务器的写入权限，且远方分支和本地分支保持一致

6. `git remote show origin`
   1. 查看远方仓库的更多信息

7. `git remote rename <old-name> <new_name>`
   1. 修改远方仓库的简写名
   2. 会修改所有远程跟踪的分支名称

8. `git remote remove <shortname>` 或 `git remote rm`
   1. 删除一个远方仓库
   2. 删除与这个仓库仓库相关的远程跟踪分支以及配置信息