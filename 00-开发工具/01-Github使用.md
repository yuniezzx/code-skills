### Github 使用
####  注册或登录 
登录[Github](https://github.com/)官网，及 `sign in` 和` sign up` 按钮。注册后可以在手机上下载 Github 软件，该软件可以控制账户登录时的二次验证，查看通知，拉取请求等等。

![github-sign-in-page](https://github.com/yuniezzx/Images/blob/main/code-markdown/devTool/Github-sign-in-page.png?raw=true)

#### 创建仓库
在登录后会跳转到自己的主页面（home page)。 左侧为 repositories 区域。点击 `New` 按钮为创建一个新的仓库。

![create-new-repository](https://github.com/yuniezzx/Images/blob/main/code-markdown/devTool/Create-repository.png?raw=true)

1. 创建当前仓库的名称
2. 公有，指能被所有人看到仓库
3. 私有，可以指定谁能看到仓库
4. 是否添加README 文件(该文件一般放在仓库根目录，展示仓库信息)
5. 添加 .gitignore (本体推送时自动忽略的文件列表)
6. 选择协议


### Git 与 Github 关系
1、Git: Git 是一个开源的分布式版本控制系统，它允许开发者跟踪和管理源代码历史，支持多人协作开发。Git 的核心功能包括版本控制、分支管理、合并和冲突解决等。它是基于命令行的工具，可以在本地计算机上完全离线工作。

2、GitHub: GitHub 是一个基于 Git 的在线托管平台，提供 Git 仓库的托管服务。它为开源项目和个人开发者提供了一系列协作工具，如代码审查、问题跟踪、项目管理、Wiki 页面等。GitHub 还提供了图形用户界面，使得用户可以更容易地使用Git的功能。此外，GitHub 还提供了一些社交网络特性，如关注其他用户、星标项目（star）、对项目代码提出改进建议（fork）和提交拉取请求（pull request）。

综上所述，Git 是一个版本控制系统，而 GitHub 是一个使用 Git 的工具和服务提供者。开发者可以在本地使用 Git 进行版本控制，然后将代码推送到 GitHub 上进行共享和协作。

### 本地 Git 安装
#### 下载
- Linux: 用自带的包管理器安装
- Mac: `brew install git`
- Windows: [Git 官方网站]( https://git-scm.com/download/win) 下载

#### 配置
Git 自带一个 git config 的工具来帮助设置控制 Git 外观和行为的配置变量。这些变量存储在三个不同的位置：
1. /etc/gitconfig 文件: 包含系统上每一个用户及他们仓库的通用配置。
2. ~/.gitconfig 或 ~/.config/git/config 文件：只针对当前用户。 你可以传递 --global 选项让 Git 读写此文件，这会对你系统上 所有 的仓库生效。
3. 当前使用仓库的 Git 目录中的 config 文件（即 .git/config）：针对该仓库。

> 每一个级别会覆盖上一级别的配置，所以 .git/config 的配置变量会覆盖 /etc/gitconfig 中的配置变量。

##### 设置用户
```bash
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
```

使用了 --global 选项，那么该命令只需要运行一次，因为之后无论你在该系统上做任何事情， Git 都会使用那些信息。 
用户信息将保存在~/.gitconfig 中。

##### Git 的 Github 身份验证
从 Git 连接到 GitHub 存储库时，需要使用 HTTPS 或 SSH 向 GitHub 进行身份验证。

使用 https 每次推送或拉取操作时，Git 可能会提示你输入用户名和密码。你可以通过配置 Git 来缓存凭据，以避免每次都输入。

使用 ssh 密钥，这样免去每次都输密码的麻烦。
生成密钥：
```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
或
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
这里 -t ed25519 是指定生成一种较新的、推荐的密钥类型。如果你需要兼容性，可以使用 rsa。
生成后，你的公钥通常保存在 ~/.ssh/id_ed25519.pub（或 ~/.ssh/id_rsa.pub）文件中。

添加公钥到Github
```bash
cat ~/.ssh/id_rsa.pub
```
登录Github ，点击 `Settings -> SSH and GPG keys -> New SSH key`;
将公钥内容复制粘贴到 New SSH key 中。
```bash
ssh -T git@github.com  // 验证 key 是否正常
```




### 参考链接
- [Github入门教程，适合新手学习（非常详细）](https://blog.csdn.net/black_sneak/article/details/139600633)
- [Git - Book](https://git-scm.com/book/zh/v2)