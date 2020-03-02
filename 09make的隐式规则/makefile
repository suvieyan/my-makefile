.PHONY : all

all:
	@echo "command-1"

VAR := test

all:
	@echo "all : $(VAR)"

include 1.mk

# 从上至下， 被覆盖
# yandeMacBook-Pro:09make的隐式规则 yanwallis$ make
# makefile:9: warning: overriding commands for target `all'
# makefile:4: warning: ignoring old commands for target `all'
# all : test