# Git命令

Stage  本地库的暂缓群区

本地库和远程库关联 git remote add name(本地库名字) git@github.com:path(用户名)/xxx.git(仓库名)

git remote add 本地库名字 远程仓库路径

推送本地内容    git push -u origin(本地库名字) master(分支) 第二次提交不用-u

##### 创建版本库

- mkdir xxx      本地创建文件
- cd  xxx          切换目录
- pwd            显示当前目录
- git init           初始化git本地仓库
- git add xxx      添加文件到本地库
- git commit -m ""  提交文件

##### 版本状态
- git status     查看当前git的状态
- git diff       查看修改的地方
- git log--pretty[--oneline](提交信息一行输出) [-p](查看修改详情 增删修改文件数量) [--graph]以图形查看    查看日志
- git reset --hard HEAD^^  回退head表示当前版本
- git reflog        记录每次命令

##### 分支
``` shell
git branch        查看分支  
git branch xxx      创建本地分支
git checkout xx     切换本地分支
git checkout -b xxx   创建+切换分支
git checkout -b 本地分支x origin/远程分支x  拉取远程分支
git merge  xxx     合并某分支到当前分支
git branch -d xxx    删除本地分支
git branch -d -r origin/branchname 删除远程分支
git branch -m|-M oldbranch newbranch 重命名分支 如果new名字已经存在 需要使用-M强制重命名
git pull origin xxx 拉取分支上的代码
git br -m [old_br] [new_br]  分支重命名
```

推送本地分支到远程分支
远程已有remote_branch分支并且已经关联本地分支 且本地已经切换到本地分支 git push
远程已有remote_branch分支但未关联本地分支 且本地已经切换到本地分支 git push -u origin/remote_branch
远程没有remote_branch分支 本地已经切换到本地分支 git push origin localhost_branch:remote_branch


正常情况我们要clone一个github工程是这样的

git**@github**.com:jj/JForm.git

如今在github工程是这样的
git **clone github**:jj/JForm.git
git_ali      阿里
github      github

 

配置本地git仓库用户名密码
git config user.name ""
git config user.email ""
git config --global user.name ""

 

git log <filename> 查看文件的修改历史
git show <commitid> <filename> 查看文件的某次修改
git rm -r --cached <> 删除远程仓库文件
git reset --hard [commit-hashcode] # [commit-hashcode]是某个 commit 的哈希值，可以用 git log 查看



1. 工作区

```shell
git diff  #查看工作区的修改
git checkout -- .  # 撤销工作区的修改
git checkout -- [filename]
git add .    # 提交到暂存区
```

2. 暂存区

```shell
git diff --staged   # 查看暂存区的修改
git reset .         # 撤销暂存区的修改 状态返回到工作区

git reset --hard  # 如果已暂存，但未提交本地仓库之前，想把所有文件直接抛弃(而不是从暂存区删除)
git commit -m ""    # 提交到本地仓库
git rm --cached src/test.pptx  # 删除暂存区的文件
```

3. 本地仓库

```shell
git checkout a18c6fa   # 撤销本地提交,回到指定某次log的状态
git reset --hard HEAD~1  # 重置之前的提交

git revert 711bb0b  # 撤销某次修改

git merge --abort   #撤销合并

```

4. 远程仓库

```shell

```