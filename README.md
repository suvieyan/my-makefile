# my-makefile
[toc]
## 1.make和makefile

### 1.什么是make

- 命令， 用于编译源代码
- 本质是一个应用程序

	- 解析源程序之间的依赖关系
	- 根据依赖关系自动维护编译工作
	- 执行宿主操作系统的各项命令

### 2.makefile是什么

- 是一个描述文件

	- 定义一系列规则来指定源文件编译的先后顺序
	- 拥有特定的语法规则，支持函数定义和函数调用
	- 能够直接集成操作系统的各项命令

### 3.make和makefile的关系

- makefile描述指导make程序如何完成工作
- make根据makefile的规则执行命令， 编译输出

### 4.执行

- make hello

	- 查找当前目录下makefile或者MakeFile文件， 执行hello处

- make

	- 执行makefile的顶层

### 5.示例

```shell
yandeMacBook-Pro:yan-test yanwallis$ vim make.txt
yandeMacBook-Pro:yan-test yanwallis$ make -f make.txt 
echo "hello yan"
hello yan
```

- make 解释器执行makefile脚本当中的命令

## 2.初识makefile的结构


### makefile的意义

- 定义源文件的依赖文件

	- targets：prerequisites; command1; '\t'command2

- 说明如何编译各个源文件并生成可执行文件

### makefile语法

- makefile 元素

	- targets

		- 要生成的目标文件名
		- make所需执行的命令名称
		- 可以包含多个目标， 使用空格对多个目标名进行分割

	- prerequisites

		- 当前目标所依赖的其他目标或文件
		- 可以包含多个依赖， 使用空格对多个依赖进行分割

	- command

		- 完成目标所需要执行的命令

- 注意点：

	- tab键
	- 续行符：“\”

- makefile依赖规则

	- 1.目标对应的文件不存在， 执行对应命令
	- 2.依赖在时间上比命令更新， 执行对应命令
	- 3.依赖关系连续发生时， 对比依赖上的每一个目标

### makefile 依赖示例

- 案例一

  ```makefile
  hello:test
      echo "hello yan"
  
  test:
      echo "test"
      pwd
      ls
  
  ```

- 案例二

  ```makefile
  all:test
      @echo "make all"
  
  test:
      @echo "make test"
  ```

	- 注意：无回写字符

### 编译案例

```makefile
hello.out all:func.o main.o
    gcc -o hello.out func.o main.o

func.o:func.c
    gcc -o func.o -c func.c

main.o:main.c
    gcc -o main.o -c main.c
```

- 1.当生成过main.o或者func.o之后， 如果修改func.c 文件， 再次进行make， main.o不会再次生成
- 2.生成过hello.out 且没有进行更新的时候， 不会重新生成

## 3.伪目标的引入

### make 初衷是为了编译C语言的源文件

### 场景
如果我们在Makefile当中写一个目标clean， 同时在当前目录new一个clean的文件， 当我们执行make clean的时候， 不会执行Makefile当中的clean命令， 因为make命令先找文件， 我们可以通过伪目标实现。 比如我们先声明clean是伪目标

```makefile
.PHONY:clean
##注释##
clean:
    rm *.o hello.out
```

### Makefile的目标是什么

- 默认情况下：

	- Makefile认为目标对应一个文件
	- Makefile比较目标文件和依赖文件的新旧关系， 决定是否执行命令
	- make以文件处理作为第一优先级

### Makefile中的伪目标

- 通过.PHONY声明一个伪目标
- 伪目标不对应任何实际的文件
- 不管伪目标的依赖是否更新， 命令总是执行

### 伪目标的语法

- 先声明后使用
- 本质

	- 伪目标是make中特殊目标.PHONY的依赖

- 语法

```makefile
.PHONY:clean
##注释##
clean:
    rm *.o hello.out
```

### 为目标的调用技巧

- 1.规则调用（函数调用）

## 4.变量和不同的赋值方式


### 变量

- 文本数据， 只支持字符串
- 命名规则

	- 数字，字母下划线
	- 不能包含冒号， #， = 以及空格
	- 大小写敏感

### 赋值方式

#### 1.简单赋值（:=）

```makefile
x := foo
y := $(x)b
x := new
.PHONY :test
test:
  @echo "x=>$(x)"
  @echo "y=>$(y)"

# 结果：
#x=>new
#y=>foob
```
#### 2.直接赋值
#### 3.递归赋值（=）

```makefile
# x = foo
y = $(x)b
x = new
.PHONY :test
test:
  @echo "c=>$(c)"
  @echo "y=>$(y)"
  @echo "y=>$(y)"

# 结果：
# c=>
# x=>new
# y=>newb
```

- 可以先使用， 再赋值

#### 4.条件赋值（?=）

```makefile
x := foo
y := $(x)b
x ?= new
.PHONY :test
test:
  @echo "x=>$(x)"
  @echo "y=>$(y)"

# 结果：
# x=>foo
# y=>foob
```

- 没有赋值，使用赋值符号中的值； 有赋值， 就不使用
#### 5.追加赋值（+=）

```makefile
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
```

- 追加， 类似字符串追加， 但是使用空格隔开进行连接

## 5.预定义变量的使用


### 自动变量

- $@

	- 当前规则中的目标

- $^

	- 所有依赖

- $<

	- 第一个依赖

- 注意转义符号：

	- $是makefile当中的转义
	- \ 是shell当中的转义
	- $@ 在shell当中有特殊含义， 需要转义
#### 自动变量demo1
```makefile
.PHONY : all first second third

all : first second third
	@echo "\$$@=>$@"
	@echo "$$^=>$^"
	@echo "$$<=>$<"

first:
second:
third:

# 结果：
# $@=>all
# $^=>first second third
# $<=>first
```
#### 自动变量demo2
1.原始案例
##### func.c
```c
#include<stdio.h>

void foo(){
  printf(" hello makefile");
}

```
##### main.c
```c
extern void foo();
int main()
{
	foo();

	return 0;
}
```
##### makefile
```makefile
TARGET := hello.out
CC := gcc


$(TARGET):func.o main.o
	$(CC) -o $(TARGET) func.o main.o

func.o:func.c
	$(CC) -o func.o -c func.c

main.o:main.c
	$(CC) -o main.o -c main.c

.PHONY : rebuild clean all

rebuild: clean all

all : $(TARGET)

clean:
	rm *.o $(TARGET)
```
2.使用自动变量进行改写
```makefile
TARGET := hello.out
CC := gcc


$(TARGET):func.o main.o
	$(CC) -o $@ $^

func.o:func.c
	$(CC) -o $@ -c $^

main.o:main.c
	$(CC) -o $@ -c $^

.PHONY : rebuild clean all

rebuild: clean all

all : $(TARGET)

clean:
	rm *.o $(TARGET)


# 执行结果
# gcc -o func.o -c func.c
# gcc -o main.o -c main.c
# gcc -o hello.out func.o main.o

```


### 特殊变量
#### $(MAKE):当前make解释器文件名
#### $(MAKECMDGOALS):命令行中指定的目标名（make的命令行参数）
#### $(MAKEFILE_LIST):当前要处理的Makefile列表
- make所需要处理的Makefile文件列表
- 当前Makefile的文件名总是位于列表的最后
- 文件名之间以空格进行分割

```makefile
.PHONY : all out first second third test

all out: first second third
	@echo "$(MAKE)"  
	@echo "$(MAKECMDGOALS)"
	@echo "$(MAKEFILE_LIST)"

first:
	@echo "first" 
second:
	@echo "second" 
third:
	@echo "third" 
test:
	@$(MAKE) first
	@$(MAKE) second
	@$(MAKE) third

# make结果：
# /Library/Developer/CommandLineTools/usr/bin/make   # make 解释器
# 不打印输出， 因为是make调用, 没有指定具体的命令， 命令行的目标
#  makefile  # 本文件名


# make test 结果
# yandeMacBook-Pro:03预定义变量2 yanwallis$ make test
# first
# second
# third
```

#### $(MAKE_VERSION):当前make解释器版本号
#### $(CURDIR):当前解释器的工作目录
#### $(.VARIABLES):所有已经定义的变量名列表
#### $(RM):
```makefile
.PHONY : test1 test2
YAN Author := yan wallis

test1 : 
	@echo "$(MAKE_VERSION)"  
	@echo "$(CURDIR)"
	@echo "$(.VARIABLES)"

test2:
	@echo "$(RM)"


# make test1 结果
# 3.81  版本
# /Users/yanwallis/Documents/cpp-projects/yan-test/03预定义变量2
# <D ?F CWEAVE ?D @D @F PC CURDIR SHELL RM CO _ PREPROCESS.F LINK.m LINK.o OUTPUT_OPTION COMPILE.cpp MAKEFILE_LIST LINK.p CC CHECKOUT,v LEX.m CPP Apple_PubSub_Socket_Render LINK.cc PATH LD TEXI2DVI YACC COMPILE.mod ARFLAGS LINK.r LINT COMPILE.f LINT.c YACC.m YACC.y AR .FEATURES TANGLE SSH_AUTH_SOCK GET %F COMPILE.F CTANGLE .LIBPATTERNS LINK.C PWD LINK.S PREPROCESS.r LINK.c LINK.s HOME MAKEFILEPATH LOGNAME ^D COMPILE.m XPC_FLAGS COLORTERM MAKE MAKECMDGOALS SHLVL AS PREPROCESS.S COMPILE.p MAKE_VERSION USER FC .DEFAULT_GOAL %D WEAVE MAKE_COMMAND LINK.cpp F77 OLDPWD TERM_PROGRAM .VARIABLES TMPDIR *F COMPILE.def LEX MAKEFLAGS YAN Author MFLAGS *D TERM_PROGRAM_VERSION LEX.l XPC_SERVICE_NAME +D COMPILE.r +F M2C __CF_USER_TEXT_ENCODING MAKEFILES COMPILE.cc <F CXX COFLAGS COMPILE.C ^F COMPILE.S LINK.F SUFFIXES COMPILE.c COMPILE.s .INCLUDE_DIRS MAKELEVEL MAKEINFO TEX LANG TERM F77FLAGS LINK.f GNUMAKE


# make test2 结果
# rm -f   

```



## 6.变量的高级主题


### 1.变量值的替换
- 使用指定字符或字符串替换变量值中的后缀字符或字符串
- 语法格式：$(var:a=b) 或${var:a=b}
    -  替换表达式中不能有空格
    -  make支持${}对变量取值


### 2.变量的模式替换
- 使用%保留变量值中的指定字符
- 语法格式：$(var:a%b=x%y) 或${var:a%b=x%y}
    -  替换表达式中不能有空格
    -  make支持${}对变量取值

### 3.规则中的模式替换
targets :target-pattern:prereq-pattern
    command1
    command2
意义：通过target-pattern从targets当中匹配子目标， 再通过prereq-pattern从子目标生成依赖， 进而构成完整的规则

```makefile
OBJS := func.o main.o 
$(OBJS) : %.o: %.c
	gcc -o $@ -c $^
    
找到func.o， 生成以下
等价于
func.o :func.c
	gcc -o $@ -c $^	

main.o :main.c
	gcc -o $@ -c $^	
```
#### 案例1
```makefile
src1 := a.cc b.cc c.cc
obj1 := $(src1:cc=o) 
test1:
	@echo "obj1=>$(obj1)"

# make test1
# obj1=>a.o b.o c.o 

src2 := a1b.c a2b.c ag3b.c
obj2 := $(src2:a%b.c=x%y) 
test2:
	@echo "obj2=>$(obj2)"

# make test2
# obj2=>x1y x2y xg3y 

```

#### 案例2
##### 1.func.c
```c
#include<stdio.h>

extern char* g_hello;

void foo(){
  printf("void foo(): %s\n", g_hello);
}
```
##### 2.main.c
```c
extern void foo();
int main()
{
	foo();

	return 0;
}

```
##### 3.const.c
```c
const char* g_hello ="hello makefile";
```

##### 4.makefile
```makefile
TARGET := hello.out
CC := gcc
OBJS := func.o main.o const.o

$(TARGET):$(OBJS)
	$(CC) -o $@ $^

$(OBJS): %.o : %.c
	$(CC) -o $@ -c $^

.PHONY : rebuild clean all

rebuild: clean all

all : $(TARGET)

clean:
	$(RM) *.o $(TARGET)

# 执行结果
# gcc -o func.o -c func.c
# gcc -o const.o -c const.c
# gcc -o hello.out func.o main.o const.o

```
### 4.变量值的嵌套引用
- 一个变量名中可以包含对其他变量的引用
- 嵌套引用的本质是使用同一个变量表示另外一个变量
```makefile
x := y
y := z
a := $($(x))
$(x) 就是y
$(y) 就是z
所以a := z
```
### 5.命令行变量
- 运行make时， 在命令行定义变量
- 命令行变量默认覆盖Makefile中定义的变量
```makefile
hm := hello makefile

test:
	@echo "hm=>$(hm)"

# 执行结果
# yandeMacBook-Pro:04变量高级 yanwallis$ make
# hm=>hello makefile
# yandeMacBook-Pro:04变量高级 yanwallis$ make hm=cmd
# hm=>cmd
```

### 6.override 关键字
- 指示Makefile中定义的变量不能被覆盖
- 变量定义和赋值都需要override关键字
```makefile
override var := test
test :
	@echo "var => $(var)"

# 执行结果
# yandeMacBook-Pro:04变量高级 yanwallis$ make
# var => test
# yandeMacBook-Pro:04变量高级 yanwallis$ make var=cmd
# var => test
```

### 7.define 关键字
- makefile中定义多行变量
- 多行变量的定义从变量名开始到endef 结束
- 可使用override关键字防止被覆盖
- define定义的变量等价于使用=定义的变量
```makefile
define foo
I'm fool!
endef

override define cmd
	@echo "run cmd ls..."
	@ls
endef

test :
	@echo "foo=>$(foo)"
	$(cmd)

# 执行结果
# yandeMacBook-Pro:04变量高级 yanwallis$ make foo:=verfoo
# foo=>verfoo
# run cmd ls...
# const.c         func.c          main.c          makefile        makefile1       makefile2       makefile3       makefile4
```
### 8.环境变量（全局变量）
- 能够直接使用环境变量的值
- 定义了同名变量， 环境变量将被覆盖
- 运行make时指定-e选项， 优先使用环境变量

#### 1.为什么使用环境变量
环境变量可以在所有的makefile中使用
可移植性低
#### 2.变量在不同Makefile之间的传递方式
- 直接在外部定义环境变量进行传递
- 使用export定义变量进行传递（定义临时环境变量）
- 定义make命令行变量进行传递

##### makefile.2
```makefile
JAVA_HOME := my java home 

test:
	@echo "JAVA_HOME=>$(JAVA_HOME)"
	@echo "var=>$(var)"
	@echo "new=>$(new)"
```
##### makefile
```makefile
JAVA_HOME := java home 
export var := yan wallis
new := wallis

test:
	@echo "JAVA_HOME=>$(JAVA_HOME)"
	@echo "make author file ..."
	@$(MAKE) -f makefile.2
	@$(MAKE) -f makefile.2 new:=$(new)

# 执行结果
# yandeMacBook-Pro:04变量高级 yanwallis$ make
# JAVA_HOME=>java home 
# make author file ...
# JAVA_HOME=>my java home 
# var=> yan wallis # export 临时创建环境变量传递
# new=>
# JAVA_HOME=>my java home 
# var=>yan wallis
# new=>wallis
```

### 9.目标变量（局部变量）
- 作用域只在指定目标及连带规则中
- target: name <assignment> value
- target: override name <assignment> value

```makefile
var := yan wallis
test : var := test-var

test:
	@echo "test:"
	@echo "var => $(var)"

test1:
	@echo "test1:"
	@echo "var => $(var)"

# 执行结果
# yandeMacBook-Pro:04变量高级 yanwallis$ make
# test:
# var => test-var
# yandeMacBook-Pro:04变量高级 yanwallis$ make test1
# test1:
# var => yan wallis
```
### 10.模式变量
- 模式变量是目标变量的扩展
- 作用域只在复合模式的目标及连带规则中
    - pattern: name <assignment> value
    - pattern: override name <assignment> value

```makefile
new := yan wallis
%e : override new := test-new

rule :
	@echo "rule:"
	@echo "new=>$(new)"

test :
	@echo "rule:"
	@echo "new=>$(new)"

# 执行结果
# %e ： 以e结尾的规则的统配
# yandeMacBook-Pro:04变量高级 yanwallis$ make
# rule:
# new=>test-new
# yandeMacBook-Pro:04变量高级 yanwallis$ make test
# rule:
# new=>yan wallis

#yandeMacBook-Pro:04变量高级 yanwallis$ make rule test new:=cmd-new
# rule:
# new=>test-new
# rule:
# new=>cmd-new
```

