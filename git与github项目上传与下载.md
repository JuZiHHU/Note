# git与github项目上传与下载
- [git与github项目上传与下载](#git与github项目上传与下载)
  - [基础配置](#基础配置)
  - [从GitHub上下载项目到本地仓库](#从github上下载项目到本地仓库)
  - [从本地git上传项目到github](#从本地git上传项目到github)
  - [删除GitHub上的文件](#删除github上的文件)
  - [注意事项](#注意事项)
  - [常用git命令](#常用git命令)

## 基础配置
- github账号
- 管理本地仓库的 git
  - windows平台下[git安装](https://zhuanlan.zhihu.com/p/242540359)
  - linux平台下（Ubuntu系统为例）apt-get install git   
    查看git版本 git --version
- 配置ssh:本地Git仓库和Github仓库之间的传输是通过SSH加密的
    1. 找到本机的公钥：  
        windows:位于C:\Users\86159\.ssh下的id_rsa.pub文件  
        linux:/home/panan/.ssh
        如果没有则通过控制命令台生成    
        ssh-keygen -t rsa -C "注册github时用的邮箱" 默认回车即可
    2. 在GitHub上输入id_rsa.pub文件的内容    
       ![ssh](./markdown_test/images/ssh_key.jpg)
       在settings中  点击New SSH key    

- GitHub选项介绍   
    Repository name: 仓库名称（输入名字，最好不要使用中文）
    Description(可选): 仓库描述介绍
    Public, Private : 仓库权限（公开共享，私有或指定合作者）
    Initialize this repository with a README: 添加一个README.md
    
## 从GitHub上下载项目到本地仓库

1.  选择项目下载的文件夹 在git终端中进入该目录   
  ![ssh](./markdown_test/images/02.jpg)
2. git clone （项目链接）https://github.com/JuZiHHU/Note.git  
  ![ssh](./markdown_test/images/03.jpg)    
  ![ssh](./markdown_test/images/04.jpg)  
  然后在该文件夹下就可以看到下载的项目
3. linux平台下在终端直接执行相应命令即可
   

## 从本地git上传项目到github
1. 创建本地项目文件 右键 git bash here进入该目录  
   输入git init创建git仓库
2. 在github上创建仓库
3. 本地仓库与远程仓库关联
4. 推送远程
```c++
git init
git add README.md //git add (将文件添加到本地仓库，git add  * 是添加全部文件)
git commit -m "first commit"  提交到本地缓冲 "" 中为注释
//将本地仓库和远程仓库进行关联
//也可以
git remote add origin https://github.com/hongduhong/test.git
/*
推送到远程 
git push -u origin master
新建的远程仓库是空的，所以要加上-u这个参数，等远程仓库里面有了内容之后，
下次再从本地库上传内容的时候只需下面这样就可以了
*/ 
git push -u origin master
```
## 删除GitHub上的文件

1. 取消暂存文件/目录 使用git rm -r --cached 命令标记文件以进行取消暂存：
```c++
    $ git rm -r --cached backup/

    rm 'backup/backup.sh'

    rm 'backup/rsync_exclude.lst'

    将buckup/替换为你的目录或文件。
```

2. 进行新的提交 将新的提交对象/更改保存到本地Git存储库：
```c++
    $ git commit -m 'Remove newly ignored directory "./backup"'

    master d631061] Remove newly ignored directory "./backup"

    2 files changed, 14 deletions(-)

    delete mode 100644 backup/backup.sh

    delete mode 100644 backup/rsync_exclude.lst
```
3. 将更改推送到远程git存储库 上一个命令将更改保存到本地git存储库，使用以下命令将它们推送到远程存储库：
```c++
    $ git push origin master
```
[参考博客: linux删除远程目录,从远程存储库中删除.gitignore中的git文件/目录](https://blog.csdn.net/weixin_42136791/article/details/116651886)

## 注意事项
1. 在docs命令窗口看到下边的提示：
```c++

$git config --global user.name "Jhon" 

$git config --global user.email Jhon@example.com"

解决方案：

按照上面的提示，输入

>git config --global user.name "这里是你github的用户名" 

>git config --global user.email 这里是你注册github的邮箱
```
## 常用git命令
[git操作](https://www.runoob.com/git/git-basic-operations.html)
[参考博客：Git的使用--如何将本地项目上传到Github](https://www.cnblogs.com/du-hong/p/9921214.html)
