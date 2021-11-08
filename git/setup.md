# 本机安装git
支持macos、windows、linux
# 搭建初始
## 创建本机标识符
**git config --global user.name "YourName"**
**git config --global user.email "YourEmail"**
# 创建本机的SSH Key
**ssh-keygen -t rsa -C "YourEmail"**  
然后把生成在.ssh/id_rsa.pub中的ssh key添加到github的账户中 

