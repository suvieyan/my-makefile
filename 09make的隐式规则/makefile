SRCS := $(wildcard *.c)
OBJS := $(SRCS:.c=.o)

app.out: $(OBJS)
	$(CC) -o $@ $^
	$(RM) $^
	@echo "Target ==> $@"

# yandeMacBook-Pro:09make的隐式规则 yanwallis$ make
# cc    -c -o func.o func.c
# cc    -c -o main.o main.c
# cc -o app.out func.o main.o
# rm -f func.o main.o
# Target ==> app.out
# yandeMacBook-Pro:09make的隐式规则 yanwallis$ ./app.out 
# void foo(): hello yan
# 生成.o 文件， .o依赖.c , 对应-c -o func.o func.c
# CC 和RM  来自于make标准库
