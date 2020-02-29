.PHONY : test

define func1
	@echo "my name is $(0)"
endef

func2 := @echo "my name is $(0)"

new := $(abspath ./)

var1 := $(call func1)
var2 := $(call func2)
var3 := $(abspath test.cpp)


test :
	@echo "new ==> $(new)"
	$(call func1)
	$(call func2)

	@echo "var1 ==> $(var1)"
	@echo "var2 ==> $(var2)"
	@echo "var3 ==> $(var3)"

# 结果：
# func1 是自定义函数
# fun2 是普通变量
# new ==> /Users/yanwallis/Documents/cpp-projects/my-makefile/06函数
# my name is func1
# my name is

# var1 ==>        @echo my name is func1
# var2 ==> @echo my name is 
# var3 ==> /Users/yanwallis/Documents/cpp-projects/my-makefile/06函数/test.cpp