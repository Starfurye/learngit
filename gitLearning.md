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

关联远程库，使用`git remote add origin git@server-name:path/repo-name.git`（详情见Github提示）。

关联后，使用命令`git push -u origin master`**第一次**推送`master`分支的所有内容，此后，每次本地提交后，只要有必要，就可以使用命令`git push origin master`推送最新修改，其中，`origin`是默认的库名。