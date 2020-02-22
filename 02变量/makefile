x := foo
y := $(x)b
x += new
.PHONY :test
test:
	@echo "x=>$(x)"
	@echo "y=>$(y)"

# 结果：
# x=>foo new
# y=>foob
