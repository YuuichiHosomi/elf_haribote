OBJS_BOOTPACK = bootpack.obj naskfunc.obj hankaku.obj graphic.obj dsctbl.obj \
		int.obj fifo.obj keyboard.obj mouse.obj memory.obj sheet.obj timer.obj \
		mtask.obj window.obj console.obj file.obj tek.obj elf.obj

TOOLPATH = ../../z_tools/
INCPATH  = ../../z_tools/haribote/

MAKE     = $(TOOLPATH)make -r
NASK     = $(TOOLPATH)nask
CC1      = $(TOOLPATH)gocc1 -I$(INCPATH) -Os -Wall -quiet
GAS2NASK = $(TOOLPATH)gas2nask -a
OBJ2BIM  = $(TOOLPATH)obj2bim
MAKEFONT = $(TOOLPATH)makefont
BIN2OBJ  = $(TOOLPATH)bin2obj
BIM2HRB  = $(TOOLPATH)bim2hrb
RULEFILE = ../haribote.rul
EDIMG    = $(TOOLPATH)edimg
IMGTOL   = $(TOOLPATH)imgtol
GOLIB    = $(TOOLPATH)golib00
HARITOL  = $(TOOLPATH)haritol

CLEAN_FILES := $(wildcard *.lst *.obj *.map *.bim *.hrb)


#TOOLPATH86 = ../../../tolset_p86/z_tools/
#ELFGCCPATH = $(TOOLPATH86)i386-elf-gcc/bin/
ELFGCCPATH = $(HOME)/gcc-6.3.0/i386-elf-gcc/bin/
export PATH:=$(subst /,\,$(TOOLPATH86));$(ELFGCCPATH);$(PATH)

CC       = $(ELFGCCPATH)i386-elf-gcc -I$(INCPATH) -I../ -Os -Wall -fleading-underscore -fno-unwind-tables -fno-asynchronous-unwind-tables
LD       = $(ELFGCCPATH)i386-elf-ld -L$(APILIBPATH) --nmagic


# デフォルト動作

default :
	$(MAKE) ipl11.bin
	$(MAKE) haribote.sys

# ファイル生成規則

ipl11.bin : ipl11.nas Makefile
	$(NASK) ipl11.nas ipl11.bin ipl11.lst

asmhead.bin : asmhead.nas Makefile
	$(NASK) asmhead.nas asmhead.bin asmhead.lst

hankaku.bin : hankaku.txt Makefile
	$(MAKEFONT) hankaku.txt hankaku.bin

hankaku.obj : hankaku.bin Makefile
	$(BIN2OBJ) hankaku.bin hankaku.obj _hankaku

bootpack.bim : $(OBJS_BOOTPACK) Makefile
	$(OBJ2BIM) @$(RULEFILE) out:bootpack.bim stack:3136k map:bootpack.map \
		$(OBJS_BOOTPACK)
# 3MB+64KB=3136KB

bootpack.hrb : bootpack.bim Makefile
	$(BIM2HRB) bootpack.bim bootpack.hrb 0

haribote.sys : asmhead.bin bootpack.hrb Makefile
	$(HARITOL) concat haribote.sys asmhead.bin bootpack.hrb

hello.ko : hello.c Makefile
	$(CC) -c hello.c
	$(HARITOL) copy hello.o hello.ko

# 一般規則

%.gas : %.c bootpack.h Makefile
	$(CC1) -o $*.gas $*.c

%.nas : %.gas Makefile
	$(GAS2NASK) $*.gas $*.nas

%.obj : %.nas Makefile
	$(NASK) $*.nas $*.obj $*.lst

# コマンド

clean :
	-$(HARITOL) remove asmhead.bin
	-$(HARITOL) remove hankaku.bin
	-$(HARITOL) remove $(CLEAN_FILES)

src_only :
	$(MAKE) clean
	-$(HARITOL) remove $(wildcard ipl*.bin)
	-$(HARITOL) remove haribote.sys
