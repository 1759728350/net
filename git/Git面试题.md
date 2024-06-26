##### **什么时候使用“git rebase”代替“git merge”？**

答案：你自己开发分支一直在做，然后你想把主线的修改合到你的分支上，做一次集成，这种情况就用rebase比较好，把你的提交都放在主线修改的头上

1. rebase会把你开发分支的commit全部一条整个放到主分支的最后，所以叫做变基,会增加很多个commit提交。
2. merge会把主分支和你develop分支的commit合并在一起，只形成一个新的commit提交。

##### **拉取请求（pull request）”和“分支（branch）”之间有什么区别？**


- 分支（branch） 是代码的一个独立版本。我pull别人的代码branch创建一个自己的独有版本
    
- 拉取请求（pull request） 是当有人用仓库，建立了自己的分支，我pull了他的代码然后修改,最后合并到他指定的分支上

##### 忽略文件
**我们在本地工程常会修改一些配置文件，这些文件不需要被提交，而我们又不想每次执行 git status 时都让这些文件显示出来，我们该如何操作？**

答案：在 Git 工作区的跟目录下创建一个特殊的.gitignore 文件，然后把忽略的文件名编辑进去，Git 就会自动忽略这些文件。


##### **如何把本地仓库的内容推向一个空的远程仓库？**

```shell
git remote add origin 远程仓库地址 // 将本地和远程厂库关联起来  
git add .  
git commit -m ‘提交信息’  
git push origin master // 将本地代码推送到库上
```


##### 如何解决冲突
```shell
git stash       //将未提交的内容放到暂存区的栈中
git pull        //拉取并合并到本地分支
git stash pop   //从暂存区取出（拉取本更新的分支和暂存区中取出的自己写的代码合并，可能存在冲突，需要手动解决冲突）
//然后add commit
```

##### 提交代码时候写错commit信息后，如何重新设置commit信息？
```shell
git commit --amend -m "修改信息"
```
来对最近一次commit进行修改

##### 在当前主分支上新建一个分支的步骤

1. **切换到主分支**：首先，请确保您处于项目的主分支（通常是 `master` 或 `main` 分支）上。

   ```bash
   git checkout main
   ```

   这个命令会将您切换到主分支，以便从主分支创建新的功能分支。

2. **拉取远程更新**（可选）：如果您的主分支不是最新的，请务必先拉取最新的远程更新到本地主分支。

   ```bash
   git pull origin main
   ```

   这个命令会从远程仓库拉取最新的更改到本地主分支。

3. **创建新的功能分支**：现在，您可以基于当前的主分支创建一个新的功能分支。

   ```bash
   git checkout -b feature-branch   
   ```

   这个命令会同时创建并切换到一个名为 `feature-branch` 的新分支。您可以将 `feature-branch` 替换为您希望使用的任何分支名称。

4. **进行开发工作**：现在您可以在新的功能分支上进行开发工作，添加、修改或删除代码。

5. **添加、提交更改**：一旦您完成了对新功能的开发工作，可以将更改添加到暂存区并提交。

   ```bash
   git add .
   git commit -m "Add new feature"
   ```

   这些命令会将所有更改添加到暂存区并提交到当前的功能分支。

6. **推送功能分支到远程仓库**：在您完成开发并提交更改后，推送功能分支到远程仓库。

   ```bash
   git push origin feature-branch
   ```

   这个命令会将新的功能分支推送到远程仓库，使其他人能够查看您的更改并进行合并操作。


##### **如何查看文件的提交历史和分支的提交历史**

使用git log查看文件提交历史

git log filename

使用git log查看分支提交历史

git log branch file

##### **什么时候应使用 “git stash”？**

git stash 命令把你未提交的修改（已暂存（staged）和未暂存的（unstaged））保存以供后续使用，以后就可以从工作区中进行还原。比如切换分支和不同版本提交


##### **你能解释下 Gitflow 工作流程吗？**

Gitflow 工作流程使用两个并行的、长期运行的分支来记录项目的历史记录，分别是 master 和 develop 分支。

* Feature，每个功能都应留在自己的develop分支中开发
* Develop，是合并所有功能（feature）分支，并执行所有测试的分支。只有当所有内容都经过彻底检查和修复后，才能合并到 release分支。 
* Release,  当develop分支都测试完毕,得到一个稳定的分支
* Master，随时准备发布线上版本的分支，其所有内容都是经过全面测试和核准的（生产就绪）。  
* Hotfix, 分支是用于给快速给生产版本master分支修复打补丁的。

##### 五个分支合作案例
假设我们有一个软件项目正在使用Gitflow工作流进行开发。我们可以通过一个具体的生产例子来说明这五个分支之间的关系。

1. **Develop分支的功能开发**：假设我们要添加一个新功能到我们的软件中，比如添加用户身份验证功能。我们会从Develop分支创建一个新的Feature分支，比如命名为"feature-authentication". 在这个Feature分支上，团队成员可以独立地开发身份验证功能，而不影响其他功能的开发。一旦开发完成并通过了测试，我们将Feature分支合并回Develop分支。

2. **Release分支的版本准备**：当开发团队认为软件已经达到了一个发布新版本的阶段，他们会从Develop分支创建一个新的Release分支，比如命名为"release-v2.0". 在Release分支上，团队可以进行最后的测试、修复bug、更新版本号等操作。一旦准备好发布，Release分支将被合并回Master分支和Develop分支。
   ```shell
     git checkout master # 切换到Master分支 
     git merge release-v2.0 # 将Release分支合并到Master分支 
     git checkout develop # 切换到Develop分支 
     git merge release-v2.0 # 将Release分支合并到Develop分支
     ```
3. **Master分支的版本发布**：当Release分支准备好发布时，我们将它合并回Master分支。这样，Master分支上就包含了最新的稳定版本，可以随时用于发布。发布完成后，我们可以基于Master分支打上一个标签，以便将来可以轻松地找到发布的版本。

4. **Hotfix分支的紧急修复**：假设我们在发布后发现了一个严重的bug需要立即修复。我们会从Master分支创建一个新的Hotfix分支，比如命名为"hotfix-bugfix". 在Hotfix分支上，我们进行bug修复并进行测试。一旦修复完成，我们将Hotfix分支合并回Master分支和Develop分支，以确保后续的开发也包含了这个bug的修复。

```
------develope-----------------------------
               |       单个模块功能 |       |              |                                    |
				-------feature----        |              |合并回开发分支                        |
                                          |              |                       合并回开发分支 |
--------------------------------------release-----测试----+                                   |
														  | 合并到主                           | 
---------------------------------------------------master---------------------------------+  |
																	|              合并到主|  |
                                                                    +----hotfix-----------+--+

```
![](img/Pasted%20image%2020240420132148.png)