## git学习一三记

使用git大概一年多了，一直没有系统的学习下它，到了新公司，让先学习几天就重新系统的学校梳理下。


git是一个分布式的系统，每个人的电脑都可以充当一个服务器，但是通常还是有一个充当服务器的角色的电脑，仅仅用来方便大家交换修改。

     git init

初始化一个目录
             
	git config --global user.name 'your name' 
	git config --global user.email 'email@example.com'

配置邮箱和密码

	git status
 
随时查看当前工作目录的状态

	git add 
添加

	git commit  
提交
 
###版本回退相关：

	git log 
可以查看log信息

	git log --pretty=oneline log
信息每条只显示在一行里

	git reflog 
查看历史信息

	git reset --hard HEAD^ 
回到上一个版本 （^一个表示上一个版本^^两个表示上上个版本 依次类推 太多太多的话如下）

	git reset -hard HEAD~100
回到前100个版本。

	git reset -hard commit-id 
回到commit-id 这个版本

###暂存区和工作区

工作区就是我们看到的自己电脑上的版本区域，.git这个目录是版本库 其中版本库里有很多东西，其中一个即时stage或者叫做index 是暂存区
git帮我们创建的第一个分支就是master分支
git add 是把工作区的东西添加到暂存区 
git commit  是把暂存区里的东西添加到了分支里。


###管理修改
git管理的不是文件时修改
只有在把修改的内容通过git add 添加到暂存区里之后才能被commit到分支上
否则是不会被提交的

###撤销修改
1.工作区的东西改乱了，回到上一个版本
则使用 

	git checkout -- (file)文件名
注意必须要有 -- 否则就是开一个分支的意思了

2.不仅工作区改乱了而且还提交了暂存区
则使用 

	git reset HEAD file(文件名)

3.如果提交到了版本库则回退到上个版本 

	git reset --hard commit-id


###删除文件

	rm file(文件名) 
或者直接手动删除掉，并没有从版本库里删掉，所以可以用

	git rm file(文件名) 
然后再commit之后就可以从版本库里删掉了


远程仓库管理
生成

	ssh key     ssh-keygen -t rsa -C 'youremail@example.com'
然后在github上找到ssh 的地方，把ssh key加进去就可以用ssh连接来进行提交代码了

把本地的版本和github上的关联里起来的命令

	git remote add origin git@server-name:path/repositoryName

####分支管理
git的分支是为了解决多人协作问题

创建分支并切换到分支上

	git checkout -b dev   
创建了dev分支并切换到dev分支上

上一句等于下面两句的作用

	git branch dev 
创建了dev分支

	git checkout dev  
切换到dev分支上

查看有哪些分支

	git branch   
下面会列出有哪些分支，当前分支前面会有个*  星号
master为主分支

当在主分支的时候 

	git merge dev  

就是把dev分支合并到master主分支上

合并之后就需要删掉分支了

	git branch -d dev 
删掉dev分支 



###解决冲突
如果merge的时候报错，手动解决下就可以了。。
所以改动之前记得pull


###分支管理策略
使用强制禁用模式合并，这样不会丢失掉分支的信息

	git merge --no-ff -m 'merge with no-ff' dev

修复bug
1.有bug的时候就把当前工作的分支（比如是dev） 
	git stash 保存起来
2.然后再在该修复bug的分支（比如是feature）上创建一个分支(issue-101)，

	git checkout -b issue-101创建了bug编号为101的修复分支
3.在issue-101 分支上修复完之后commit支护再回到feature分支上merge
   
	git merge --no-ff -m 'merge fix a bug' issue-101
4.再回到dev分支上恢复并删除掉stash

  	git stash apply 恢复分支 
 
	git stash drop 删除stash

  	git stash pop   恢复分支并删除stash

还可以通过 

	git stash list  来查看stash列表

	git stash apply stash@{0}  恢复指定的stash 


在开发新功能的时候最好开一个feature分支来进行开发

如果要丢弃一个没有被合并过的分支，通过 

	git branch -D name 进行强制删除

###查看远程仓库信息

如果要查看远程仓库的信息 
	
	git remote 就可以
如果要查看更详细的信息 

	git remote -v 可以看到抓取和推送的地址   fetch push

本地的dev分支和远程的dev分支之间要有链接！！
设置dev 和origin/dev的链接：

	git branch --set-upstrean dev origin/dev

###打标签
标签是打到commit上了的

	git tag 可以看到打了的标签有哪些
	
	git tag v1.0 给当前所在分支打标签v1.0

可以给历史提交的commit打标签
首先 git log查看信息找到所要找的commit-id
然后再 git tag commit-id 这样就可以给历史的commit打标签了

可以创建带有说明的标签 -a 指明标签名 -m 写说明性文字
git tag -a v1.2 -m 'add a tag v1.2'

git show v0.9可以查看当前这个标签的信息


打的标签在本地,所以可以直接删除标签

	git tag -d v1.0 删除v1.0这个标签

当然没也可以推送到远程

	git push origin v1.0

或者也可以一次性推送到远程

	git push origin --tags

如果标签已经被推送到了远程，也可以删除

	git tag -d v1.0
	git push origin :refs/tags/v1.0 
上面这两步用来删除已经推送到了远程的标签
 
 