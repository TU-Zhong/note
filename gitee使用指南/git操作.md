1.  本地下载了git之后就可以直接在命令行里面操作  

2. 如果地址里面有空格，那么就必须要把地址用双引号括起来

3. 如果要进入不同盘符，首先要转换盘符，输入D:等(按实际要求操作)

4. 实际操作
	- **如果没有仓库：**
		git@gitee.com.ta_tu_zhong/aaa.git是目标仓库的ssh地址
		```cmd
		mkdir aaa
		cd aaa
		git init 
		touch README.md
		git add README.md
		git commit -m "first commit"
		git remote add origin git@gitee.com:ta_tu_zhong/aaa.git
		git push -u origin "master"
		```
	- 如果有仓库：
		```cmd
		cd existing_git_repo
		git remote add origin git@gitee.com:ta_tu_zhong/aaa.git
		git push -u origin "master"
		```
5. **如果出现`error: failed to push some refs to ‘git@[gitee](https://so.csdn.net/so/search?q=gitee&spm=1001.2101.3001.7020).com:name/project.git’`这种代码块的样式，**
	项目推送时遇Git推送错误：
	
	`error: failed to push some refs to ‘git@gitee.com:name/project.git’`
	
	**1.  分析：**
	
	这个问题的产生是因为远程仓库与本地仓库并不一致所造成。
	
	**2、解决方案：**
	
	那么我们把远程库同步到本地库就可以了。
	执行命令：
	
	`git pull --rebase origin master`
		
	将远程仓库中的更新合并到本地仓库，–rebase的作用是取消掉本地仓库中刚刚的commit
		然而未果，出现错误：
		
		`error: src refspec master does not match any`
	分析：引起该错误的原因是，目录中没有文件，空目录不能提交。
		
	依次执行：
		
		`git pull origin master`
		`git push origin master`
		
	解决！
		
	**一般而言，正常的推送流程应为：**
	
		1. 在github上创建项目
		
		2. 使用git clone https://github.com/name/project.git克隆到本地
		
		1. 编辑项目
		
		2. git add . （将变更提交至缓存区）
		
		3. git commit -am “提交说明（注释）”
		
		4. git push origin master 将本地变更推送至远程仓库master分支
		
		此时如果在github的remote上已经有了文件，会出现error。那么应当先pull一下，即：
		
		`git pull origin master`
		
		随即push即可。
		`git push origin master` 
6. 这里出现了 `refuse to merge unrelated histories ` 的字样，这是因为在本地新建库后，与远程仓库的内容不一致导致的（远程仓库有一些内容本地没有）。解决办法就是先将本地仓库和远程仓库的内容保持一致。正确的提交过程如下
```cmd
	git init            
	               //初始化仓库
	git add .(文件name)                //添加文件到本地 
	git commit -m “first commit”      //添加文件描述信息
	git remote add origin  远程仓库地址 //链接远程仓库 
	git pull origin master           // 把本地仓库的变化连接到远程仓库master    分支
	//git pull origin master --allow-unrelated-histories 上一行无效时使用
	git push -u origin master        //把本地仓库的文件推送到远程仓库master     分支
```

一个很傻逼的错误，如果你的项目中的文件名和仓库里面的文件名一样的话，一定会发生merge Conflict，这个会一直报错直到你解决这个问题