CC := gcc
MKDIR := mkdir
RM := rm -rf

DIR_OBJS := objs
DIR_TARGET := target

DIRS := $(DIR_OBJS) $(DIR_TARGET)

TARGET := $(DIR_TARGET)/hello_makefile.out
# 得到源文件的文件列表， func.c main.c
SRCS := $(wildcard *.c)
# 值替换， func.o， main.o
OBJS := $(SRCS:.c=.o)

# objs/func.o objs/main.o
OBJS := $(addprefix $(DIR_OBJS)/, $(OBJS))

.PHONY : rebuild clean all

$(TARGET) : $(DIRS) $(OBJS)
	$(CC) -o $@ $(OBJS)
	@echo "Target File ==> $@"

$(DIRS) :
	$(MKDIR) $@

$(DIR_OBJS)/%.o : %.c
    ifeq ($(DEBUG), true)
		$(CC) -o $@ -g -c $^
    else
	    $(CC) -o $@ -c $^
    endif

rebuild : clean all

all : $(TARGET)

clean :
	@echo "clean"
	$(RM) $(DIRS)

# 执行结果
# gcc -o objs/const.o -c const.c
# gcc -o objs/func.o -c func.c
# gcc -o objs/main.o -c main.c
# gcc -o target/hello_makefile.out objs/const.o objs/func.o objs/main.o
# Target File ==> target/hello_makefile.out

# yandeMacBook-Pro:07变量和函数实战 yanwallis$ ls
# Makefile        const.c         func.c          main.c          objs            target
# yandeMacBook-Pro:07变量和函数实战 yanwallis$ cd target/
# yandeMacBook-Pro:target yanwallis$ ls
# hello_makefile.out
# yandeMacBook-Pro:target yanwallis$ ./hello_makefile.out 
# void foo(): hello makefile
