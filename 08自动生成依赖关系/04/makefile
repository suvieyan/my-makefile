.PHONY : all clean

MKDIR := mkdir
RM := rm -rf

SRCS := $(wildcard *.c)
DEPS := $(SRCS:.c=.dep)

# include $(DEPS)
# 一般会产生警告， 使用- 没有警告
-include $(DEPS)

all:
	@echo "this is all"

%.dep : %.c
	@echo "Creating $@ ..."
	@set -e;\
	$(CC) -MM -E $^ | sed 's,\(.*\)\.o[ :]*,objs/\1.o : ,g' >$@

clean :
	$(RM) $(DEPS)


# yandeMacBook-Pro:04 yanwallis$ make
# makefile:9: func.dep: No such file or directory
# makefile:9: main.dep: No such file or directory
# Creating main.dep ...
# Creating func.dep ...
# make: Nothing to be done for `objs/func.o'.
# yandeMacBook-Pro:04 yanwallis$ tree
# .
# ├── func.c
# ├── func.dep
# ├── func.h
# ├── main.c
# ├── main.dep
# └── makefile

# 0 directories, 6 files
# yandeMacBook-Pro:04 yanwallis$ 