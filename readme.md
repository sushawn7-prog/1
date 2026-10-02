# 将本地文件推送到远端 GitHub 仓库的完整流程

本文档介绍如何将本地文件（或本地项目）推送到远端 GitHub 仓库的完整步骤。

---

## 一、前置准备

### 1. 安装 Git

- **Windows**：下载并安装 [Git for Windows](https://git-scm.com/download/win)
- **macOS**：`brew install git`
- **Linux**：`sudo apt install git`（Debian/Ubuntu）或 `sudo yum install git`（CentOS）

安装完成后，验证：

```bash
git --version
```

### 2. 配置 Git 用户信息（首次使用必须）

```bash
git config --global user.name "你的用户名"
git config --global user.email "你的邮箱"
```

### 3. 配置 GitHub 认证方式

推荐使用 **SSH** 或 **Personal Access Token (PAT)**。

#### 方式一：SSH（推荐）

```bash
# 生成 SSH Key（一路回车即可）
ssh-keygen -t ed25519 -C "你的邮箱"

# 查看公钥内容
cat ~/.ssh/id_ed25519.pub
```

将输出的公钥内容复制到 GitHub：`Settings → SSH and GPG keys → New SSH key`。

验证：

```bash
ssh -T git@github.com
```

#### 方式二：HTTPS + Token

在 GitHub 上生成 Token：`Settings → Developer settings → Personal access tokens`，推送时用 Token 作为密码。

---

## 二、在 GitHub 上创建仓库

1. 登录 GitHub，点击右上角 **+ → New repository**
2. 填写仓库名称（Repository name）
3. 选择 Public / Private
4. **不要**勾选 "Initialize this repository with a README"（如果本地已有文件）
5. 点击 **Create repository**
6. 记录下仓库地址，例如：
   - SSH：`git@github.com:username/repo.git`
   - HTTPS：`https://github.com/username/repo.git`

---

## 三、本地推送流程

### 场景 A：本地已有项目，推送到新建的空仓库

```bash
# 1. 进入项目目录
cd /path/to/your/project

# 2. 初始化 git 仓库
git init

# 3. （可选）创建 .gitignore 文件，忽略不需要提交的文件
cat > .gitignore << EOF
node_modules/
*.log
.DS_Store
dist/
.env
EOF

# 4. 添加所有文件到暂存区
git add .

# 5. 提交到本地仓库
git commit -m "first commit"

# 6. 重命名默认分支为 main（GitHub 默认）
git branch -M main

# 7. 关联远程仓库
git remote add origin git@github.com:username/repo.git

# 8. 推送到远端
git push -u origin main
```

> `-u` 参数用于设置上游分支，之后直接用 `git push` 即可。

---

### 场景 B：只推送单个文件到已有仓库

```bash
# 1. 克隆远端仓库到本地
git clone git@github.com:username/repo.git
cd repo

# 2. 将你的文件复制进来
cp /path/to/your/file.txt .

# 3. 添加、提交、推送
git add file.txt
git commit -m "add file.txt"
git push
```

---

### 场景 C：本地已有 Git 仓库，推送到新远端

```bash
cd existing-repo

# 修改远端地址
git remote set-url origin git@github.com:username/new-repo.git
# 或者添加新的远端
git remote add neworigin git@github.com:username/new-repo.git

# 推送
git push -u origin main
```

---

## 四、常见问题与解决方案

### 1. `fatal: remote origin already exists`

说明已存在 origin，需先删除或修改：

```bash
git remote remove origin
# 或
git remote set-url origin 新地址
```

### 2. `failed to push some refs` / `rejected`

远端有本地没有的提交（如 README），先拉取合并：

```bash
git pull --rebase origin main
git push
```

### 3. `Permission denied (publickey)`

SSH Key 未配置或未添加到 GitHub，参考第一步配置 SSH。

### 4. `Support for password authentication was removed`

GitHub 不再支持密码认证，需使用 Token 或 SSH。

### 5. 推送大文件失败

单文件超过 100MB 会被拒绝，需要使用 [Git LFS](https://git-lfs.github.com/)：

```bash
git lfs install
git lfs track "*.psd"
git add .gitattributes
```

---

## 五、常用命令速查

| 命令 | 说明 |
|------|------|
| `git init` | 初始化本地仓库 |
| `git add .` | 添加所有文件到暂存区 |
| `git add <file>` | 添加指定文件 |
| `git commit -m "msg"` | 提交 |
| `git remote add origin <url>` | 关联远端 |
| `git remote -v` | 查看远端地址 |
| `git push -u origin main` | 首次推送并关联上游 |
| `git push` | 后续推送 |
| `git pull` | 拉取远端更新 |
| `git status` | 查看当前状态 |
| `git log --oneline` | 查看提交历史 |

---

## 六、完整流程总结（一句话版）

```bash
git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:username/repo.git
git push -u origin main
```

完成 ✅ 本地文件已成功推送到 GitHub 仓库！