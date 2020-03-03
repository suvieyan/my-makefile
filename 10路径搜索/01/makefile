OBJS := func.o main.o
INC := inc
SRC := src
VPATH := $(INC) $(SRC)
CFLAGS := -I $(INC)


vpath %.c $(SRC)
# 在INC 搜索.h 文件
vpath %.h $(INC)

# 不再在SRC 搜索.c 文件
vpath %.c

# 取消所有的vpath设置
# vpath




hello.out: $(OBJS)
	$(CC) -o $@ $^
	@echo "Target File ==>$@"

$(OBJS): %.o: %.c
	$(CC) $(CFLAGS) -o $@ -c $<

# 默认在当前文件夹
# VPATH: 在指定路径下查找， 特制Makefile的命令查找路径
# $(CC) -o $@ -c $< 会报错， gcc 找不到func.h 头文件， 需要-I $(INC) 指定

