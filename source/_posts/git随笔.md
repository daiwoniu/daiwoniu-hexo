---
title: git随笔
tags:
  - git
  - 命令
  - 随笔
date: 2016-08-17 18:36:57
updated: 2016-08-17 18:36:57
categories: 开发工具
---
### 2016-08-17 18:36:57
初始化项目
``` bash
mkdir testproject
cd testproject
git init
touch README
git add README
git commit -m 'first commit'
git remote add origin git@git.tdcredit.com:develop/testproject.git   指定本地库与github上的哪个项目相连
git push -u origin master   将本地库提交到github上。
```

设置用户信息：
``` bash
git config --global user.name "defnngj"//给自己起个用户名
git config --global user.email  "defnngj@gmail.com"//填写自己的邮箱
```
修改git远程地址： 在项目根目录下.git目录下的config文件
<!--more-->
基础命令：
``` bash
git init  初始化目录
git clone http://.....   克隆仓库到本地
git branch 查看本地分支
git branch -a 查看本地和远程分支
git checkout 分支（origin/的后面开始写） 切换分支
git pull  获取最新代码
git status 查看变更的文件
git config --global push.default "current"    Git设置当前分支为默认push分支
git config --global http.postBuffer 524288000  修改默认上传上限
# 添加文件
git add . 添加全部修改文件
git add test/  添加指定文件夹下所有修改文件
git add test/test.java test/test2.java  添加指定修改文件
git add -i  查看已添加但没有加入版本库的文件（unchanged代表以修改未添加add）
#删除文件
git rm -r --cached target/
git rm a.java   删除文件
git rm --cached test/test.java 删除添加add的文件
#提交
git commit -m '提交信息' 提交到版本库
git push  将提交到版本库内容上传到服务器 新分支，后面跟分支名
#撤销回滚
git reset --hard HEAD 回滚到add之前的状态
git reset --hard 回复到未修改前
git show 查看最新一次的改动
git remote show origin   查看本地分支和远程分支的对应
git remote prune origin  如果远程分支已经删除，根据远程分支更新本地分支
git merge tmp  合并tmp分支到当前分支
git push origin :tmp   删除远程分支
git branch -m v0.2.0/master develop  重命名本地分支
git push origin develop 本地当前分支推送到远程
git push --delete origin v0.1.0/month_bill  删除远程分支 v0.1.0/month_bill
```
如果本地有变更，获取远端和本地合并
``` bash
git fetch origin
git merge origin/develop
```
如果希望保留生产服务器上所做的改动,仅仅并入新配置项, 处理方法如下:
``` bash
git stash
git pull
git stash pop
然后可以使用git diff -w +文件名 来确认代码自动合并的情况.
反过来,如果希望用代码库中的文件完全覆盖本地工作版本. 方法如下:
git reset --hard
git pull
```

<br />
<br />

###### 常见问题

efrror: RPC failed; result=22, HTTP code = 413  或 he remote end hung up unexpectedly  错误
``` bash
git config http.postBuffer 524288000
git config core.autocrlf true
设置ssh提交。   ssh -T git@github.com   测试连接是否成功
``` bash
ssh-keygen -t rsa -C “joe.le@xxx.com”
```
按3个回车，密码为空。把id_rsa.pub里面的内容添加ssh key到git上面。

错误提示：fatal: remote origin already exists.
``` bash
git remote rm origin
```

error: failed to push som refs to ........
``` bash
git pull origin master //先pull 下来 再push 上去
```

old mode 100755 new mode 100644让git忽略掉文件权限检查：git config --add core.filemode false


今天使用Git在命令行下更新代码遇到了问题，起初觉得是自己安装某软件导致冲突，从网上搜索了一下找到类似问题，成功解决问题。
错误信息如下:
E:\storm-sql>git pull origin joeywen
  0 [main] us 0init_cheap: VirtualAlloc pointer is null, Win32 error 487
AllocationBase 0x0, BaseAddress 0x68570000, RegionSize 0x2F0000, State 0x10000C:\Program Files (x86)\Git\bin\sh.exe: *** Couldn‘t reserve space for cygwin‘s heap, Win32     error 0
原因分析:
Cygwin uses persistent shared memory sections, which can on occasion become corrupted. The symptom of this is that some Cygwin programs beginto fail, but other applications are unaffected. Since these sharedmemory sections are persistent, often a reboot is needed toclear them outbefore the problem can be resolved.
解决办法:
找到Git的安装目录，在命令行下切到bin目录，使用如下命令
rebase.exe-b 0x50000000msys-1.0.dll
然后git重新恢复了正常，在使用该命令确保git此时没有在使用

