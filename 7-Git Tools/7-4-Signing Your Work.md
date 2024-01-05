# 签署工作

Git 虽然是密码级安全的，但它不是万无一失的。 如果你从因特网上的其他人那里拿取工作，并且想要验证提交是不是真正地来自于可信来源， Git 提供了几种通过 GPG 来签署和验证工作的方式。

## GPS 介绍

首先，在开始签名之前你需要先配置 GPG 并安装个人密钥。
`gpg --list-keys`

如果你还没有安装一个密钥，可以使用 gpg --gen-key 生成一个。
`gpg --gen-key`

一旦你有一个可以签署的私钥，可以通过设置 Git 的 user.signingkey 选项来签署。

`git config --global user.signingkey <gpg-key>`

现在 Git 默认是用密钥来签署标签和提交

## 签署标签

如果已经设置好一个 GPG 私钥，可以使用它来签署新的标签。 所有需要做的只是使用 -s 代替 -a 即可：

`git tag -s v1.5 -m 'my signed 1.5 tag'`

如果在那个标签上运行 git show，会看到你的 GPG 签名附属在后面：

`git show v1.5`

## 验证标签

要验证一个签署的标签，可以运行 `git tag -v <tag-name>`。 这个命令使用 GPG 来验证签名。 为了验证能正常工作，签署者的公钥需要在你的钥匙链中。

## 签署提交

在最新版本的 Git 中（v1.7.9 及以上），也可以签署个人提交。 如果相对于标签而言你对直接签署到提交更感兴趣的话，所有要做的只是增加一个 -S 到 git commit 命令。

`git commit -a -S -m 'signed commit'`

git log 也有一个 --show-signature 选项来查看及验证这些签名。
`git log --show-signature -1`

另外，也可以配置 git log 来验证任何找到的签名并将它们以 %G? 格式列在输出中。
`git log --pretty="format:%h %G? %aN %s"`

这里我们可以看到只有最后一次提交是签署并有效的，而之前的提交都不是。
在 Git 1.8.3 及以后的版本中，git merge 与 git pull 可以使用 --verify-signatures选项来检查并拒绝没有携带可信 GPG 签名的提交。
如果使用这个选项来合并一个包含未签名或有效的提交的分支时，合并不会生效。

`git merge --verify-signatures non-verify`

如果合并包含的只有有效的签名的提交，合并命令会提示所有的签名它已经检查过了然后会继续向前。

`git merge --verify-signatures signed-branch`

也可以给 git merge 命令附加 -S 选项来签署自己生成的合并提交。 下面的例子演示了验证将要合并的分支的每一个提交都是签名的并且签署最后生成的合并提交。

`git merge --verify-signatures -S signed-branch`

## 每个人必须签署
签署标签与提交很棒，但是如果决定在正常的工作流程中使用它，你必须确保团队中的每一个人都理解如何这样做。 如果没有，你将会花费大量时间帮助其他人找出并用签名的版本重写提交。 在采用签署成为标准工作流程的一部分前，确保你完全理解 GPG 及签署带来的好处。
