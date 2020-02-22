.PHONY : all out first second third test

all out: first second third
	@echo "$(MAKE)"  
	@echo "$(MAKECMDGOALS)"
	@echo "$(MAKEFILE_LIST)"

first:
	@echo "first" 
second:
	@echo "second" 
third:
	@echo "third" 
test:
	@$(MAKE) first
	@$(MAKE) second
	@$(MAKE) third

# make结果：
# /Library/Developer/CommandLineTools/usr/bin/make   # make 解释器
# 不打印输出， 因为是make调用, 没有指定具体的命令， 命令行的目标
#  makefile  # 本文件名


# make test 结果
# yandeMacBook-Pro:03预定义变量2 yanwallis$ make test
# first
# second
# third