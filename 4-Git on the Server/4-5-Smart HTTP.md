# Smart HTTP

一般我们同 SSH 授权进行访问，通过 git:// 进行无授权访问
但是还有一种协议可以同时实现以上两种方式的访问。
设置 Smart HTTP 一般只需要在服务器上启用一个 Git 自带的名为 git-htttp-backend 的 CGI 脚本
该脚本将会读取由 Git fetch 或 git push 命令向 HTTP URL 发送的请求路径和头部信息来判断该客户端是否支持 HTTP 通信。
如果 CGI 发现该客户端支持智能模式（Smart），它将会以智能模式与它通信，否则它将会落到哑（Dumb）模式下

在完成以上简单的安装步骤后，我们将使用 Apache 来作为 CGI 服务器，在 Linux 环境下，可以执行以下的命令安装
```
sudo apt-get install apache2 apache2-utils
a2enmod cgi alias env
```

该操作会启动 mod_cgi,mod_alias 和 mode_env 等 Apache 模板，这些模块都是使功能正常工作所必需的。

还需要将 /srv/git 的 Unxi用户组设置为 www-data，这样 Web 服务器才能读写该仓库，因为运行 CGI 脚本的 Apache 实例默认会以该用户的权限运行
`chgrp -R www-data /srv/git`

接下来向 Apache 配置文件添加一些内容，来让 git-http-backend 作为 Web 服务器对 /git 路径请求的处理器
```
SetEnv GIT_PROJECT_ROOT /srv/git
SetEnv GIT_HTTP_EXPORT_ALL
ScriptAlias /git/ /usr/lib/git-core/git-http-backend/
```

如果留空 GIT_HTTP_EXPORT_ALL 这个环境变量，Git 只会对无授权客户端提供带 git-daemon-export-ok 文件的版本库，就像 Git 守护进程一样

最后，如果想要 Apache 允许 git-http-backend 请求并实现写入操作的授权验证，使用以下授权屏蔽配置：
```
<Files "git-http-backend">
  AuthType Basic
  AuthName "Git Access"
  AuthUserFile /srv/git/.htpasswd
  Require expr !(%{QUERY_STRING} -strmatch '*service=git-receive-pack*'
|| %{REQUEST_URI} =~ m#/git-receive-pack$#)
  Require valid-user
</Files>
```

这需要创建一个包含所有合法密码用户的 .htpasswd 文件。以下是田间一个 "schacon" 用户到此文件的例子
`htppasswd -c /srv/git/.htpasswd schacon`