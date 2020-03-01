.PHONY : all clean

MKDIR := mkdir
RM := rm -rf
CC := gcc

DIR_DEPS := deps

SRCS := $(wildcard *.c)
DEPS := $(SRCS:.c=.dep)
DEPS := $(addprefix $(DIR_DEPS)/, $(DEPS))

all:
	@echo "this is all"

# 对输入参数判断
ifeq ("$(MKKECMDGOALS)", "all")
include $(DEPS)
endif

ifeq ("$(MKKECMDGOALS)", "")
include $(DEPS)
endif



$(DIR_DEPS) :
	$(MKDIR) $@

ifeq ("$(wildcard $(DIR_DEPS))", "")
# 依赖DIR_DEPS ， 没有先创建DIR_DEPS 文件夹
$(DIR_DEPS)/%.dep : $(DIR_DEPS) %.c
else
$(DIR_DEPS)/%.dep : %.c
endif 
	@echo "Creating $@ ..."
	@set -e;\
	$(CC) -MM -E $(filter %.c, $^) | sed 's,\(.*\)\.o[ :]*,objs/\1.o : ,g' >$@

clean :
	$(RM) $(DIR_DEPS)

# 结果：
# 先创建.dep 文件， 后生成deps 文件夹
# yandeMacBook-Pro:05 yanwallis$ make all
# makefile:13: func.dep: No such file or directory
# makefile:13: main.dep: No such file or directory
# Creating main.dep ...
# Creating func.dep ...
# mkdir deps
# this is all
