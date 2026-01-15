# 本地文件夹关联 GitHub 全新远程仓库（命令行完整教程）

## 一、教程目标

将本地新建文件夹，通过 VSCode 命令行全程操作，关联到 GitHub 上全新创建的远程仓库，无需手动打开 GitHub 网页，实现本地与远程仓库的双向同步，适合新手入门教学。

## 二、前置条件

- - 系统环境：Windows（基于 winget 包管理器，统一操作口径）

- - 工具依赖：Git（VSCode 通常自带，无则需提前安装）、GitHub CLI（gh，官方命令行工具）

- - 准备工作：拥有 GitHub 账号，本地已新建目标文件夹（示例路径：`F:\GITHUBfiles\farm`）

## 三、完整操作步骤（按顺序执行，新手可直接复制命令）

### 步骤 1：打开 VSCode 内置终端

1. 1. 打开 VSCode，通过「文件 → 打开文件夹」，选择要关联的本地新文件夹；

2. 2. 按下快捷键 `Ctrl + `` `（反引号，Tab 键上方），打开内置终端（默认 PowerShell 环境，无需切换）；

3. 3. 终端默认路径会自动定位到当前打开的文件夹，可通过 `pwd` 命令验证路径是否正确。

### 步骤 2：安装 GitHub CLI（gh，系统级安装，任意目录可执行）

gh 是 GitHub 官方命令行工具，可直接在终端创建远程仓库，替代网页操作，提升效率：

# Windows 专属安装命令，执行后需授予管理员权限
winget install GitHub.cli

备注：安装时会弹出「用户账户控制」弹窗，点击「是」即可；安装完成后必须关闭当前终端，重新打开 VSCode 终端（让 gh 命令生效，新手易忽略此步）。

### 步骤 3：登录 GitHub 账号（验证安装+授权）

# 执行登录命令，按终端提示逐步操作
gh auth login

按以下提示选择（新手直接回车默认选项即可）：

1. 1. 选择平台：`GitHub.com`（默认第一项，回车）；

2. 2. 选择协议：`HTTPS`（默认第一项，回车，新手推荐 HTTPS 协议，无需配置 SSH 密钥）；

3. 3. 选择登录方式：`Login with a web browser`（默认第一项，回车）；

4. 4. 终端会生成一串验证码，同时自动打开浏览器的 GitHub 授权页面；

5. 5. 在浏览器中输入验证码，登录自己的 GitHub 账号，点击「授权 GitHub CLI」；

6. 6. 回到终端，看到 `? Logged in as 你的用户名` 提示，即登录成功。

验证登录状态（可选，确保无问题）：

gh auth status

正常输出示例（替换为实际用户名）：

github.com
  ? Logged in to github.com account timeol21 (keyring)
  - Active account: true
  - Git operations protocol: https
  - Token: gho_************************************
  - Token scopes: 'gist', 'read:org', 'repo', 'workflow'

输出含「?」标识即表示登录正常，权限足够操作仓库。

### 步骤 4：初始化本地 Git 仓库

确保终端路径为目标本地文件夹，执行以下命令初始化 Git 管理（首次执行会生成隐藏的 .git 文件夹，用于跟踪文件变化）：

# 初始化本地 Git 仓库
git init

# 可选：再次验证当前路径，避免操作错文件夹
pwd

正常输出：`Initialized empty Git repository in F:/GITHUBfiles/farm/.git/`，表示初始化成功。

### 步骤 5：在 GitHub 上创建全新远程仓库

通过 gh 命令一键创建远程仓库（示例仓库名：GITHUBfiles，可按需修改）：

# 创建公开远程仓库（如需私有，替换 --public 为 --private）
# --clone=false 表示仅创建远程仓库，不生成本地克隆文件夹（适配已有本地文件夹场景）
gh repo create GITHUBfiles --public --clone=false

正常输出：`? Created repository 你的用户名/GITHUBfiles on GitHub`，表示远程仓库创建成功。

### 步骤 6：关联本地文件夹与远程仓库

将本地初始化后的仓库，与刚创建的 GitHub 远程仓库绑定，命令如下（替换为实际用户名）：

# 关联远程仓库（origin 是远程仓库别名，通常默认用 origin）
git remote add origin https://github.com/你的用户名/GITHUBfiles.git

# 验证关联是否成功（关键步骤，必须确认）
git remote -v

正常输出示例（地址与远程仓库一致即可）：

origin  https://github.com/timeol21/GITHUBfiles.git (fetch)
origin  https://github.com/timeol21/GITHUBfiles.git (push)

若输出上述内容，说明关联成功；若提示「remote origin already exists」，表示本地已关联过其他仓库，需先删除旧关联（命令：`git remote remove origin`），再重新执行关联命令。

### 步骤 7：提交本地文件并推送到 GitHub

完成文件提交与推送，实现本地文件同步到远程仓库，补充分支重命名步骤，避免分支名不一致问题（新手易踩坑点）：

# 1. 将本地所有文件添加到 Git 暂存区（. 表示所有文件）
git add .

# 2. 提交文件到本地仓库（备注可自定义，新手推荐用 "first commit" 标识首次提交）
git commit -m "first commit"

# 3. 重命名默认分支为 main（解决部分环境默认分支为 master 的兼容问题，关键步骤）
git branch -M main

# 4. 推送本地文件到远程仓库，并绑定本地 main 分支与远程 main 分支
# -u 表示后续推送/拉取可直接用 git push/git pull，无需重复指定分支
git push -u origin main

正常输出示例（含推送进度）：

Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Writing objects: 100% (3/3), 215 bytes | 215.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/timeol21/GITHUBfiles.git
 * [new branch]      main -> main
Branch 'main' set up to track remote branch 'main' from 'origin'.

输出无报错即表示推送成功，本地文件夹与远程仓库已实现双向关联。

### 步骤 8：最终效果验证（可选，教学时可演示）

1. 1. 打开 GitHub 网页，登录账号，在个人仓库列表中找到「GITHUBfiles」仓库；

2. 2. 进入仓库后，可看到本地推送的文件和「first commit」提交记录，说明关联生效；

3. 3. 回到 VSCode，左侧「源代码管理」面板会显示仓库名「GITHUBfiles」，后续可直接通过该面板提交、推送文件。

## 四、常见问题排查（新手教学必讲，规避踩坑）

### 问题 1：执行 gh 命令提示「不是内部或外部命令」

原因：gh 安装后未重启终端，系统环境变量未更新；

解决：关闭 VSCode 终端，重新打开，或直接重启 VSCode。

### 问题 2：推送时提示「remote origin already exists」

原因：本地文件夹已关联过其他远程仓库；

解决：先删除旧关联，再重新关联新仓库：

git remote remove origin
git remote add origin https://github.com/你的用户名/GITHUBfiles.git

### 问题 3：推送时提示「fatal: unable to access 'https://github.com/...'」

原因：网络异常，或 GitHub 账号未登录成功；

解决：重新执行 `gh auth login` 确认登录状态，检查网络连接，或更换网络环境重试。

### 问题 4：分支重命名提示「fatal: not a valid object name: 'main'」

原因：未执行 git commit 提交文件，本地无分支可重命名；

解决：先执行 `git add .` 和 `git commit -m "first commit"`，再执行分支重命名命令。

## 五、核心命令速查（教学时可单独提取）

操作作用

对应命令

安装 GitHub CLI

winget install GitHub.cli

登录 GitHub 账号

gh auth login

验证登录状态

gh auth status

初始化本地仓库

git init

创建远程仓库（公开）

gh repo create 仓库名 --public --clone=false

关联远程仓库

git remote add origin 仓库地址

验证关联状态

git remote -v

添加文件到暂存区

git add .

提交本地文件

git commit -m "提交备注"

重命名分支为 main

git branch -M main

推送文件到远程仓库

git push -u origin main

## 六、教学提示

1. 1. 新手操作时，建议逐行执行命令，每步验证输出结果，避免跳过步骤；

2. 2. 仓库名、用户名需替换为实际信息，避免复制示例命令直接执行；

3. 3. HTTPS 协议无需配置 SSH 密钥，适合新手入门，后续可根据需求切换为 SSH 协议；

4. 4. gh 工具仅需安装一次，后续新建仓库无需重复安装，直接登录即可操作。


