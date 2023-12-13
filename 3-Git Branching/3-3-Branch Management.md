# 分支管理

1. `git branch`
    - 不加任何运行参数，会得到当前所有分支的一个列表

2. `git branch -v`
    - 查看每个分支的最后一笔提交

3. `git branch --merged`
    - 查看哪些分支已经合并到当前分支中

4. `git branch --no-merged`
    - 查看所有包含未合并工作的分支，默认列出未合并到当前分支

5. `git branch --no-merged <branch_name>`
    - 查看没有合并到指定分支的分支

6. `git branch -D <brnach_name>`
    - 在没有合并的分支中，如果想要删除，需要加上 -D 参数
