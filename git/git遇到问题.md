##### 为啥每次提交都需要输密码?
因为你git remote add 的是https的协议url
只要改成ssh的url就不需要验证了
![](img/Pasted%20image%2020221126190211.png)
```shell
//先删除之前关联的远程仓库名,以防之后再add时name重复
git remote rm regression 
//重新关联
git remote add regression git@github.com:1759728350/breathe.git
//之后正常提交就行了
```
##### 报错解决

![image-20220218172506512](Git.assets/image-20220218172506512.png)

```shell
$ git push note master
To github.com:1759728350/note.git
 ! [rejected]        master -> master (non-fast-forward)
error: failed to push some refs to 'github.com:1759728350/note.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.

dougax@lyh MINGW64 /d/elecbook (master)
$ git pull --rebase note master
From github.com:1759728350/note

 * branch            master     -> FETCH_HEAD
   Successfully rebased and updated refs/heads/master.
```

##### add.不能乱用
add是将文件加入到缓存区且让git能追踪到
add . 将所有文件到加入了,有的文件你还没修改后就不要急着推上去



## 待解决
当需要用到这些命令时再去学吧
git 查看暂存区的使用场景挖坑
git diff 命令
git rebace
查看及删除git远程库不需要的文件
思考
![](img/Pasted%20image%2020221201185509.png)



参考
[学习笔记](https://gitee.com/hongjilin/hongs-study-notes/tree/master/%E7%BC%96%E7%A8%8B_%E5%89%8D%E7%AB%AF%E5%BC%80%E5%8F%91%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/Git%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0#1git-stash)
[车子的git十篇命令解析](https://blog.csdn.net/longintchar/category_7883282.html)
