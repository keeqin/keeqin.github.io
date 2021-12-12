# UBOOT目录分析
### 各文件介绍
* .gitignore:包含git add时忽略的文件格式
* comfig.mk:是一个Makefile文件，被其他Makefile调用
* Makefile:uboot代码的主Makefile
* Kconfig:图像化配置文件
* Kbuild:生成配置后的头文件
### 各文件夹介绍
* arch:与架构有关的代码，如arm，x86，mips
* api:硬件无关的应用编程接口
* board:开发板，最开始目录里只有开发板目录，后来开发板太多，把后来新增的开发板放在厂商目录里。
* common:放一些硬件无关，普遍适用的代码
* configs:板子的配置文件。****_defconfig
* disk:磁盘相关，没研究过没用过。
* drivers:驱动。从linux源码抠出来的原封不动的驱动，如网卡驱动，sd卡驱动。
* examples:示例代码
* fs:文件系统相关文件，也是从linux源码中移植过来的的。
* include:头文件目录，uboot和linux一样把所有的头文件都放在一个目录里，而不是跟着自己的c文件。
* lib(lib_xxx):库文件
* net:网络相关文件。如tftp，nfs
* post:上电自检程序
* tools: 工具类文件。如logo
