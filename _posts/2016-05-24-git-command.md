--- 
layout: post 
title: git基础命令
category: Tech 
tags: git 
---

基本的 Git 工作流程如下:  
1. 在工作目录中修改文件。  
2. 暂存文件，将文件的快照放入暂存区域。  
3. 提交更新，找到暂存区域的文件，将快照永久性存储到 Git 仓库目录。


Git 有三种状态，你的文件可能处于其中之一：已提交（committed）、已修改（modified）和已暂存（staged）。
![](https://git-scm.com/book/en/v2/book/01-introduction/images/areas.png)

定制 Git 环境
-

- git config --system 系统级
- git config --global 针对当前用户
- git config 针对该仓库

配置用户信息：

- git config user.name "your username" 这个信息将用于远程仓库
- git config user.email address@host 这个信息将用于登陆远程仓库
- git config core.editor youreditor 文本编辑器
 - window下youreditor 可以为 "'C:/Program Files (x86)/Notepad++/notepad++.exe' -multiInst -notabbar -nosession -noPlugin"
- git config --list 配置完成后检查
- git config <key>检查 Git 的某一项实际生效的配置, 避免重复

求助热线：

- git help verb
- git verb --help
- man git-verb

获取git仓库
- 
- git init 初始化当前目录
- git clone [url] [myrepository] 将远程仓库拷入当前目录下新建的myrepository

![Figure 2-1. 文件的状态变化周期](https://git-scm.com/book/en/v2/book/02-git-basics/images/lifecycle.png)

1. 检查当前文件状态:
 - git status
 - git status -s/short 更为紧凑的格式输出
 - 一个文件同时出现在暂存区和修改区，说明add后没有commit，又对该文件进行了修改。
 - git diff 查看暂存区和修改区的不同
 - git diff --cached/staged 查看已暂存和以前版本的不同
2. All -> Untracked
 - 创建.gitignore文件，忽略指定格式的文件
 - git rm filename 从本地磁盘和暂存区删除文件，且提交后不再跟踪该文件
 - git rm f 强制删除已暂存的文件
 - git rm --cached 从暂存区删除文件，且提交后不再跟踪该文件
 - git rm log/\*.loglog/ 删除log/目录下扩展名为.log 的所有文件
 - git mv file_from file_to = mv + git rm + git add
3. Untracked / Modified -> Staged:
 - git add [file or dir name] *.c
 - 将add命令理解为添加内容到下一次提交中
4. Staged -> Unmodified (Commit)
 - git commit 这种方式会启动文本编辑器以便输入本次提交的说明，默认vim
 - git commit -m "message"
 - git commit -a 自动把所有已经跟踪过的文件暂存起来一并提交(跳过add)

查看提交历史
-
- git log 全部
- git log -p -2 列出最近2次提交差异
- git log --pretty=format:"%h %s" --graph 形象地展示分支、合并历史

撤消操作
-
- git commit --amend 添加文件合并到上一次提交
- git reset HEAD filename 取消暂存
- git checkout -- filename 将文件恢复到上次提交状态
- 使用分支是更安全的做法

远程仓库的使用
-
- git remote -v 显示所有远程仓库
- git remote show origin 显示origin细节
- git remote add <shortname> <url> 添加一个新的远程 Git 仓库
- git remote rename from to 重命名
- git remote rm origin 删除一个远程仓库
- git fetch pb 拉取pb仓库的内容，不会自动合并
- git pull 自动的抓取然后合并远程分支到当前分支
- git push [remote-name] [branch-name] 推送

打标签
-
- git tag 列出已有标签
- 附注标签（annotated）
 - git tag -a v1.4 -m 'my version 1.4' 创建标签
 - git show v1.4 显示标签对应的提交信息
- 轻量标签（lightweight）
 - git tag v1.4-lw
 - git show v1.4-lw
- git tag -a v1.2 9fceb02 为校检和开头为9fceb02的提交打标签
- git push origin [tagname] 显式地推送标签到共享服务器
- git push origin --tags 推送所有标签
- git checkout -b [branchname] [tagname]
 - 并不能真正检出一个标签，需要在制定标签上创建分支

Git 别名
-
- git config --global alias.ci commit ci为commit别名
 - 输入 git commit 时，只需要输入 git ci
- git config --global alias.visual '!gitk'
 - 输入 git visual 时，只需要输入gitk
- 常用
 - git config --global alias.unstage 'reset HEAD --' 暂存文件
 - git config --global alias.last 'log -1 HEAD' 最后一次提交

分支
-
- git branch iss53 新建
- git checkout iss53 切换
- git checkout -b iss53 新建并切换
- git checkout master & git merge hotfix 合并hotfix到master分支
- git branch -d hotfix 删除hotfix分支
- 合并冲突时需修改<<<<<<<=======>>>>>>>间的内容并删除这三行，暂存后进行合并提交。
- git branch 查看分支
- git branch -v 查看每一个分支的最后一次提交
- git branch --merged 查看哪些分支已经合并到当前分支

远程分支
-
- git ls-remote (remote) 来显式地获得远程引用的完整列表
- git remote show (remote) 获得远程分支的更多信息
- git fetch origin 更新本地数据库，移动 origin/master 指针指向新的、更新后的位置
- git push (remote) (branch) 推送本地的 serverfix 分支来更新远程仓库上的 serverfix 分支
- git push origin serverfix:awesomebranch 将本地的 serverfix 分支推送到远程仓库上的 awesomebranch 分支
- git config --global credential.helper cache 简单的凭证存储
- git push origin --delete serverfix 从服务器上删除 serverfix 分支

变基
-
变基总的原则：只对尚未推送或分享给别人的本地修改执行变基操作清理历史，从不对已推送至别处的提交执行变基操作。
待合并等操作熟练后再，有需要时，再熟悉git包括变基等其他高级性能。