.PHONY : all clean rebuild

MKDIR := mkdir
RM := rm -rf
CC := gcc

DIR_DEPS := deps
DIR_EXES := exes
DIR_OBJS := objs

DIRS := $(DIR_DEPS) $(DIR_EXES) $(DIR_OBJS)

EXE := app.out
EXE := $(addprefix $(DIR_EXES)/, $(EXE))

SRCS := $(wildcard *.c)

OBJS := $(SRCS:.c=.o)
OBJS := $(addprefix $(DIR_OBJS)/, $(OBJS))

DEPS := $(SRCS:.c=.dep)
DEPS := $(addprefix $(DIR_DEPS)/, $(DEPS))

all: $(DIR_OBJS) $(DIR_EXES) $(EXE)

# 对输入参数判断
ifeq ("$(MKKECMDGOALS)", "all")
include $(DEPS)
endif

ifeq ("$(MKKECMDGOALS)", "")
include $(DEPS)
endif

$(EXE) : $(OBJS)
	$(CC) -o $@ $^
	@echo "succeddful! Target=>$(EXE)"

$(DIR_OBJS)/%.o : %.c
	$(CC) -o $@ -c $(filter %.c, $^)


$(DIRS) :
	$(MKDIR) $@

ifeq ("$(wildcard $(DIR_DEPS))", "")
# 依赖DIR_DEPS ， 没有先创建DIR_DEPS 文件夹
$(DIR_DEPS)/%.dep : $(DIR_DEPS) %.c
else
$(DIR_DEPS)/%.dep : %.c
endif 
	@echo "Creating $@ ..."
	@set -e;\
	$(CC) -MM -E $(filter %.c, $^) | sed 's,\(.*\)\.o[ :]*,objs/\1.o $@: ,g' >$@

clean :
	$(RM) $(DIRS)

rebuild :
	@$(MAKE) clean
	@$(MAKE) all


# yandeMacBook-Pro:07 yanwallis$ tree
# .
# ├── define.h
# ├── func.c
# ├── func.h
# ├── main.c
# ├── makefile
# ├── new.h
# └── other.h

# 0 directories, 7 files
# yandeMacBook-Pro:07 yanwallis$ make 
# makefile:32: deps/func.dep: No such file or directory
# makefile:32: deps/main.dep: No such file or directory
# mkdir deps
# Creating deps/main.dep ...
# Creating deps/func.dep ...
# mkdir objs
# mkdir exes
# gcc -o objs/func.o -c func.c
# gcc -o objs/main.o -c main.c
# gcc -o exes/app.out objs/func.o objs/main.o
# succeddful! Target=>exes/app.out
# yandeMacBook-Pro:07 yanwallis$ tree
# .
# ├── define.h
# ├── deps
# │   ├── func.dep
# │   └── main.dep
# ├── exes
# │   └── app.out
# ├── func.c
# ├── func.h
# ├── main.c
# ├── makefile
# ├── new.h
# ├── objs
# │   ├── func.o
# │   └── main.o
# └── other.h

# 3 directories, 12 files

