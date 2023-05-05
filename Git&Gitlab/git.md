### 创建远程仓库 在github 或gitee 上创建仓库
### 初始化本地仓库 从远程仓库colne 到本地
###    设置用户名和邮箱地址
###    git config --global user.name  名字
###    git config --global user.email 邮箱地址
### 
### 
### git 详细指令
### git init 初始化本地厂库
### git remote add origin 仓库地址  添加远程仓库源（理解为与远程仓库建立联系）
### git status 查看状态 是否加入到暂存区 红色没有 绿色在
### git add . 加入所有文件到暂存区  . 表示所有文件  git add 后面可以跟文件名
### git commit -m '注释内容' 添加到本地仓库的分支中
### git clone  https://github.com/slinjing/devops.git  将库克隆下来到本地


### git push 提交本地仓库到远程仓库中
### git push origin master
 
### git config -l 查看配置
### git diff 查看所有修改了的文件 diff后面跟文件名 表示查看该文件的修改
### 
### 这里的修改指的是提交，版本也指的是提交的版本
### git log 查看修改历史
### git reflog 查看修改历史（简单形式）
### git reset --hard HEAD^ 回到上一个版本 git reset --hard 版本号 回到指定版本


    git中出现“interactive rebase in progress； onto 11dde1e”错误分析与解决方案
    出错原因分析：
    进行提交前，需提前拉取远程仓库的代码，拉取之后，需要重新add、commit，避免仓库的数据被修改，但是再次提交之后会出现上图的错误；

    原因：
    是因为你现在正在编辑的提交将要覆盖在“11ddele commited”，之前使用过git rebase执行过代码的覆盖；

    解决方法：
    git rebase --continue   //使用该命令继续代码的提交
    使用上述代码后，需要重新提交一下，解决代码冲突问题，直至没有rebase提示即正常进行：

    

To https://github.com/slinjing/devops.git
 ! [rejected]        main -> main (fetch first)
error: failed to push some refs to 'https://github.com/slinjing/devops.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.


git pull --rebase origin main
git push --set-upstream origin main
