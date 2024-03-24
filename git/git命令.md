    

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

```bash
git checkout <commit_SHA> -- <file_path>
```

其中，`<commit_SHA>`是目标提交的SHA值（可以通过`git log`查看），`<file_path>`是要恢复的文件路径。这个命令会将指定文件恢复到指定提交的状态。

如果只想恢复到最近一次提交的状态，可以使用以下命令：

```shell
git checkout HEAD -- <file_path>    ##从最新提交恢复文件状态,但刚才修改的内容会被永久删除
##HEAD可以省略
git checkout -- <file>
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

>使用场景 : 将之前commit的文件重新撤回来,重新commit


<font color=#99CCFF style=" font-weight:bold;">改变了本地仓库和暂存区</font>
```shell
git reset --mixed <commit>
```
这个命令会将 HEAD 指针移动到指定的 `<commit>`，提交回退, 并清空暂存区，但工作目录的文件不变。这样，你可以重新选择要提交的内容，相当于取消了之前的提交并取消了之前的暂存。

>使用场景: 从工作目录中重新选择add和重新提交commit



<font color=#99CCFF style=" font-weight:bold;">改变了本地仓库,暂存区和工作目录</font>
```shell
git reset --hard <commit>
```
这个命令会将 HEAD 指针移动到指定的 `<commit>`，并重置暂存区和工作目录，强制使它们与指定提交一致。这意味着你会丢失工作目录中未提交的所有更改，慎用。

>使用场景: 工作目录彻底回退, 完全放弃指针移动之前的所有提交 , 清空暂存区。重写,然后重add,重新commit



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


#### git status
<font color=#99CCFF style=" font-weight:bold;">观测工作区目录和暂存区</font>
`git status` 是 Git 中常用的命令之一，用于查看当前工作目录的状态和已暂存（staged）的变更。
通过运行 `git status` 命令，你可以了解当前工作区、暂存区和本地仓库的文件状态，以及哪些文件被修改过、哪些文件被暂存、哪些文件还未被跟踪等信息。

   `git status` 命令会显示类似以下的输出信息：
   ```shell
   On branch master
   Your branch is up to date with 'origin/master'.

   Changes not staged for commit:
     (use "git add <file>..." to update what will be committed)
     (use "git checkout -- <file>..." to discard changes in working directory)

           modified:   file1.txt
           modified:   file2.txt

   Untracked files:
     (use "git add <file>..." to include in what will be committed)

           newfile.txt
   ```
   - `On branch master`：当前所在分支。
   - `Changes not staged for commit`：未暂存的变更，即修改过但尚未添加到暂存区的文件。
   - `Untracked files`：未跟踪的文件，即未被 Git 管理的新文件。
   - 提供了相应的命令示例，如如何将文件暂存 (`git add`)、如何丢弃工作区中的更改 (`git checkout -- <file>`) 以及如何将未跟踪的文件加入暂存区等。

4. **根据需要执行相应操作**：
   - 如果有修改过的文件未暂存，可以使用 `git add <file>` 命令将其添加到暂存区。
   - 如果想要丢弃对某个文件的修改，可以使用 `git checkout -- <file>` 命令。
   - 如果有新文件需要加入版本控制，可以使用 `git add <file>` 将其加入暂存区。

通过 `git status` 命令，你可以及时了解你的项目中文件的状态，帮助你更好地管理和提交文件变更。


**使用场景**

1. **查看工作目录的状态**：  
    当你在开发过程中需要了解当前工作目录中文件的状态时，可以使用 `git status` 命令。它会显示哪些文件被修改过、哪些文件已经暂存、哪些文件还没有被 Git 跟踪等信息。
    
2. **确认提交前的更改**：  
    在准备提交代码之前，通过运行 `git status` 可以确保你已经暂存了所有需要提交的更改，以及了解未暂存和未跟踪的文件，避免漏掉需要提交的内容。
    
3. **查找冲突**：  
    当你在合并分支或者拉取远程更新后出现冲突时，可以使用 `git status` 查看显示的文件信息，并根据提示来解决冲突。
    
4. **撤销修改**：  
    在 `git status` 的输出中，你可以看到哪些文件被修改过，如果你想要丢弃某个文件的修改，可以使用相应命令来恢复到最近一次提交的状态。



#### git pull
<font color=#99CCFF style=" font-weight:bold;">工作区目录,本地仓库都会变,暂存区会变?</font>
##### 冲突状况
1. **合并分支时的冲突**：当两个分支上对同一文件的相同部分进行了修改，Git 在合并这两个分支时可能无法自动解决冲突，导致冲突产生。
2. **rebase 操作时的冲突**：在使用 `git rebase` 命令将提交移动到另一个基础提交时，如果存在与基础提交冲突的修改，会导致冲突。
3. **多人协作时的冲突**：多人同时对同一文件进行修改并推送到远程仓库时，会导致冲突。这通常发生在团队协作中，需要解决不同开发者之间对同一文件的同时修改。

##### 解决冲突思路
当合并过程中发生冲突时，需要手动解决这些冲突。以下是解决合并冲突的基本步骤：

1. **查看冲突文件**：首先运行 `git status` 命令来查看哪些文件存在冲突。Git 会列出所有有冲突的文件。

2. **编辑冲突文件**：打开包含冲突的文件，在冲突部分周围会有特殊标记，如 `<<<<<<<`、`=======`、`>>>>>>>`。这些标记将冲突的不同部分分隔开来，你需要手动决定保留哪些内容、删除哪些内容或者作其他修改以解决冲突。

3. **解决冲突**：根据冲突情况，编辑文件并移除特殊标记，保留需要的部分，删除不需要的部分，确保最终文件的内容是你希望的结果。

4. **标记为已解决状态**：解决完所有冲突后，使用 `git add <conflicted_file>` 命令将文件标记为已解决状态。

5. **完成合并**：继续执行 `git merge --continue` 或 `git commit` 命令完成合并操作。

6. **提交变更**：如果执行 `git merge --continue`，接着可以直接提交合并的变更；如果手动解决冲突后使用 `git commit`，需要添加提交信息并完成提交。


##### 例子
假设有两个分支：`master` 和 `feature`，并且正在从 `feature` 分支合并到 `master` 分支时发生了冲突。以下是一个简单的示例来演示如何解决合并冲突：

1. 首先，从 `feature` 分支切换到 `master` 分支：
   ```
   git checkout master
   ```

2. 然后进行合并操作，假设在这个过程中发生了冲突：
   ```
   git merge feature
   ```

3. 运行 `git status` 查看存在冲突的文件，假设有一个文件 `example.txt` 发生了冲突。

4. 打开 `example.txt` 文件，可能看起来类似以下内容：
   ```shell
   some content
   <<<<<<< HEAD
   content on master branch
   =======
   content on feature branch
   >>>>>>> feature
   ```

5. 根据需要编辑 `example.txt` 文件，解决冲突。比如保留 `master` 分支的内容或 `feature` 分支的内容，或者结合两者。

6. 保存并关闭文件后，使用以下命令将文件标记为已解决状态：
   ```
   git add example.txt
   ```

7. 然后继续合并操作：
   ```
   git merge --continue
   ```

8. 如果一切顺利，Git 将会完成合并操作。如果还有其他待提交的变更，可以运行 `git commit` 提交合并的结果。



#### git branch

<font color=#99CCFF style=" font-weight:bold;">本地仓库发生改变</font>
创建/删除/查询本地仓库和远程仓库的分支
详见GIT笔记


#### git revert
<font color=#99CCFF style=" font-weight:bold;">改变本地仓库,会将工作区的文件也给删了</font>
>撤销提交并留下一个撤销提交的提交信息
>撤销特定提交而保留提交历史

`git revert` 命令用于撤销指定提交所引入的更改，并创建一个新的提交来应用撤销操作。这个命令不会改变 Git 历史记录，而是通过创建一个新的提交来撤销之前的提交。

下面是 `git revert` 命令的基本用法和步骤：

1. **找到要撤销的提交**：首先使用 `git log` 命令查看提交历史，确定要撤销的提交的哈希值（commit hash）。

2. **执行撤销操作**：运行以下命令撤销指定提交（用 `<commit>` 代表要撤销的提交哈希值）：
   ```
   git revert <commit>
   ```

3. **编辑提交信息**：Git 会自动打开一个文本编辑器以编辑撤销提交的信息。你可以修改默认的提交信息，保存并关闭编辑器。

4. **确认提交**：保存编辑后的提交信息后，Git 将会生成一个新的提交，该提交包含了对指定提交的撤销操作。

5. **解决冲突**：如果在撤销操作中存在冲突，需要手动解决冲突并标记为已解决状态后，再进行提交。

6. **完成操作**：撤销操作完成后，相关文件将恢复到撤销提交之前的状态。

值得注意的是，`git revert` 不会擦除历史记录，而是通过创建新的提交来撤销指定提交的更改，因此适用于已经推送到远程仓库的提交。如果只是想在本地工作区撤销最后一次提交，可以考虑使用 `git reset` 命令。



撤回