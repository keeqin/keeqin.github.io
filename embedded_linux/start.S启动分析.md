1. 在c语言中整个项目的入口就是main函数
2. 在uboot中，因为有汇编的参与，因此不能直接找main。整个程序的入口取决于链接脚本中ENTRY声明的地方(ENTRY(_start)).因此要找_start符号。
3. 编译好u-boot之后，查看u-boot.lds。第三行为ENTRY(_start)
4. _start可以在arch/arm/lib/vectors.S中找到定义
5. _start第一个跳转指令为复位: b reset
6. reset在arch/arm/cpu/arm926rjs/start.S中，最终执行save_boot_params_ret下的指令
7. 指令将cpu设置为SVC32模式，禁止IRQ和FRQ。
8. 接着由于没有define CONFIG_SKIP_LOWLEVEL_INIT,所以执行bl cpu_init_crit
9. cpu_init_crit中disable MMU and D cache
10. 然后跳转到arch/arm/cpu/arm926ejs/sunxi/lowlevel_init.S的lowlevel_init去设置临时栈。

11. 然后跳转到s_init，s_init中好像什么都没做
12. 最后返回到save_boot_params_ret，然后执行bl _main
13. _main入口在arch/arm/cpu/lib/crt0.S中
---
14. **_main**  
(内存定义在include/configs/sunxi-common.h中)
* SP = CONFIG_SPL_STACK (0x8000)
* board_init_f_alloc_reserve() 此函数中将栈指针下移给gd和early memory留出空间,返回调整后的栈指针给SP和r0
* r9 = r0 = SP = (0x8000-0x400-sizeof(gd)16字节对齐) = 0x7b00
* gd 结构体在include/asm-generic/global_data.h中定义
* board_init_f_init_reserve() 此函数中定义gd结构体
指针，指向预留空间后的栈顶,把结构体清零，并且gd->malloc_base指向early memory
* 将0设置为参数执行 board_init_f( ulong )初始化DDR，定时器，和代码拷贝
-----------
    1. r0 = gd-> start_addr_sp // gd 已经在board_init_f()中被复制
    2. r0 8字节对齐
    3. sp = r0 将栈从SRAM移到DRAM
    4. 新 gd 在 bd 下面，使 r9 指向新gd
    5. 给链接寄存器赋值为here标签
    6. 将gd->reloc_off赋值给r0
    7. 将 r0 和 lr 的值相加赋值给 lr
    8. 将gd->relocaddr复制给r0 ，此时r0保存着uboot要拷贝的目的地址
    9. 跳转到 relocate_code ,此标签在arch/arm/lib.relocate.S中。负责将uboot代码拷贝到新的地方去
    10. 跳转到relocate_vectors ,对向量表进行重定位
    11. 进入c_runtime_cpu_setup。
    12. 清除 bss 段
    13. 将gd设置为参数调用board_init_r

---
16. 然后开始初始化board