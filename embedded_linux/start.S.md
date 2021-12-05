1. 在c语言中整个项目的入口就是main函数
2. 在uboot中，因为有汇编的参与，因此不能直接找main。整个程序的入口取决于链接脚本中ENTRY声明的地方(ENTRY(_start)).因此要找_start符号。
3. 编译好u-boot之后，查看u-boot.lds。第三行为ENTRY(_start)
4. 入口点_start在arch/arm/lib/vectors.S
5.