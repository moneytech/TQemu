# TQemu
Tiny Qemu run on any hosts (including JS/browser)

# How to build Native version to run on your Linux Host
'''
configure
make
'''
# How to build JS version to run on web browser
'''
emconfigure configure --with-host=js
make
'''

# How to build arm linux
reference config: prebuild/linux-3.18.config
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi-

# Technical Notes
## TCG dead lock
when exec loop which depends on HW interrupt, single thread will cause dead lock. Add max exec ins count to exit dead loop.

## Linux XIP issue
Linux kernel (3.18) has as a MMU bug which does not set Translate Table correct for ro_data and init_data. This bug cause kernel boot OOP.
This table is set in the head.S by below code:
Get base from head.S code (r4) : mcr     p15, 0, r4, c2, c0, 0           @ load page table pointer
But it is cleared by mistake in prepare_page_table() function.

## Optimize Linux boot
The kernel decompressing is too slow. Use XIP to get rid of kernel decompressing and relocating
The initrd decompressing is too slow. Hacking build script not to compress initrd

## JS Limitation
emcc has func pointer limitation, which needs function prototype being matched strictly. 
	g_list cmp function needs to be reworked.
	all op helper functions need to be fixed

emcc has memory alignment limitation, which cannot access unaligned memory.

how to debug tcg:
	enable tcg_dump_ops in tcg_gen_code()
	trace TB exec in cpu_tb_exec()
	trace tci in tcg_qemu_tb_exec()


## JS debug
It can find corresponding C functions in JS code with additional prefix "\_" 

Sometimes, it needs to extend the exceptions levels to find error code. (change Error.stackTraceLimit by JS console)
