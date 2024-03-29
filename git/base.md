# 本机安装git
支持macos、windows、linux
# 搭建初始化
* ### 创建本机标识符
    **git config --global user.name "YourName"**  
    **git config --global user.email "YourEmail"**
* ### 创建本机的SSH Key
    **ssh-keygen -t rsa -C "YourEmail"**  
    然后把生成在.ssh/id_rsa.pub中的ssh key添加到github的账户中  

# 基础操作
* ### 初始化一个文件夹作为仓库
    **git init**
* ### 向暂存区中添加文件,使之成功git管理的对象
    **git add DocName**
## 将暂存区的文件提交到仓库的历史记录中
    **git commit -m "commit information"**
## 修改提交信息
    **git commit --amend**
## 查看提交日志
    **git log**
## 查看工作区状态
    **git status**
## 查看更改前后的差别额
    **git diff**

# 分支操作
* ### 查看当前分支
    **git branch**
* ### 创建新分支feature-A
    **git brach feature-A**
* ### 切换到feature-A分支
    **git checkout feature-A**
* ### 创建并切换到feature-B分支
    **git checkout -b feature-B**
* ### 回溯历史版本
    **git reset --hard _hashcode_**  
    hashcode为目标时间点到哈希值,通过git log/git reflog:查看
* ### 合并操作
    **git merge --no-ff feature-A**

# 推送到远程仓库
* ### 添加远程仓库
    **git remote add git@github.com:keeqin/git_tutorial.git**
* ### 推送到master分支
    **git push origin master**

# 从远程仓库获取
* ### 获取默认的主分支
    **git clone git@github.com:keeqin/git_tutorial.git**
* ### 获取最新的远程仓库分支
    **git pull origin master**