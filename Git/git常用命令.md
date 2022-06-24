##### git  init  ----初始化git

##### git clone        ----克隆代码

##### git add remote upstream http:/XXXXXXXXX.git       ---- 添加远程仓库

##### git remote rm  name（远程仓库名）    -----删除远程仓库

##### git remote -v  ---- 显示当前关联的所有远程仓库

##### git pull  远程仓库名  远程分支名      例如  git pull origin master      ----拉取远程分支的代码

##### git push  远程仓库名  远程分支名      例如  git push  origin master     ----向远程分支推送代码

##### git add .  ----暂存修改的代码

##### git status ----检查当前状态

##### git commit -m "massage" ----本地保存代码

##### git log    -----查看提交历史

##### git stash  -----修改代码入栈  不提交不暂存

##### git stash pop  ----入栈代码出栈

##### git stash  drop ----清空栈中代码

##### git branch  ----查看本地所有分支

##### git checkout -b xxx  ----新建xxx本地分支并进入xxx分支

##### git checkout  xxx   ----切换到xxx分支

##### git branch -d xxx   ----删除分支xxx

##### git merge  xxx  ----当前分支内容合并到xxx分支

##### git reset  版本号   -----回退版本    版本号通过 Git  log   那串最长的就是

##### git push 远程仓库名 分支名 --force    -----强制提交   例如 git push -f origin master   强制提交到master

##### 强制拉取：

   1. ###### git fetch --all

   2. ###### git reset  --hard 远程仓库名/分支名   例如 git reset  --hard origin/master

   3. git pull

##### git fetch --all   ---拉取所有全程仓库所有分支

git pull --rebase origin master

该命令的意思是把远程库中的***\*更新合并\****到（**pull=fetch+merge**）本地库中，**–-rebase**的作用是取消掉本地库中刚刚的commit，并把他们**接到**更新后的版本库之中