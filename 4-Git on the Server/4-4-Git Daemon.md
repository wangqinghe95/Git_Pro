# Git 守护进程

接下来我们将通过 "Git" 协议建立一个基于守护进程的仓库，来实现一个快速且无需授权的 Git 数据访问。因为其不包含授权服务，任何通过该协议管理的内容将在其网络上公开

如果运行在防火墙之外的服务器上，它应该只对那些公开的只读项目服务。如果运行在防火墙之内的服务器上，它可用于支撑大量参与人员或自动系统（用于持续集成或编译的主机）只读访问的项目，这样就省去逐一配置 SSH 公钥的麻烦。

无论何时，该 Git 协议都是相对容易设定的。通常只需要执行以下命令
`git daemon --reuseaddr --base-path=/srv/git/ /srv/git`

1. --reuseraddr 是运行服务器在无需等待的旧连接超时的情况下重启
2. --base-path 允许用户在未完全指定路径的条件下克隆项目，结尾的路径是告诉 Git 守护进程从何处寻找仓库来导出
3. 如果有防火墙正在运行，需要开放端口 9418 权限

在 Linux 发行版中，在 /etc/systemd/system/git-daemon.service 中放一个文件即可，文件内容如下
```
[Unit]
Description=Start Git Daemon
[Service]
ExecStart=/usr/bin/git daemon --reuseaddr --base-path=/srv/git/ /srv/git/
Restart=always
RestartSec=500ms
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=git-daemon
User=git
Group=git
[Install]
WantedBy=multi-user.target
```

注意， git 启动的 Git 驻留程序同时使用了 Group 和 User 权限，按需修改它并保证提供在此系统上
此外确保 Git 的二进制文件位于 /usr/bin/git,必要时需要修改该路径

最后，运行启动命令
`systemctl enable git-daemon`
停止命令是
`systemctl stop git-daemon`

接下来，还需要告诉 Git 哪些仓库允许基于服务器的无授权访问，可以在每个仓库下创建一个名为 git-daemon-export-ok 的文件来实现
```
cd /path/to/project.git
touch git-daemon-export-ok
```
该文件运行 Git 提供无需授权的项目访问服务