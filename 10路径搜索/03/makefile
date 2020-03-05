GPATH := src
VPATH := src
CFLAGS := -I inc



app.out: func.o main.o
	$(CC) -o $@ $^
	@echo "Target File ==>$@"

%.o: %.c inc/func.h
	$(CC) $(CFLAGS) -o $@ -c $<

# yandeMacBook-Pro:03 yanwallis$ make
# make: `src/app.out' is up to date.
# make不合理的地方
# 关于路径的处理很不合理， 需要人为