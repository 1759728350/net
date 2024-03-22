

## 区域概念
1. 工作区（Working Directory）：工作区是你实际编辑文件的地方，可以看作是你项目的目录。
    
2. 暂存区（Staging Area）：也叫索引（Index），是一个临时存储更改的地方，你可以将要提交的更改暂时存放在这里。
    
3. 仓库（Repository）：也叫版本库，是Git用来存储项目的地方，包含所有提交的历史记录和元数据。
	1.  本地仓库（Local Repository）：本地仓库是指存储在本地计算机上的Git仓库副本，包含完整的项目历史记录和文件。你可以在本地进行提交、分支操作等Git操作。
    
	2. 远程仓库（Remote Repository）：远程仓库是指位于网络上的Git仓库，通常托管在像GitHub、GitLab、Bitbucket等服务上。你可以将本地仓库的更改推送（push）到远程仓库，或者从远程仓库拉取（pull）最新的更改到本地仓库。



##### git clone
<font color=#99CCFF style=" font-weight:bold;">远程仓库-->修改本地仓库和工作区</font>

`git clone`命令用于克隆（Clone）一个远程仓库到本地，创建一个本地仓库的副本。当你使用`git clone`命令时，Git会复制远程仓库中的所有文件、提交历史记录和分支信息到本地计算机上，使你能够开始在本地进行开发、编辑和提交更改。


```bash
git clone -o 本地<repository-url> [<directory>]
```

- `<repository-url>`：远程仓库的URL地址，可以是HTTPS或SSH协议。
- `[<directory>]`：可选参数，指定在本地保存克隆仓库的目录名称，如果不指定，默认为远程仓库名称。

例如，要克隆GitHub上的一个仓库到本地，可以使用以下命令：

```bash
git clone https://github.com/user/repo.git
```

这将克隆`user`用户下的`repo`仓库到当前目录下的名为`repo`的文件夹中。如果你想要指定本地目录名称，可以在命令中添加目录名称，比如：

```bash
git clone https://github.com/user/repo.git my-local-repo
```

这将克隆`user`用户下的`repo`仓库到名为`my-local-repo`的文件夹中。


##### git push
<font color=#99CCFF style=" font-weight:bold;">本地仓库---->远程仓库</font>
```bash
将当前分支的提交推送到远程仓库,没有指定远程分支就是推送给和本地分支同名的远程分支
git push [<remoteRepo>] [<localBranch>]

指定要推送的本地分支
git push <远程仓库名> <本地分支名> :<远程分支名>
```

- `<remoteRepo>`：可选参数，指定要推送到哪个远程仓库，默认为`origin`。
- `<branchBranch>`：可选参数，指定要推送的本地分支，默认为当前所在分支。

例如，如果要将当前分支newbranch的提交推送到远程仓库`origin`的`main`分支，可以执行以下命令：

```bash
git push origin newbranch:main2
```

**记得在使用`git push`命令之前先确保你的本地分支和远程分支之间没有冲突，并且已经与远程仓库同步。**

#### git checkout
<font color=#99CCFF style=" font-weight:bold;">工作区和暂存区发生变化</font>

**1. 切换分支：**

- `git checkout <branch_name>`: 这是`git checkout`最常见的用法，用于切换到指定的分支。在切换分支时，Git会自动将工作目录和索引（暂存区）中的文件更新为目标分支的最新状态。

**2. 创建并切换分支：**

- `git checkout -b <new_branch_name>`: 这个命令结合了创建新分支和切换到该分支的操作。如果指定的新分支名不存在，则会创建一个新的分支并切换到该分支。

**3. 恢复文件：**

- `git checkout -- <file_name>`: 可以使用该命令来撤销对特定文件的修改，将文件恢复到最近一次提交时的状态。
- `git checkout -- .`: 将当前分支下所有文件恢复到最近一次提交时的状态。

**4. 切换到特定提交或标签：**

- `git checkout <commit_hash>`: 允许您检查特定提交时的项目状态，并在那个特定提交上继续工作。
- `git checkout <tag_name>`: 切换到指定标签所对应的代码状态。

**5. 创建临时分支进行实验性修改：**

- `git checkout -b <experimental_branch>`: 在当前分支的基础上创建一个新分支，并立即切换到新分支进行实验性的修改，而不影响当前主要分支。

另外，还有其他一些高级用法，比如通过`git checkout --detach`命令可以在游离状态下查看历史提交，而不会创建分支。`git checkout`也可用于切换到远程分支或创建追踪分支等操作。

总之，`git checkout`是一个非常灵活且功能强大的命令，可用于在Git版本控制系统中管理分支、文件和提交记录。



`git checkout`切换分支时，工作区和暂存区的变化取决于两种情况：

1. **切换到已存在的分支：** 如果你切换到一个已存在的分支，且该分支有与当前所在分支不同的提交，那么工作区和暂存区会根据目标分支的状态进行更新，原来分支上的文件会被覆盖。这意味着你的工作区和暂存区会发生变化。
    
2. **切换到新建的分支：** 如果你切换到一个新建的分支，那么工作区和暂存区将保持不变，因为新建分支的内容与当前所在分支相同。



#### git tag
git tag为某个提交打标签,所以要指定对应的提交

打标签是在Git中对特定提交进行标识和管理的重要操作，有助于准确地定位项目中重要的里程碑、版本发布等节点。

一次完整的提交打标签的过程通常包括以下步骤：

1. **确定要打标签的提交：** 首先，你需要确定要为哪个提交打标签。可以通过`git log`查看提交历史，找到想要打标签的提交哈希值或者使用分支名等来指定提交。

2. **创建标签：** 一旦确定了要打标签的提交，就可以使用`git tag`命令来创建标签。有两种类型的标签，分别是轻量标签和附注标签：
   - 创建轻量标签（Lightweight Tag）：
     ```bash
     git tag <tag_name> <commit_hash>
     ```
   - 创建附注标签（Annotated Tag）：
     ```bash
     git tag -a <tag_name> -m "Tag message" <commit_hash>
     ```

3. **推送标签到远程仓库（可选）：** 如果需要将本地创建的标签推送到远程仓库，可以使用`git push`命令：
   - 推送单个标签到远程仓库：
     ```bash
     git push origin <tag_name>
     ```
   - 一次性推送所有标签到远程仓库：
     ```bash
     git push --tags
     ```

4. **验证标签：** 在完成标签创建和推送后，你可以通过`git tag`命令或查看远程仓库来验证标签是否成功创建和推送。

5. **查看标签信息：** 可以使用`git show <tag_name>`命令来查看特定标签的详细信息，包括提交信息、作者、日期等。

