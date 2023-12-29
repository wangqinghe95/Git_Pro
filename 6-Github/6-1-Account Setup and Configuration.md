# Github 账户创建和配置

1. 创建 Github 账户
   + 直接访问 https://github.com，选择一个未被占用的用户名，提供一个电子邮件地址和密码，点击写着“Sign up for GitHub”的绿色大按钮即可。
   + 注册之后，Github 会给我们提供的邮件地址发一封验证邮件，需要我们先去邮件中验证后继续下一步。

2. 配置 SSH 访问
    1. 本地电脑中生成一个 ssh-key
    2. 将 ~/.ssh/id_rsa.pub 中的内容复制到 Github 账户中 **设置**-》**ssh-keys**中

3. 邮件地址
   1. Github 可以使用用户邮件区分 Git 提交，如果在提交中使用了多个邮件地址，希望 Github 能够正确地将它们来连接起来，需要在管理页面中 Emails 部分中添加个人所拥有的所有邮箱地址

4. 两步验证
   1. 为了额外的安全性，可以设置两步验证。
   2. 你可以在 Account settings 页面的 Security 标签页中找到 Two-factor Authentication 设置。
   3. 点击“Set up two-factor authentication”按钮，会跳转到设置页面。该页面允许你选择是要在登录时使用手机 app 生成辅助码（一种“基于时间的一次性密码”），还是要 GitHub 通过 SMS 发送辅助码。
   4. 选择合适的方法后，按照提示步骤设置 2FA，你的账户会变得更安全，每次登录 GitHub 时都需要提供除密码以
外的辅助码。