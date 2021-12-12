1. 在c语言中整个项目的入口就是main函数
2. 在uboot中，因为有汇编的参与，因此不能直接找main。整个程序的入口取决于链接脚本中ENTRY声明的地方(ENTRY(_start)).因此要找_start符号。
3. 编译好u-boot之后，查看u-boot.lds。第三行为ENTRY(_start)
4. _start可以在arch/arm/lib/vectors.S中找到定义
5. _start第一个跳转指令为复位: b reset
6. reset在arch/arm/cpu/arm926rjs/start.S中，最终执行save_boot_params_ret下的指令
7. 指令将cpu设置为SVC32模式，禁止IRQ和FRQ。
8. 接着由于没有define CONFIG_SKIP_LOWLEVEL_INIT,所以执行bl cpu_init_crit
9. cpu_init_crit中disable MMU and D cache
10. 然后跳转到arch/arm/cpu/arm926ejs/sunxi/lowlevel_init.S的lowlevel_init去设置临时栈，和gd。

11. 然后跳转到s_init，
---
* s_init  
* borad_init_f ( ) arch/arm/mach-sunxi/board.c
    * preloader_console_init() "U-BOOT SPL ....."
    * sunxi_board_init()        "DRAM: 32M"
---
12. 最后返回到save_boot_params_ret，然后执行bl _main
13. _main入口在arch/arm/cpu/lib/crt0.S中
---
14. **_main**  
(内存定义在include/configs/sunxi-common.h中)
    1. SP = CONFIG_SPL_STACK (0x8000)
    2. board_init_f_alloc_reserve() 此函数中将栈指针下移给gd和early memory留出空间,返回调整后的栈指针给SP和r0
    3. r9 = r0 = SP = (0x8000-0x400-sizeof(gd)16字节对齐) = 0x7b00
    4. gd 结构体在include/asm-generic/global_data.h中定义
    5. board_init_f_init_reserve() 此函数中定义gd结构体
    指针，指向预留空间后的栈顶,把结构体清零，并且gd->malloc_base指向early memory
    6. 将0设置为参数执行 board_init_f( ulong ) common/board_f.c中，用于配置 dram 的内存分布
    -----------
    1. r0 = gd-> start_addr_sp // gd 已经在board_init_f()中被复制
    2. r0 8字节对齐
    3. sp = r0 将栈从SRAM移到DRAM
    4. 新 gd 在 dram 中，排在在 bd 下面，使 r9 指向新gd
    5. 给链接寄存器赋值为here标签
    6. 将gd->reloc_off赋值给r0
    7. 将 r0 和 lr 的值相加赋值给 lr
    8. 将gd->relocaddr复制给r0 ，此时r0保存着uboot要拷贝的目的地址
    9. 跳转到 relocate_code ,此函数标签在arch/arm/lib/relocate.S中。负责将uboot代码拷贝到新的地方去 和 .rel.dyn 重定向
    10. 跳转到relocate_vectors ,对向量表进行重定位
    11. 进入c_runtime_cpu_setup。这个函数什么也没做
    12. 清除 bss 段
    13. 将gd设置为参数调用board_init_r



---
# board_init_f ( ) 函数分析
该函数中通过函数指针要执行40几个初始化函数：
1. setup_mon_len  
    * gd->mon_len = __bss_end - _start
    * __bss_end - _start 就是整个代码段的长度，他们的值可在 .map 文件中查找到
    * licheepi nano 中: _start 0x81700000 , __bss_end 0x8177ad7c
    * 则 gd->mon_len = 0x7ad7c 大约为491kB
2. fdtdec_setup
    * 设备树设置
2. initf_malloc
    * 初始化 gd 中 malloc有关的变量，比如malloc_limit
3. env_init
    * 在 env/env.c 中
    * 先查询 env 的存储介质
    * 根据存储介质找到存储地址
    * 初始化失败的话就使用默认的存储数组，在 include/env_default.h 中定义为 defalut_environment[]
    * defalut_environment使用的是 include/config_distro_bootcmd.h中的值
    * 还有一些其他地方的值不知道
3. init_baud_rate
    * 从环境变量中读取
    * 把波特率赋值给 gd->baudtate
4. console_init_f
    * 在 common/console.c 中
4. display_optionss
    * 在lib/display_options.c 中
    * 打印出uboot版本和编译时间
5. display_text_info
    * 显示uboot重定位后的段地址
5. print_cpuinfo
    * "CPU : ALLwinner F series (SUNIV)"
6. show_board_info
    * "Mdoel : Lichee Pi Nano"
    * 从设备树文件中读取模块名 arch/arm/dts/XXX.dts
3. announce_dram_init
    * 只是打印出 "DRAM:"
4. dram_init
    * 配置可用的内存块
4. setup_dest_addr
    * 设置 gd 的 ram_size , ram_top , relocaddr .
    * licheepi nano的 ram_size 0x2000000 32M , ram_top relocaddr  0x82000000 。
5. reserve_round_4k
    * gd->relocaddr &= ~(4096 - 1)
    * 对relocaddr做 4k 对齐
6. reserve_mmu 
    * 留出 MMU 的 TLB表的位置
7. reserve_uboot
    * 给uboot在dram上留出空间，并把栈指针指向所留出空间的下面。
8. reserve_malloc
    * 给maclloc 、env 在 dram上流出空间
    * malloc 1M 、 env 8k
    * 大小的宏定义在 inlcude/configs/sunxi-common.h
9. reserve_board
    * 给 bd结构体在 dram上留出空间
    * bd 大小为80 Byte
10. setup_machine
    * 设置machine ID
    * 此ID必须与linux内核的ID匹配才能正常启动内核
    * 但是新版本使用设备树，所以此 ID 功能无效
11. reserve_global_data
    * 给 gd 留出空间，大小为 248B ，没进行字节对齐
    * 此时 gd 在sram上 ，gd->new_gd 在 dram 上
12. reserve_fdt
    * 给设备树留出空间
13. reserve_bootstage
    * 给 bootstage 留出空间
14. reserve_stacks
    * 给gd->start_addr_sp 减 16 后，做16字节对齐
15. dram_init_banksize
    * gd->bd->bi_dram[0].start = CONFIG_SYS_SDRAM_BASE (0x80000000)
    * gd->bd->bi_dram[0].size = get_effective_memsize() (0x2000000)
16. show_dram_config
    * 只是显示一下 dram size “32Mib”
17. setup_reloc
    * 将 sram 的 gd 办到 dram 上， 位置在 gd->new_gd
18. return
-----
# relocate_code
负责将uboot代码从 sram 拷贝到 dram，此函数标签在arch/arm/lib/relocate.S中。
1. ldr r1 , =__image_copy_start  # 把0x81700000赋值给 r1
2. subs r4 , r0 , r1    # r4 = r0 - r1
    * r4 此时保存着新uboot地址和旧uboot地址的偏移
3. beq relcoate_done # 如果r4 = 0 ，说明新旧地址一样，直接执行 relocate_done
4. ldr r2 , __image_copy_end # __image_copy_end - start  = 255kB
5. ldmia r1! , {r10 - r11 } #拷贝[r1]的值到r10 , r11 ,每拷贝一次r1 加一
6. stmia r0! , {r10 - r11 } # 从r10 , r11 拷贝到 [r0],每拷贝一次r0 加一
7. cmp r1 , r2 #比较是否拷贝到了 __image_copy_end
8. 如果没有，则跳转到 5。直到拷贝完成才执行 9 。
9. 重定位 .rel.dyn 段段代码
10. 退出 relocate_code 函数
-------
# relicate_vectors
    重定向异常向量表
---
# board_init_r
参数为 gd 地址 ，和 gd->relocaddr
初始化 board_init_f 中未初始化的外设，和 gd 变量.
执行 init_sequence_r 函数数组中的函数， 大概有29个函数
1. initr_trace 初始化调试跟踪
2. initr_teloc 初始化 gd->flags 标记重定向完成
3. 
