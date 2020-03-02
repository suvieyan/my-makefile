app.out :main func.o
	$(CC) -lstdc++ -o $@ $^

.c.o :
	@echo "my suffix rule1"
	$(CC) -o $@ -c $^

.c:
	@echo "my suffix rule2"
	$(CC) -o $@ -c $^

# main 匹配了单后缀， func.o 匹配了双后缀
# yandeMacBook-Pro:09make的隐式规则 yanwallis$ make
# my suffix rule2
# cc -o main -c main.c
# my suffix rule1
# cc -o func.o -c func.c
# cc -lstdc++ -o app.out main func.o