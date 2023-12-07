# Setup

## Configuration files
+ Git 自带 git config 的工具来帮助设置控制 git 外观和行为变量
+ 每个级别的配置都会覆盖上一个级别的配置。

### Linux
1. /etc/gitconfig
    + 当前系统上所有用户和仓库都生效的通用配置。在使用 git config --system 时会读写该文件中的配置变量
2. ~/.gitconfig 或 ~/.config/git/config
    + 只针对当前用户生效的配置，可以通过 --global 选项读写该文件，对该用户的所有仓库生效
3. .git/config
    + 只针对当前仓库生效，使用 --local 选项读写该文件，当然默认的读写文件也是它

### Windows
+ windows 系统中，单用户的配置文件通常保存在 (C:\Users\$User) 的 .gitconfig 文件中

### view
+ 可以通过以下命令查看所有的配置和它们所在的文件
  + `git config --list --show-origin`

## Setting
### 设置用户信息
1. 安装完 git 后，第一件事就是设置用户名和邮箱地址，因为每个 git 提交都需要使用这些信息
   1. `git config --global user.name "<user_name>"`
   2. `git config --global user.email <email_address>`

### 设置文本编辑器
1. 配置 git 文本编译器
   1. `git config --global core.editor <app_name>`
   2. Windows 中如果想要设置别的文本编辑器，需要指定可执行程序的完整路径

## Check
1. 列出所有 git 配置
    + `git config --list` 
2. 查看某一项配置
    + `git config <key>`
3. 获取帮助
    + `git help config`