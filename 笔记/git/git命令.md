
|命令|作用|
|:---:|:---:|
|git init|初始化本地仓库|
|git clone url|从url拉去远程仓库|
|git add <文件名/支持通配符>|将文件file加入缓存|
|git status -s|查看文件状态|
|git checkout <文件名>|放弃文件缓存|
|git commit -m|将所有缓存文件上传,需要填写日志|
|git reset HEAD <文件名>|取消文件缓存的另一种方式|
|git branch|查看当前分支|
|git branch <分支名>|创建分支|
|git checkout <分支名>|切换分支|
|git merge <分支名>|合并分支到当前分支|
|git branch -d <分支名>|删除分支|
|git log|回顾提交历史|
|git reset --hard  [局部索引值]|回到git reflog索引值的修改点|
|gir reset --Head HEAD^|回到上一次提交，^的数量决定回退多少步|
|git reset --hard HEAD~n|回到n次提交|
