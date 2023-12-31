# Tags

Git 可以给仓库历史中的某一个提交打上标签，以示重要，比较有代表性的就是使用标签来标记发布节点。

## 列出标签
1. `git tag`
   1. 按字母顺序列出标签
2. `git tag -l "pattern"`
   1. 按通配符列出标签

## 创建标签
Git 支持两种标签，轻量标签与附注标签

### 轻量标签
轻量标签是某个特定提交的引用,轻量标签本质上是将提交校验存储到一个文件中，没有保存其他信息
创建轻量标签不需要使用 -a, -S, -m 等选项，只需要提供标签名字

1. `git tag <tag_name>`
   1. 给当前提交打上轻量标签

### 附注标签
附注标签是存储在 Git 数据库中的一个完整对象，可以被校验。
包含了打标签者的姓名，电子邮箱地址，日期时间，还包括一个标签信息

1. 添加标签
   1. `git tag -a <tag_name> -m "<tag_message>"`
   2. -m 通常会指定一条将会存储在标签中的信息，如果没有为附注标签指定一条信息，Git 会启动编辑器要求你输入信息

2. 显示标签
   1. `git show <tag_name>`

### 后期打标签
Git 支持对历史提交打上标签

`git tag -a <tag_name> <submit_check_id> -m "tag_message"`

其中 <submit_check_id> 可以由 `git log --pretty=oneline` 获取

## 标签推送
默认情况下，git push 不会将标签推送到远程仓库服务器。如果想要推送到远方，需要显示的使用以下推送命令

`git push origin <tag_name>`

一次性推送很多标签，可以使用命令

`git push origin --tags`

## 删除标签
要删除本地仓库的标签，可使用命令 
`git tag -d <tag_name>`

如果想要删除远方仓库的标签，必须使用
`git push <remote> :/refs/tags/<tag_name>`
上述命令的含义是：将冒号前的空值推送到远程标签名，从而高效地删除它

第二种更直观的删除远方仓库的标签
`git push origin --delete <tag_name>`

## 检出标签
如果想要查看某个标签所指向的文件版本，可以使用 `git checkout` 命令，但是这样的操作会导致你的本地仓库处于“分离头指针”的状态

在“分离头指针”的状态下，如果做了某些更改然后提交他们，标签不会发生变化，而且新的提交将不属于任何分支，并且无法访问，除非通过确切的提交哈希才能访问

所以如果需要修改标签所指向的文件，那么通常需要创建一个新的分支
`git checkout -b <new_branch_name> <tag_name>`
