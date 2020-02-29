.PHONY : a b c
# test 依赖a b c
# test:a b c
# 等价于
test:a b
test:b c
test:
	@echo "$^"

# yandeMacBook-Pro:08自动生成依赖关系 yanwallis$ make
# a b c