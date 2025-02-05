1. 如果要进入不同盘符，首先要转换盘符，输入D:等(按实际要求操作)
2. 如果地址里面有空格，那么就必须要把地址用双引号括起来
3. 实际操作
	- 如果没有仓库：
		git@gitee.com.ta_tu_zhong/aaa.git
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
		
	
