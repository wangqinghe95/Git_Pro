# 底层命令与上层命令

上层命名是用来被用户使用的，用户风格友好的版本控制命令

而下层命令则是用于控制系统的工具集，不面向最终用户，更适合作为新工具的组件和自定义脚本的组成部分。

`git init` 后，git 会创建一个 .git 目录，包含了 Git 存储和操作的东西。如果想要备份和复制一个版本库，只需要将该目录拷贝至另一处即可。

```
d-----         12/7/2023  10:27 AM                hooks
d-----         12/7/2023  10:27 AM                info
d-----         12/7/2023  10:27 AM                logs
d-----         1/17/2024   9:23 AM                objects
d-----         12/7/2023  10:27 AM                refs
-a----         1/17/2024   9:23 AM             37 COMMIT_EDITMSG
-a----         12/7/2023  10:27 AM            302 config
-a----         12/7/2023  10:27 AM             73 description
-a----         1/16/2024   2:22 PM             99 FETCH_HEAD
-a----        12/21/2023  11:02 AM             21 HEAD
-a----         1/17/2024   9:23 AM          15624 index
-a----         1/16/2024   2:22 PM             41 ORIG_HEAD
-a----         12/7/2023  10:27 AM            112 packed-refs
------        12/11/2023   9:41 AM             82 TAG_EDITMSG
```

+ description: 供 GitWeb 使用，无需关系
+ config: 包含项目特有的配置选项。
+ info: 包含一个全局性排除（global exclude）文件， 用以放置那些不希望被记录在 .gitignore 文件中的忽略模式（ignored patterns）。
+ hooks: 包含客户端或服务端的钩子脚本（hook scripts）
+ objects: 存储所有数据内容
+ refs: 存储指向数据（分支、远程仓库和标签等）的提交对象的指针；
+ HEAD: 指向目前被检出的分支；
+ index: 保存暂存区信息