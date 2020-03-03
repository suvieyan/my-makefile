OBJS := func.o main.o
INC := inc
SRC := src1
SRC2 := src2



VPATH := $(INC) $(SRC)
CFLAGS := -I $(INC)

vpath %.c $(SRC2)



# 在INC 搜索.h 文件
# vpath %.h $(INC)

# 不再在SRC 搜索.c 文件
# vpath %.c

# 取消所有的vpath设置
# vpath




hello.out: $(OBJS)
	$(CC) -o $@ $^
	@echo "Target File ==>$@"

$(OBJS): %.o: %.c
	$(CC) $(CFLAGS) -o $@ -c $<


# yandeMacBook-Pro:02 yanwallis$ ./hello.out 
# void foo(): hello src2
# 优先使用vpath， 没有才VPATH
