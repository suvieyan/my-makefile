.PHONY : test
var1 := 
var2 := $(var1)
var3 = 
var4 = $(var3)

test:
    ifdef var1
		@echo "var1 is defined"
    else
		@echo "var2 is not defined"
    endif

    ifdef var2
		@echo "var1 is defined"
    else
		@echo "var2 is not defined"
    endif

    ifeq ($(var1),$(var2))
		@echo "var1==var2"
    else
		@echo "var1!=var2"
    endif

    ifdef var3
		@echo "var3 is defined"
    else
		@echo "var3 is not defined"
    endif

    ifdef var4
		@echo "var4 is defined"
    else
		@echo "var4 is not defined"
    endif

    ifeq ($(var3),$(var4))
		@echo "var3==var4"
    else
		@echo "var3!=var4"
    endif

# 执行结果
# var2 is not defined
# var2 is not defined
# var1==var2
# var3 is not defined
# var4 is defined
# var3==var4
# 说明： = 是递归赋值， 只能判断var3无值， var4 不能确定， 因此var4 是defined

