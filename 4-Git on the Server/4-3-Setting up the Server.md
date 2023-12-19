# 配置服务器

介绍一下使用 authorized_keys 方法对用户进行认证，假设使用的操作系统是标准的 Linux 发行版，如 Ubuntu

1. 创建一个操作系统用户 git，并为其建立一个 .ssh 目录
    + 可以使用 ssh-copy-id 命令完成
```
sudo adduser git
su git
cd
mkdir .ssh && chmod 700 .ssh
touch .ssh/authorized_keys && chmod 600  .ssh/authorized_keys
```

2. 为系统用户 git 的 authorized_keys 文件添加一些开发者的 SSH 公钥

假设密钥存储在临时文件 /tmp/id_rsa_<user_id>.pub，有以下三个临时文件
/tmp/id_rsa_john.pub
/tmp/id_rsa_josie.pub
/tmp/id_rsa_jessica.pub

执行以下命令将这些临时文件的公钥添加到系统用户 git 中 .ssh 目录下的  authorized_keys 文件的末尾
```
cat /tmp/id_rsa_john.pub >> ~/.ssh/authorized_keys
cat /tmp/id_rsa_josie.pub >> ~/.ssh/authorized_keys
cat /tmp/id_rsa_jessica.pub >> ~/.ssh/authorized_keys
```

3. 为开发者新建一个空仓库

借助 --bare 选项的 `git init` 命令，这个命令在初始化仓库时不会创建工作目录
```
cd /srv/git
mkdir project.git
cd project.git
git init --bare
```

4. 用户推送文件

此时，john，joise，jessica 中的任意一个人就可以将他们的最初版本推送到这个仓库中，只需要将此仓库设置为项目的远程仓库并向其推送分支。
注意，每添加一个新项目，都需要有人登录服务器取得 shell，并创建一个裸仓库。

我们假定这个设置了 git 用户的 Git 仓库的服务器使用 gitserver 作为主机名。同时该服务器运行在内网，并且 DNS 配置中将 gitserver 指向此服务器
那么我们运行如下命令
```
# on John's computer
cd myproject
git init
git add .
git commit -m "initial commit"
git remote add origin git@gitserver:/srv/git/project.git
git push origin master
```

5. 其他用户克隆

最终版本代码推送成功后，其他开发者就可以克隆此仓库，并推回各自的改动，命令如下：
```
git clone git@gitserver:/srv/git/project.git
cd project
vim README
git commit -am "fix for README file"
git push origin mster
```

以上步骤就完成了一个快速搭建具有读写权限，并且面向多个开发者的 Git 服务器

需要注意的是，目前所有开发者用户都能以系统用户 Git 的身份登录服务器从而获得一个普通权限的 shell，如果你想对此加以限制的话，可以修改 /etc/passwd 文件中 git 用户对应的 shell 值

借助一个 git-shell 的受限 shell 工具，可以方便地将用户 git 地活动限制在 Git 相关地范围内，该工具随 Git 软件包一同提供。
如果将 git-shell 设置为用户 git 的登录 shell，那么该用户便不能登录此服务器的普通 shell 访问权限。如果要使用  git-shell，需要用它替换 bash 或 csh，使其成为该用户的登录 shell，为进行此操作，首先必须保证 git-shell 的完整路径名已存在 /etc/shells
```
cat /etc/shells
which git-shell
sudo -e /etc/shells
```

可以使用 chsh <username> -s <shell> 命令修改任一系统用户的 shell
`sudo chsh git -s $(which git-shell)`

这样用户就只能利用 SSH 连接对 Git 仓库进行推送和拉取操作，而不能登录及其并取得普通 shell。

