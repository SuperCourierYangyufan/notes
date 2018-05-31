#Git
1. #####git是一个分布式版本控制系统.与svn这种集中式版本控制系统不同
2. #####安装后需要设置个人信息
    ```
    $ git config --global user.name "Your Name"
    $ git config --global user.email "email@example.com"
    ```
3. #####创建仓库
    ```
        $ git init  //创建了一个空仓库
    ```
4. #####添加文件
    ```
        $ git add 文件名   //添加到缓冲区
    ```
5. #####提交仓库
    ```
        $ git commit -m 文件名  //-m 提交的详情信息,添加到当前分支
    ```
6. #####git查看日志
    ```
        $ git log        //提交日志
        $ git reflog     //查看回退日志,便可以根据id再次回退
    ```
7. #####回退
    ```
        $ git reset --hard HEAD~(1,2,3)回退多少个版本
        $ git reset --hard (commit ID) //指定到那个版本
    ```
8. #####查看状态
    ```
        $ git status
    ```
9. #####查看与最新版本区别
    ```
    git diff HEAD -- 文件名
    ```
10. #####文件撤销
    ```
    $ git checkout -- 文件名 //用版本库里的版本替换工作区的版本
    ```
11. #####文件删除
    ```
        $ git rm 文件名   //删除工作区的文件,但是分支中还有
        $ git rm --cached 文件名  //删除缓冲区
        $ git commit -m "rm 文件名" //删除分支中的
    ```
12. #####提交远程仓库
    ```
        $ git remote add origin git@github.com:SuperCourierYangyufan/demo-config.git(ssh地址) //操作远程仓库
        $ git push -u origin master|自定义分支的名字 //第一次提交
        $ git push origin master|自定义分支的名字  //提交
    ```
13. ######克隆
    ```
        $ git clone git@github.com:SuperCourierYangyufan/demo-config.git(ssh地址)
    ```
14. ######分支操作
    ```
        查看分支：git branch
        
        创建分支：git branch <name>
        
        切换分支：git checkout <name>
        
        创建+切换分支：git checkout -b <name>
        
        合并某分支到当前分支：git merge (--no-ff -m) <name>  //(从分支历史上就可以看出分支信息)
        
        删除分支：git branch -d (-D) <name> //(对没有合并的分支强行删除)
    ```
15. #####Bug解决
    ```
        $ git stash
        $ 创建新的分支修改Bug,并提交
        $ 切换需要解决Bug的分支
        $ git stash pop
    ```
16. ######下载最新版本
    ```
        $ git pull origin master(远程) : master(本地)
    ```
17. ######标签
    ```
       $ git tag 版本号 (commit id) //打标签
       $ git tag -d 版本号 //删除
       $ git push origin --tags //推送 
    ```