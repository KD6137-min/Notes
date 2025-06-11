# 一、 概述

## 版本控制方式：

- 集中式版本控制工具: 版本库存放在中央服务器, 每个人从中央服务器中下载代码, 必须联网, 个人修改后提交到中央版本库, 如SVN和CVS
- 分布式版本控制工具: 无中央服务器, 每台电脑都有完整的版本库, 无需联网, 多人各自将个人修改推送给对方, 如Git

## Git工作流程图

![Git工作流程图](../assets/1.png)

![2](../assets/2.png)

## 实现原理

- 提交: 每一个提交都代表了项目在某个时刻的完整快照, 都有一个唯一的 SHA-1 哈希值, Git利用这个哈希值来标识和引用提交
- 分支: 在 Git 中是一个指向特定提交的可移动指针, 创建一个新分支实际上是在创建新指针, 它指向当前的提交, 在这个分支上进行新的提交, 这个指针会向前移动到最新的提交
- HEAD指针: `HEAD`指向当前所处的分支的最新提交, 它可以用来表示当前工作状态



------



# 二、基本配置

## 查看用户信息

```bash
git config --global user.name
git config --global usre.email
```

## 设置用户信息

```bash
git config --global user.name "NewName"
git config --global usre.email "newEmail@server.com"
```

## 为常用指令及参数配置别名

vim ~/.zshrc 添加如下:

```bash
#用于输出git提交日志
alias git-log='git log --pretty=oneline --all --graph --abbrev-commit'
#用于输出当前目录所有文件及基本信息
alias ll='ls -al'
```

source ~/.zshrc

## 解决输入汉字乱码问题

检查当前配置

```bash
git config --global --get i18n.commitEncoding
git config --global --get i18n.logOutputEncoding
```

若无输出, 则可设置为UTF-8

```bash
git config --global i18n.commitEncoding utf-8
git config --global i18n.logOutputEncoding utf-8
```

## git命令输出显示在终端

```bash
git config --global core.pager cat
```

## 忽略列表

无需纳入Git管理且不希望出现在未跟踪文件列表的文件

工作目录中创建.gitignore文件(名称固定), 列出要忽略的文件模式

示例:

```txt
# no .a files
*.a
# but do track lib.a 
!lib.a
# only ignore the TODO file in the current directory, not subdir/TODO
/TODO
# ignore all files in the build/ directory
build/
# ignore doc/notes.txt, but not doc/server/arch.txt
doc/*.txt
# ignore all pdf files in the doc/ directory
doc/**/*.pdf
```

[专门生成.gitignore文件的网站](http://gitignore.io)



------



# 三、基础命令

## 获取本地仓库

在任意位置创建空目录, 作为本地Git仓库, cd到该目录, 执行:

```bash
git init
```

完成后生成一个.git文件夹

## 基本操作命令

```bash
git add			 // 工作区-->暂存区
git add 单个文件名｜通配符
git add . 		// 将所有修改加入暂存区

git commit		 // 暂存区-->本地仓库(保存修改的各个历史版本)
git commit -m "注释内容"

git status 		 // 查看修改的状态
git revert 	 	 // 撤回提交信息
```

## 查看日志

git log 用于查看版本控制历史记录, 了解项目变更, 查找特定提交, 图形化显示和定制输出格式(如 --pretty、--oneline、--stat 自定义日志的输出格式)

```bash
git log [option]
```

- options:
  - --all : 显示所有分支
  - --pretty=oneline : 将提交信息显示为一行
  - --oneline : 简洁显示每次提交的摘要
  - --abbrev-commit : 使输出的commitID更简短
  - --graph : 图形化表示
  - -n 5 : 限制显示最近的提交数量

HEAD -> : head指向谁, 谁就是当前的分支

单独页面中, 按q退出日志

## 版本回退

```bash
git reset --hard commitID 	// commitID可用git log查看
git reset commitID --hard 	// 同上

git reflog 		// 查看已经删除的提交记录

git restore 	// 用暂存区恢复工作区
```

git reset有三种模式:

- --soft: 更新分支(本地仓库)
- --mixed: 默认, 更新分支(本地仓库), 重置暂存区
- --hard: 更新分支(本地仓库), 重置暂存区, 重置工作区

## 移除文件

```bash
git rm --cached <file> 	// 从暂存区中移除文件, 但保留工作目录中的文件
```

## 标签

标签与特定提交绑定, 需先提交, 再打标签

两类:

- 轻量标签: 只是对某提交的引用, 无附加信息, 类似书签

  ```bash
  git commit -m "add ..."
  git tag <tag_name>  	// 创建一个轻量标签并指向当前提交
  ```

- 附注标签: 对某提交的完整对象, 包括标签名、时间、标签者、邮件地址、标签说明

  ```bash
  git commit -m "add ..."
  git tag -a <tag_name> -m "Tag message"
  ```

常见tag命令:

```bash
git tag // 列出所有标签
git tag v1.0	// 创建轻量标签
git tag -a v1.0 -m "Release version 1.0" 	// 创建附注标签
git show <tag_name> 	// 查看指定标签的详细信息
git tag -d <tag_name> 	// 删除本地标签
git push origin <tag_name> // 推送单个标签
git push origin --tags 		// 推送所有标签
git checkout <tag_name> 	// 检查特定标签对应的提交
```



------



# 四、分支

把工作从开发主线上分离, 来进行Bug修改、开发新功能, 不影响开发主线

```bash
git branch				// 查看分支
git branch -a 			// 查看所有分支
git branch 分支名		// 创建分支
git branch 新分支名 master	// 基于master创建新分支

git branch -d 分支名 	// 删除分支, 删前做各种检查
git branch -D 分支名		// 强制删除, 不做任何检查

git checkout 分支名 		// 切换分支
git checkout -b 分支名 	// 创建并切换分支

git switch 分支名 		// 切换分支
```

## 合并

```bash
git merge 分支名			// 合并分支, 将分支中所有提交合并到当前分支中
```

merge自动合并不同行, 若同时修改了同一文件的同一行, 则需要手动解决冲突, 然后再用git add和commit

快进模式: `Fast Forward`, 一种合并策略, 当前分支(如feature分支)在main分支基础上进行了线性的开发, 且合并时main没有新的提交时, main分支将直接指向feature分支的最新提交, 而不需要创建一个新的合并提交

强制创建一个合并提交, 不使用快进模式:

```bash
git merge --no-ff feature 		// --no-ff, 保留了分支的历史结构
```

## 变基

合并树可能变得混乱复杂, 可使用变基

![3](../assets/3.png)

```bash
git rebase master 	// 系统首先计算dev和master的差集, 将其应用到dev分支
git switch master 
git merge dev		// 切换回master并合并, 得到干净的分支树
```

rebase之后, develop的结点自动被清除

## diff

用于显示工作目录和版本库之间、不同分支之间、或不同提交之间的差异

1. 查看工作目录与暂存区的差异:

   ```bash
   git diff 	// 显示自上次使用git add以来工作目录中未暂存的更改
   ```

2. 查看暂存区与上次提交之间的差异:

   ```bash
   git diff --cached 	// 显示已暂存但尚未提交的更改
   ```

3. 查看工作目录与最后一次提交之间的差异:

   ```bash
   git diff HEAD // 显示工作目录中所有更改(已暂存和未暂存的)与当前分支最新提交HEAD之间的差异
   ```

4. 查看两个提交之间的差异:

   ```bash
   git diff <commit1> <commit2>
   git diff HEAD~1 HEAD	// 显示上一个提交与当前提交之间的差异
   ```

5. 查看两个分支之间的差异:

   ```bash
   git diff <branch1> <branch2>
   ```

输出:  +: 新增的行 -: 删除的行

## cherry

用于比较两个分支或提交, 找出哪些提交在一个分支中存在但在另一个分支中不存在

重点在于比较提交的存在性, 而不是具体的文件差异(与git diff区别)

```bash
git cherry <upstream> <branch> 		 // 列出在branch中存在, 不在upstream中的提交
```

upstream为要比较的基准分支, branch为要检查的分支

### cherry-pick

用途:

- 从一个分支复制特定的改动到另一个分支, 而无需合并整个分支的历史
- 修复紧急问题, 从另一分支挑出某修复提交, 快速应用到当前分支
- 从废弃分支拯救有用的提交

```bash
git cherry-pick <commit-hash> 	// 挑选一个提交
git cherry-pick <commit1-hash> <commit2-hash> <commit3-hash> // 挑选多个, 枚举
git cherry-pick <commit1-hash>..<commit2-hash> 		// 挑选多个, 范围: 从1到2

// 若有冲突, 解决冲突后, 运行: 
git cherry-pick --continue

// 放弃当前的挑选
git cherry-pick --abort
```

会创建一个新提交, 为当前分支的最新提交

不会自动去重, 需避免重复挑选

不建议用于大量提交



------



# 五、remote

github、gitlab、码云

## 配置SSH公钥

SSH公钥理解为设备识别码，让设备有权限更改remote仓库

```bash
ssh-keygen -t rsa 		// 使用rsa算法
```

不断回车即可, 若已存在公钥, 则覆盖

获取公钥:

```bash
cat ~/.ssh/id_rsa.pub
```

复制公钥内容到Gitee网站, 添加成功后:

```bash
ssh -T git@gitee.com
```

输入yes, 连接成功

## 建立关联

需要现在码云上创建好仓库

```bash
git remote add origin <仓库路径>(码云上的ssh/http)
git branch -vv 		// 显示分支, 及其与远端的关联
```

origin只是为方便操作而起的一个别名, 一般默认origin
 remote add只是建立起本地到远程的关联, 本地就可使用origin操作远程仓库

## 基础操作

```bash
git remote 		 			// 查看远程仓库
git remote show 			// 同上
git remote remove <名称> 	// 删除链接
git remote rename <旧> <新> 	// 重命名
```

## push

```bash
git push [-f] [--set-upstream] [远端名[本地分支名][:远端分支名]]

git push --delete <branch> 		// 删除远端分支
```

- -f: 强制覆盖
- --set-upstream: 简写-u,推送到远端, 同时建立与远端分支的关联关系, 让本地分支"跟踪"远端分支, 若当前分支已经和远端分支关联, 则可省略, 只有git push即可

打标签push:

```bash
git tag v0.1 master
git push --tags
```

## clone

```bash
git clone <仓库路径> [本地目录] 	// 本地目录可省, 自动生成一个新目录
```

- --depth=1: 克隆深度, 只克隆最新的版本即可

## fetch

```bash
git fetch [remoteName] [branchName]
```

将仓库里的更新都抓取到本地, 但不合并

若不指定远端名和分支名, 则抓取所有分支

## pull

```bash
git pull [remoteName] [branchName]
```

将远端仓库的修改拉到本地并自动合并, 等同fetch+merge(自动merge会污染分支)以当前分支为基础merge

若不指定远端名和分支名, 则抓取所有并更新当前分支

更推荐使用:

```bash
git pull --rebase [remote] [bbranch]
```

本地提交被搁置, 先将远程更新应用到本地, 在提交本地更新, 不会产生合并提交

```bash
// 配置默认的git pull策略为rebase
git config --global pull.rebsase true
```



------



# 六、Git标准流程/分支管理策略

## github-flow/PR流程

1. 克隆代码或者更新代码到本地
    ​`git clone --depth=1 <路径>​`或者`git pull​`

2. 基于master分支创建并切换到自己的分支
    `​git branch -a​  git branch mybranch master​`

   `git checkout mybranch`或者`git switch mybranch`

3. 在自己的分支上做开发并实施版本控制: `git add`/ `git commit`

4. 把自己的工作成果同步到服务器上: `git push -u origin mybranch`

5. 在线发起合并请求, 将自己的工作成果合并到master分支, 如果Pull Request(Merge Request)(即PR流程)被接受那么工作成果就会出现在master分支上

   若合并代码时出现冲突无法自动合并, 应该先通过`git pull`将冲突代码拉到本地, 使用`git diff`查看哪些地方发生了冲突, 解决冲突一定不能凭主观臆断, 而是要当面沟通, 手动解决冲突以后重新提交代码, 将自己的分支push到服务器, 再次发起合并请求

## git-flow

两个长线分支: master、develop

三个短线分支: feature、release、hotfix

![4](../assets/4.png)

### 1. Production/master

生产分支/主分支, 最稳定的正式版本, 只能从其他分支合并, 不能直接修改, 每次更新都要打上版本号

> master分支只存放历史release版本的源代码, 即稳定的发布版本

### 2. develop

开发分支/集成分支, 从master创建, 包含所有要发布到下一个release的新功能, 主要合并其他分支内容, 到达一个稳定点准备好发布时, 从该点拉取一个预发分支并附上版本号

> develop分支用来整合feature分支, 存放最新开发版本的源代码

### 3. release/xxxx

预发分支/发布分支, 理解为"待发布"分支, 从develop创建, 专为测试、发布新版本开辟, 发布后合并到develop和Production分支中, 需要提交到服务器, 由测试工程师测试, 一个团队在做release分支时, 另一个团队可以接着开发下一版本

> release分支只做和发布有关的事, 如测试、修复bug、编写发布文档, 不会添加新特性

### 4. feature/xxxx

新功能分支, 从develop创建, 开发新功能, 同期并行开发但不同期上线, 开发完毕后合并至develop分支, 或效果不好直接丢弃

> feature分支只存在于开发者本地, 永远不会和master有接触

### 5. hotfix/xxxx

修复分支, 只能基于master创建, 从指定tag版本拉取hotfix分支进行紧急bug修复, 并附上版本号, 修复并测试后合并到develop和production分支, 不会打断开发分支, 使开发和修复并行、独立工作

### 其他分支

如test测试分支、pre预上线分支

```bash
// 应用
brew install git-flow 		// 安装git-flow
git flow init 		// 初始化

git flow feature start MyFeature 	// 从develop开启一个新分支
git flow feature publish MyFeature 	// push到远程
git flow feature pull origin MyFeature 
git flow feature finish MyFeature 	// 合并到develop, 删除feature, 切换回develop

git flow release start Release [Base]	// 创建一个release分支
git flow release publish Release
git flow release finish Release // 合并到master、develop, 打tag, 删除release
git push --tags 	// push到服务器

git flow hotfix start VERSION [BASENAME] 	// 开启一个hotfix分支
git flow hotfix finish VERSION 		// 结束一个hotfix分支, 合并到develop、master
```

APP推荐: Sourcetree