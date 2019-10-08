### 工作常用名称:

##### git:	

```
git status:  显示有变更的文件

git diff： 显示暂存区和工作区的差异
git diff --cached [file]：显示暂存区和上一个commit的差异

git init ：当前目录新建一个Git代码库
git clone [url] ：下载一个项目和它的整个代码历史
git commit -m [message] ： 提交暂存区到仓库区
git commit -a ：提交工作区自上次commit之后的变化，直接到仓库区

git branch ：列出所有本地分支
git branch -r： 列出所有远程分支 
git branch -a: 列出所有本地分支和远程分支
git branch [branch-name]:新建一个分支，但依然停留在当前分支
git checkout -b [branch]:新建一个分支，并切换到该分支
git branch [branch] [commit]:新建一个分支，指向指定commit
git branch --track [branch] [remote-branch]:新建一个分支，与指定的远程分支建立追踪关系
git checkout [branch-name]：切换到指定分支，并更新工作区
git merge [branch]:合并指定分支到当前分支
git branch -d [branch-name]:删除分支

git pull [remote] [branch]：取回远程仓库的变化，并与本地分支合并
git push [remote] [branch]：上传本地指定分支到远程仓库

```

​	

##### linux:	

```
使用curl发送GET请求：curl protocol://address:port/url?args

使用curl发送POST请求：curl -d "args" protocol://address:port/url

检查端口是否被占用： lsof -i:8080 

```

##### oracle:

1. 查询锁表

```
select SESS.sid, SESS.SERIAL#, LO.ORACLE_USERNAME, LO.OS_USER_NAME,
      AO.OBJECT_NAME, LO.LOCKED_MODE
from V$LOCKED_OBJECT LO, DBA_OBJECTs AO, V$SESSION SESS
where AO.OBJECT_ID = LO.OBJECT_ID 
and lo.session_id = sess.sid;
```

2. 释放锁

   ```
   alter system kill session 'sid,serial#'; //sid,serial#是上面查询出来的
   ```
