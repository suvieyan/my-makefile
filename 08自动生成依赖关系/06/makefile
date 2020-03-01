.PHONY : all

include test.txt

all:
	@echo "$@ : $^"

# b.txt 时间戳比test.txt时间戳新， 会继续向下执行
test.txt: b.txt
	@echo "Creating $@ ..."
	@echo "all: c.txt">test.txt

# yandeMacBook-Pro:06 yanwallis$ touch a.txt
# yandeMacBook-Pro:06 yanwallis$ make all
# all : a.txt
# yandeMacBook-Pro:06 yanwallis$ touch b.txt
# yandeMacBook-Pro:06 yanwallis$ make all
# Creating test.txt ...
# all : c.txt