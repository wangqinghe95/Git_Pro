# Git 别名

如果不想每次都输入完整的 Git 命令，就可以通过 Git config 文件为指定的命令设置一些别名

## 创建别名
```
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
```
如果想要输入 `git commit`， 就只需要输入 `git ci`

`git config --global alias.unstage 'reset HEAD --'`
以上的设置可以将以下两条命令等价
```
git unstage fileA
git reset HEAD -- fileA
```

`git config --global alias.last 'log -1 HEAD'`
查看最后一次提交

如果想要给 Git 的外部命令设置一个别名，那么只需要在命令前加一个 ! 即可
`git config --global alias.visual '!gitk'`