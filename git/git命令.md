

##### 区域概念
1. 工作区（Working Directory）：工作区是你实际编辑文件的地方，可以看作是你项目的目录。
    
2. 暂存区（Staging Area）：也叫索引（Index），是一个临时存储更改的地方，你可以将要提交的更改暂时存放在这里。
    
3. 仓库（Repository）：也叫版本库，是Git用来存储项目的地方，包含所有提交的历史记录和元数据。
	1.  本地仓库（Local Repository）：本地仓库是指存储在本地计算机上的Git仓库副本，包含完整的项目历史记录和文件。你可以在本地进行提交、分支操作等Git操作。
    
	2. 远程仓库（Remote Repository）：远程仓库是指位于网络上的Git仓库，通常托管在像GitHub、GitLab、Bitbucket等服务上。你可以将本地仓库的更改推送（push）到远程仓库，或者从远程仓库拉取（pull）最新的更改到本地仓库。



##### git clone
<font color=#99CCFF style=" font-weight:bold;">远程仓库-->修改本地仓库和工作区</font>

>用于克隆一个远程仓库到本地，创建一个本地仓库的副本。Git会复制远程仓库中的所有文件、提交历史记录和分支信息到本地计算机上，使你能够开始在本地进行开发、编辑和提交更改。

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
>将一个本地仓库的一个分支推送到远程仓库的某个分支
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

>主要作用就是切换到不同提交/标签/分支
>切换分支,工作区和暂存区变化
>切换提交,工作区变化
>切换标签,本质和切换提交一样,标签就是对某次重要提交打标签

**1. 切换分支：**

- `git checkout <branch_name>`: 这是`git checkout`最常见的用法，用于切换到指定的分支。在切换分支时，Git会自动将工作目录和索引（暂存区）中的文件更新为目标分支的最新状态。


**2. 恢复文件,切换文件状态到某一提交或标签：**

```
git checkout <commit_SHA> -- <file_path>
```

其中，`<commit_SHA>`是目标提交的SHA值（可以通过`git log`查看），`<file_path>`是要恢复的文件路径。这个命令会将指定文件恢复到指定提交的状态。

如果只想恢复到最近一次提交的状态，可以使用以下命令：

```
git checkout HEAD -- <file_path>
```

切换到某一标签
```shell
git checkout <tag_name>
```


**3. 创建临时分支进行实验性修改：**

- `git checkout -b <experimental_branch>`: 在当前分支的基础上创建一个新分支，并立即切换到新分支进行实验性的修改，而不影响当前主要分支。



#### git tag
<font color=#99CCFF style=" font-weight:bold;">本地仓库和远程仓库中的提交列表会变化</font>

>打标签是在Git中对特定提交进行标识和管理的重要操作，有助于准确地定位项目中重要的里程碑、版本发布等节点。


提交打标签的过程包括以下步骤：

1. **确定要打标签的提交：** 首先，你需要确定要为哪个提交打标签。可以通过`git log`查看提交历史，找到想要打标签的提交哈希值或者使用分支名等来指定提交。
```shell
	git log
```

2. **创建标签：** 一旦确定了要打标签的提交，就可以使用`git tag`命令来创建标签。

     ```bash
     git tag -a <tag_name> -m "Tag message" <commit_hash>    
     ##git tag为某个提交打标签,所以要指定对应的提交
     git tag -a "v1.0" -m "Obtain new learning ideas" 
     ```
3. 推送到远程仓库
```shell
	 git push 远程仓库 tag名字
     git push net v1.0  

	  ##推送所有标签
     git push origin --tags  
```


删除标签
```shell
git tag -d d0539dc7df8fe9fd4ba6e98252e896cc0b88b801
```

#### git rebase
<font color=#99CCFF style=" font-weight:bold;">工作区和本地仓库发生变化</font>
>将当前master分支作为为基底,将today_branch的提交列表嫁接到当前分支上

```shell
git rebase <today_branch>
```

任务:将今天写的分支变基到主分支master上

1. 切换到目标分支（通常是要合并到的分支,主分支）：`git checkout <master_branch>`
2. 运行`git rebase`命令并指定要合并的分支：`git rebase <today_branch>`
3. Git会将将`today_branch`的提交挪动到新的基底上

#### git reset
`git reset`是Git中用于重置和回退当前分支的命令，
它有三种常用模式：`--soft`、`--mixed`和`--hard`，每种模式的作用略有不同。下面是对这三种模式的详细介绍：

<font color=#99CCFF style=" font-weight:bold;">仅改变了暂存区</font>
```shell
git reset     ##清除所有暂存区文件

git reset HEAD <file>
```
撤销对暂存区（Index）中指定文件的更改，将该文件从暂存区中撤出,让这个文件回到未暂存状态。

使用场景: 将之前add的文件撤回来重新add


<font color=#99CCFF style=" font-weight:bold;">改变了本地仓库,本地仓库进行了回退</font>
```shell
git reset --soft <commit>
```
这个命令会将 HEAD 指针移动到指定的 `<commit>`，提交回退, 暂存区和工作目录的文件不改变。你可以重新提交这些更改，相当于取消了之前的提交但保留了暂存区的更改。

使用场景 : 将之前commit的文件重新撤回来,重新commit


<font color=#99CCFF style=" font-weight:bold;">改变了本地仓库和暂存区</font>
```shell
git reset --mixed <commit>
```
这个命令会将 HEAD 指针移动到指定的 `<commit>`，提交回退, 并清空暂存区，但工作目录的文件不变。这样，你可以重新选择要提交的内容，相当于取消了之前的提交并取消了之前的暂存。

使用场景: 从工作目录中重新选择add和重新提交commit



<font color=#99CCFF style=" font-weight:bold;">改变了本地仓库,暂存区和工作目录</font>
```shell
git reset --hard <commit>
```
这个命令会将 HEAD 指针移动到指定的 `<commit>`，并重置暂存区和工作目录，强制使它们与指定提交一致。这意味着你会丢失工作目录中未提交的所有更改，慎用。

使用场景: 工作目录彻底回退, 完全放弃指针移动之前的所有提交 , 清空暂存区。重写,然后重add,重新commit



总的来说，`git reset`命令可以让你回退到指定的提交，同时根据不同的模式选择是否保留暂存区和工作目录中的更改。使用该命令前请确保你理解各种模式的区别，并注意潜在的数据丢失风险。




#### git stash
<font color=#99CCFF style=" font-weight:bold;">只改变工作区</font>
>`git stash`命令用于将当前工作目录中的修改（包括已跟踪和未跟踪的文件）暂存起来，使得工作目录变为干净状态，
以便于切换到其他分支或进行其他操作。
当你在工作时需要突然转移到其他任务，但又不想暂存和提交当前的修改时，可以使用`git stash`命令来保存工作目录。

**为啥不用git add .存到暂存区呢?**
因为你切换分支后工作区目录和暂存区都会跟着变化,所以修改的内容就丢了
当你另外一个分支的任务完成,切回来之前的分支,之前干一半的修改全没了,所以用stash来存修改

意外状况: pop时发现自己在原文件上改了一部分,所以pop出来会被覆盖,那怎么办呢?可以先add,然后pop再提交
如果后来改的那部分不想要可以把暂存区里的给清除掉git reset

具体使用方法如下：

1. **暂存当前修改**：运行`git stash`命令将当前的修改暂存起来，包括已跟踪和未跟踪的文件。
   
   ```bash
   git stash
   ```

2. **查看已暂存的列表**：运行`git stash list`命令可以查看当前已经暂存的列表，每个stash都有一个唯一的标识符（stash@{n}）。

   ```bash
   git stash list
   ```

3. **恢复暂存的修改**：可以使用`git stash apply`命令将最新的stash恢复到当前分支，也可以通过指定stash的标识符来恢复指定的stash。

   - 恢复最新的stash：
     ```
     git stash apply
     ```

   - 恢复指定的stash：
     ```
     git stash apply stash@{n}
     ```

4. **应用并删除stash**：如果希望恢复stash的同时将其从stash列表中移除，可以使用`git stash pop`命令。

   - 应用并删除最新的stash：
     ```
     git stash pop
     ```

   - 应用并删除指定的stash：
     ```
     git stash pop stash@{n}
     ```

5. **删除stash**：如果不需要某个stash了，可以使用`git stash drop`命令将其从stash列表中移除。

   ```
   git stash drop stash@{n}
   ```

6. **清空stash列表**：如果需要一次性清空所有的stash，可以使用`git stash clear`命令。

   ```
   git stash clear
   ```

`git stash`命令是Git中非常有用的功能，可以帮助你轻松地保存和恢复工作目录中的修改，提高工作效率。






##### 待

1. `git status`：查看工作区和暂存区的状态。
1. `git branch`：查看本地分支列表。
4. `git revert <commit_SHA>`：撤销指定提交的更改。
git pull