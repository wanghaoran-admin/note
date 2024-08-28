# 第一章 git简介

### 1.git起源、介绍

Git 是一个分布式版本控制系统，最初由Linus Torvalds在2005年创建，用于管理Linux内核的开发。它是目前世界上使用最广泛的版本控制系统之一，被广泛应用于软件开发和其他版本控制需要的领域。





# 第二章 git命令

### 1.基本操作

- `git init`：初始化一个新的 Git 仓库。
- `git clone <repo>`：克隆一个远程仓库。
- `git status`：查看当前仓库的状态。
- `git add <file>`：添加文件到暂存区。
- `git commit -m "message"`：提交暂存区的文件，并添加提交信息。
- `git push`：将本地提交推送到远程仓库。
- `git pull`：从远程仓库拉取并合并代码。
- `git push origin wang:master`：推送到远程仓库origin，wang本地的分支名称，master远程分支名称
- `git push origin --delete feature-branch`:删除远程仓库的分支





### 2.分支操作

- `git branch`：列出所有本地分支。
- `git branch <branch>`：创建一个新分支。
- `git checkout <branch>`：切换到指定分支。
- `git merge <branch>`：合并指定分支到当前分支。
- `git branch -d <branch>`：删除本地分支。





### 3.远程仓库

- `git remote`：查看远程仓库。
- `git remote add <name> <url>`：添加远程仓库。
- `git remote remove <name>`：删除远程仓库。
- `git fetch`：从远程仓库获取数据，但不合并。







### 4.查看日志

- `git log`：查看提交日志。
- `git log --oneline`：以简洁模式查看提交日志。
- `git diff`：查看工作目录与暂存区的差异。
- `git diff <commit>`：查看与某个提交之间的差异。







### 5.撤销操作

- `git reset <file>`：从暂存区移除文件。
- `git reset --hard <commit>`：重置工作目录和暂存区到指定提交。
- `git revert <commit>`：生成一个新提交，撤销指定提交的改动。









### 6.标签

-  `git tag`：列出所有标签。
- `git tag <tag>`：创建一个新标签。
- `git push origin <tag>`：推送标签到远程仓库。







### 7.配置

- `git config --global user.name "name"`：配置全局用户名。
- `git config --global user.email "email"`：配置全局用户邮箱。





# 第三章 流程示意图

### 1.提交文件流程

```
工作区 (Workspace)
  ├── 未跟踪 (Untracked)
  │     └── 新创建一个文件
  ├── 未暂存 (Unstaged)
  │     └── 修改已有文件
  ↓
git add
  ↓
暂存区 (Staging Area / Index)
  ├── 已暂存 (Staged)
  │     └── 提交到仓库之前的缓存区
  ↓
git commit
  ↓
仓库 (Repository)
  ├── 提交记录 (Commit History)
        ├── commit 03
        ├── commit 02
        └── commit 01
```





# 第四章 git忽略文件

```bash
# 忽略所有 .class 文件
*.class

# 忽略所有 .jar 文件
*.jar

# 忽略所有 .log 文件
*.log

# 忽略所有 .properties 文件
*.properties

# 忽略所有 target 目录
/target/

# 忽略所有 build 目录
/build/

# 忽略所有 .gitignore 文件
.gitignore

# 忽略所有 .DS_Store 文件（在 macOS 下）
.DS_Store

# 在 temp 目录下的所有 .txt 文件
temp/*.txt

# 在根目录下的所有 .xml 文件
/*.xml

# 忽略 node_modules 目录下的所有内容
node_modules/

# 递归地忽略所有 .bak 后缀的文件
**/*.bak

```

 **`.gitignore` 文件来忽略特定的文件和目录。**







# 第五章 git使用流程

### 1.从远程仓库上拉取

```bash
git clone https或ssh
```



### 2.和主分支的数据保持一致

【以避免在集成回主分支时产生大量的冲突。】

【这里不需要再次配置仓库！！！！！！】

```bash
git pull 本地仓库名【note】 分支【master】
```



### 3.执行操作

比如我要修改数据：



1. 进行文件修改

2. git add添加到缓存区

3. git commit -m "作用"添加到本地仓库

4. 最后进行git push 本地仓库名【note】 分支【master】