Contents

[TOC]

## Git基础

### 创建版本库

1. 选择一个合适的目录（最好是空目录），通过`git init`将其变为Git可以管理的仓库：

```bash
$ git init
```

其中`.git`目录是Git用于跟踪管理版本库的，**没事千万不要手动修改这个目录里的文件**。

2. 用`git add`将文件添加到仓库：

```bash
$ git add readme.txt
```

3. 用`git commit -m <message>`将文件提交到仓库：

```bash
$ git commit -m "wrote a readme file"
```

`-m`后面输入本次提交的说明，`commit`一次可以提交很多文件：

```bash
$ git add file1.txt
$ git add file2.txt file3.txt
$ git commit -m "add 3 files."
```

### 时光机穿梭

* 随时掌握工作区状态，使用`git status`命令
* 如果`git status`告诉你文件被修改过，用`git diff`查看修改内容

### 版本回退

* `git log`查看版本历史记录，如果觉得输出信息太多，加上`--pretty=oneline`
* `HEAD`是一个指向当前版本的指针，`HEAD`表示当前版本，`HEAD^`表示上一个版本，`HEAD^^`表示上上个版本...`HEAD-100`表示前100个版本
* `git reset`回退：

```bash
$ git reset --hard HEAD^
```

回退到了上个版本，若要撤销这次更改，返回到原来的版本，需要知道那个版本的`commit id`（的前几位），**最后的办法**是查询Git记录的命令，利用`git reflog`查找那个版本的`commit id`。

### 撤销修改

* 当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- <filename>`。
* 当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <filename>`，再使用`git checkout -- <filename>`。
* 已经提交了不合适的修改到版本库时，想要撤销本次提交，参考上一节，不过前提是没有推送到远程库。

### 删除文件

若使用`rm`在文件管理器中删除了文件，有以下两种情况：
* 确实需要删除这个文件，那么使用`git rm <filename>`在暂存区内把文件删去，然后`git commit`。
* 卧槽，删错了，见上一节，`git checkout -- <filename>`撤销修改。

`git checkout`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失最近一次提交后你修改的内容。

## 添加远程库

关联远程库，使用`git remote add <repositoryname> git@server-name:path/repo-name.git`（详情见Github提示，库名默认为`origin`）。

关联后，使用命令`git push -u origin master`**第一次**推送`master`分支的所有内容，此后，每次本地提交后，只要有必要，就可以使用命令`git push origin master`推送最新修改，其中，`origin`是默认的库名。

### 从远程库克隆

使用`git clone`命令克隆，Git支持多种协议，包括`https`，但通过`ssh`支持的原生`git`协议速度最快。

## 分支管理

`HEAD`严格来说并不是指向提交，而是指向当前分支，`master`才指向提交。不同的人在进行同一个项目的开发时，需要建立分支，工作完成后，就需要把分支合并到`master`上，Git的合并和删除分支实质上就是指针的修改和删除。

### 合并分支

* 使用`git checkout -b <branchname>`创建并切换到分支。它相当于：

```bash
$ git branch <branchname>
$ git checkout <branchname>
```

* 使用`git branch`查看所有分支，当前分支的前面会有一个星号*。
* 使用`git checkout <branchname>`切换分支。
* 使用`git merge <branchname>`将要合并的分支合并到`master`分支上。
* 使用`git branch -d <branchname>`删除分支
* 当Git无法自动合并分支时，就必须首先解决冲突，将冲突的文件**统一**修改为我们希望的内容，然后提交。解决冲突的步骤是：查看冲突文件（`cat <filename>`，Git会给予提示），编辑冲突文件（Git已在冲突文件中写入改动情况），提交，最后删去非`master`的分支。
* 使用`git log --graph`查看合并分支图。使用`git log --graph --pretty=oneline --abbrev-commit`更方便查看。

### 分支策略

合并分支时，加上`--no-ff`就可以用普通模式合并，合并后存在历史分支，而Git默认的`fast forward`会删去历史分支。

```bash
$ git merge --no-ff -m "something" <branchname>
```

在实际开发中，我们应该按照几个基本原则进行分支管理：

首先，`master`分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；干活都在`dev`分支上，也就是说，`dev`分支是不稳定的，到某个时候，比如1.0版本发布时，再把`dev`分支合并到`master`上，在`master`分支发布1.0版本。每个人都在dev分支上干活，每个人都有自己的分支，时不时地往dev分支上合并就可以了。

### Bug分支

手头工作没有完成却要先到别的分支（修复bug时，一般要创建新的`issue-xxx`分支）上工作时，使用`git stash`，再切换到别的分支工作，工作结束后，通过`git stash pop`恢复stash内容并删除原来的stash内容。若不想删去stash内容，使用`git stash apply stash@{number}`，日后再通过`git stash drop`删除。`git stash pop`和`git stash drop`删除的都是最新的一个stash。

通过`git stash list`查看stash 内容。

### feature分支

开发一个新的feature，最好新建一个分支，如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除。

### 多人协作

多人协作的工作模式通常是这样：

1. 首先，可以试图用`git push origin <branchname>`推送自己的修改；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再用`git push origin <branch-name>`推送就能成功！

如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to <branchname> origin/<branchname>`。

* 查看远程库，使用`git remote -v`；
* 本地新建的分支如果不推送到远程，对他人就是不可见的；
* 在本地创建和远程分支对应的分支，使用`git checkout -b branch-name origin/branch-name`，本地和远程分支的名称最好一致；
* 建立本地分支和远程分支的关联，使用`git branch --set-upstream branch-name origin/branch-name`；

### Rebase

`git rebase`可以把本地未push的分叉提交历史管理整理成直线，目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。缺点是本地的分叉提交已经被修改过了。

## 标签管理

发布一个版本时，我们通常先在版本库中打一个标签（tag），这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。

Git的标签虽然是版本库的快照，但其实它就是指向某个commit的指针，标签是一个让人容易记住的名字（如版本号v1.0），而不是像`commit id`一样的字符串。

### 创建标签

* `git tag <tagname>`用于新建一个标签，默认commit为`HEAD`，也可以用`commit id`指定一个commit。
* 命令`git tag -a <tagname> -m "something..."`可以指定标签信息；
* 命令`git tag`可以查看所有标签，`git show <tagname>`查看标签信息。

### 操作标签

* `git push origin <tagname>`推送一个本地标签；
* `git push origin --tags`推送全部未推送过的本地标签；
* `git tag -d <tagname>`删除一个本地标签；
* `git push origin :refs/tags/<tagname>`删除一个远程标签。

## 使用GitHub

在GitHub上，可以点击**Fork**，这样会在自己的账号下克隆一个相同的仓库，然后通过`git clone`命令从**自己的**仓库里克隆项目——只有这样才有权限进行读写，然后就可以开始工作。工作结束后，往自己的仓库推送。如果你参与的是别人的项目，可以在GitHub上发起一个`pull request`。

## 自定义Git

```bash
$ git config --global color.ui true
```

这样，Git会适当地显示不同的颜色。

### .gitignore

忽略某些文件时，需要加入`.gitignore`并提交，不需要自己从头编写，可以到以下网站查找：

> https://www.gitignore.io/
> https://github.com/github/gitignore

检验`.gitignore`是否符合你的需要的标准是`git status`命令下是否显示`working directory clean`。

忽略文件的原则是：
* 忽略操作系统自动生成的文件，比如缩略图等；
* 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的.class文件；
* 忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。

若想添加一个文件到Git，而这个文件却已经被`.gitignore`忽略，可以强制添加：`git add -f <filename>`；另一种情况是，`.gitignore`确实写的有问题，可以用`git check-ignore -v <filename>`检查该文件。

### 配置别名

配置Git的时候，加上`--global`是针对当前用户起作用的，如果不加，那只针对当前的仓库起作用。

每个仓库的Git配置文件都放在`.git/config`文件中：

```bash
$ cat .git/config 
[core]
    repositoryformatversion = 0
    filemode = true
    bare = false
    logallrefupdates = true
    ignorecase = true
    precomposeunicode = true
[remote "origin"]
    url = git@github.com:michaelliao/learngit.git
    fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
    remote = origin
    merge = refs/heads/master
[alias]
    last = log -1
...
```

别名就在[alias]后面，要删除别名，直接把对应的行删掉即可。
而当前用户的Git配置文件放在用户主目录下的一个隐藏文件`.gitconfig`中：

```bash
$ cat .gitconfig
[alias]
    co = checkout
    ci = commit
    br = branch
    st = status
[user]
    name = Your Name
    email = your@email.com
...
```

配置别名也可以直接修改这个文件，如果改错了，可以删掉文件重新通过命令配置。

### 搭建Git服务器

> https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137583770360579bc4b458f044ce7afed3df579123eca000