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

## 7.条件判断语句
### 1.支持条件判断
- 可以根据条件的值决定make是否执行
- 可以比较不同的变量或者变量和常量值

**注意：** 可以控制make实际执行的语句， 不能控制规则中命令的执行

### 2.条件判断关键字

- ifeq: 是否相等
- ifneq: 是否不相等
- ifdef: 判断变量是否有值
- ifndef: 判断变量是否没值

if语句else以及endif都是4个空格

```makefile
.PHONY : test
var1 := A
var2 := $(var1)
var3 := 

test:
    ifeq ($(var1),$(var2))
		@echo "var1==var2"
    else
		@echo "var1!=var2"
    endif


    ifneq ($(var2),)
		@echo "var2 is not empty"
    else
		@echo "var2 is empty"
    endif

    ifdef var2
		@echo "var2 is not empty"
    else
		@echo "var2 is empty"
    endif

    ifndef var3
		@echo "var3 is empty"
    else
		@echo "var3 is not empty"
    endif

# 执行结果
# var1==var2
# var2 is not empty
# var2 is not empty
# var3 is empty

```

### 3.工程经验

- 条件判断语句之前可以有空格， 但不能有tab字符， 可能会报错【make: else: No such file or directory】
- 在条件判断语句中不要使用自动变量
- 一条完整的条件语句必须位于同一个makefile中
- 条件判断类似于C语言的宏， 预处理阶段有效， 执行阶段无效
- make在加载Makefile时
  - 首先计算表达式的值
  - 根据判断语句的表达式决定执行的内容

```makefile
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


```

## 8.函数定义及调用

### 1.makefile 支持函数的概念

- make解释器提供函数供Makefile调用
- Makefile支持自定义函数实现
- 通过define关键字实现自定义函数

### 2.自定义函数本质

- 本质是一个多行变量， 无法直接调用
- 是一种过程调用， 没有任何返回值
- 用于定义命令集合， 并应用于规则中

### 3.demo1 函数的本质就是多行变量

```makefile
.PHONY : test

define func1
	@echo "my name is $(0)"
endef

define func2
	@echo "my name is $(0)"
	@echo "my age is $(1)"
	@echo "my bith is $(2)"
endef

var := $(call func1)
new := $(func1)

test :
	@echo "new ==> $(new)"
	@echo "var ==> $(var)"
	$(call func1)  # 1.替换参数，2.多行变量替换到此处
	$(call func2, 12)

# define 用于多行变量， 把func1 作为变量来处理
# call 作用就是把相应位置换成实参
# $(call func1) 作用 # 1.替换参数，2.多行变量替换到此处
```

### 4.make解释器的预定义函数

- make的函数提供处理文件名， 变量和命令的函数
- 可以在需要的地方调用函数来处理指定的参数
- 函数在调用的地方被替换为处理结果

### 5.自定义函数和预定义函数本质剖析

- makefile 不支持真正意义上的自定义函数
- 自定义函数的本质是多行变量
- 预定义的call函数在调用时将参数传递给多行变量
- 自定义函数是call函数的实参， 并在call中被执行

### 6.demo 自定义函数是模拟

```makefile
.PHONY : test

define func1
	@echo "my name is $(0)"
endef

func2 := @echo "my name is $(0)"

new := $(abspath ./)

var1 := $(call func1)
var2 := $(call func2)
var3 := $(abspath test.cpp)


test :
	@echo "new ==> $(new)"
	$(call func1)
	$(call func2)

	@echo "var1 ==> $(var1)"
	@echo "var2 ==> $(var2)"
	@echo "var3 ==> $(var3)"

# 结果：
# func1 是自定义函数
# fun2 是普通变量
# new ==> /Users/yanwallis/Documents/cpp-projects/my-makefile/06函数
# my name is func1
# my name is

# var1 ==>        @echo my name is func1
# var2 ==> @echo my name is 
# var3 ==> /Users/yanwallis/Documents/cpp-projects/my-makefile/06函数/test.cpp
```

### 7.小结

- make解释器提供了一系列函数供Makefile调用
- 自定义函数是一个多行变量， 无法直接调用
- 自定义函数用于定义命令集合， 并应用于规则中
- 预定义的call函数在调用时将参数传递给多行变量
- 自定义函数是call函数实参， 并在call中被执行



## 9.变量和函数的综合实例



### 1.需求

- 自动生成target文件夹存放可执行文件
- 自动生成objs文件夹存放编译生成的目标文件（*.o)
- 支持调试版本的编译选项
- 考虑代码的扩展性

### 2.工具

- $(wildcard_pattern): 获取当前工作目录中满足`_pattern`的文件或者目录列表
- `$(addprefix_prefix ,_names)` :  给名字列表`_names` 中每个名字前加前缀  _prefix

### 3.关键技巧

1.自动获取当前目录下源文件列表（函数调用）

```makefile
SRCS := $(wildcard *.c)
```

2.根据源文件列表生成 目标文件列表（变量的替换）

```makefile
OBJS := $(SRCS:.c=.o)
```

3.对每一个目标文件列表加上路径前缀（函数调用）

```makefile
OBJS := $(addprefix path/, $(OBJS))
```

### 4.demo

```makefile
CC := gcc
MKDIR := mkdir
RM := rm -rf

DIR_OBJS := objs
DIR_TARGET := target

DIRS := $(DIR_OBJS) $(DIR_TARGET)

TARGET := $(DIR_TARGET)/hello_makefile.out
# 得到源文件的文件列表， func.c main.c
SRCS := $(wildcard *.c)
# 值替换， func.o， main.o
OBJS := $(SRCS:.c=.o)

# objs/func.o objs/main.o
OBJS := $(addprefix $(DIR_OBJS)/, $(OBJS))

.PHONY : rebuild clean all

$(TARGET) : $(DIRS) $(OBJS)
	$(CC) -o $@ $(OBJS)
	@echo "Target File ==> $@"

$(DIRS) :
	$(MKDIR) $@

$(DIR_OBJS)/%.o : %.c
	$(CC) -o $@ -c $^

rebuild : clean all

all : $(TARGET)

clean :
	@echo "clean"
	$(RM) $(DIRS)

# 执行结果
# gcc -o objs/const.o -c const.c
# gcc -o objs/func.o -c func.c
# gcc -o objs/main.o -c main.c
# gcc -o target/hello_makefile.out objs/const.o objs/func.o objs/main.o
# Target File ==> target/hello_makefile.out

# yandeMacBook-Pro:07变量和函数实战 yanwallis$ ls
# Makefile        const.c         func.c          main.c          objs            target
# yandeMacBook-Pro:07变量和函数实战 yanwallis$ cd target/
# yandeMacBook-Pro:target yanwallis$ ls
# hello_makefile.out
# yandeMacBook-Pro:target yanwallis$ ./hello_makefile.out 
# void foo(): hello makefile

```

### 5.编译选项功能实现

有debug 参数

`make DEBUG:=true`

```makefile
CC := gcc
MKDIR := mkdir
RM := rm -rf

DIR_OBJS := objs
DIR_TARGET := target

DIRS := $(DIR_OBJS) $(DIR_TARGET)

TARGET := $(DIR_TARGET)/hello_makefile.out
# 得到源文件的文件列表， func.c main.c
SRCS := $(wildcard *.c)
# 值替换， func.o， main.o
OBJS := $(SRCS:.c=.o)

# objs/func.o objs/main.o
OBJS := $(addprefix $(DIR_OBJS)/, $(OBJS))

.PHONY : rebuild clean all

$(TARGET) : $(DIRS) $(OBJS)
	$(CC) -o $@ $(OBJS)
	@echo "Target File ==> $@"

$(DIRS) :
	$(MKDIR) $@

$(DIR_OBJS)/%.o : %.c
    ifeq ($(DEBUG), true)
		$(CC) -o $@ -g -c $^
    else
	    $(CC) -o $@ -c $^
    endif

rebuild : clean all

all : $(TARGET)

clean :
	@echo "clean"
	$(RM) $(DIRS)

# 执行结果
# gcc -o objs/const.o -c const.c
# gcc -o objs/func.o -c func.c
# gcc -o objs/main.o -c main.c
# gcc -o target/hello_makefile.out objs/const.o objs/func.o objs/main.o
# Target File ==> target/hello_makefile.out

# yandeMacBook-Pro:07变量和函数实战 yanwallis$ ls
# Makefile        const.c         func.c          main.c          objs            target
# yandeMacBook-Pro:07变量和函数实战 yanwallis$ cd target/
# yandeMacBook-Pro:target yanwallis$ ls
# hello_makefile.out
# yandeMacBook-Pro:target yanwallis$ ./hello_makefile.out 
# void foo(): hello makefile

```



### 6.makefile 语法检查

Makefile语法经常会有问题， 用于检查

```
cat -etv Makefile
```





## 10.自动生成依赖关系

### 1.问题

思考：

- 目标文件是否只依赖源文件
- 编译器如何编译源文件和头文件

编译行为带来的缺陷

- 预处理器将头文件中的代码直接插入到源文件
- 编译器只通过预处理后的源文件产生目标文件
- 因此， 规则中以源文件为依赖， 命令可能无法执行， 如果我们更改了头文件没有响应到更新



### 2.自动生成依赖关系demo1

```shell
yandeMacBook-Pro:08自动生成依赖关系 yanwallis$ ls
func.c          func.h          main.c          makefile
```

#### 1.func.h

```c
#ifndef FUNC_H
#define FUNC_H

#define HELLO "hello yan"

void foo();

#endif
```



#### 2.func.c

```c
#include <stdio.h>
#include "func.h"

void foo(){
    printf("void foo(): %s\n", HELLO);
}
```



#### 3.Main.c

```c
#include <stdio.h>
#include "func.h"

int main(){
    foo();
    return 0;
}
```

#### 4.makefile

```makefile
OBJS := func.o main.o

hello.out : $(OBJS)
	@gcc -o $@ $^
	@echo "Target File ==> $^"


$(OBJS) : %.o:%.c
	@gcc -o $@ -c $^

# 
# yandeMacBook-Pro:08自动生成依赖关系 yanwallis$ make
# Target File ==> func.o main.o
# 更改头文件， 不会重新编译
# yandeMacBook-Pro:08自动生成依赖关系
# make: `hello.out' is up to date.
```

存在问题： 当func.h 头文件改变时， 不会影响hello.out重新编译

#### 5.makefile 更改版1

```makefile
OBJS := func.o main.o

hello.out : $(OBJS)
	@gcc -o $@ $^
	@echo "Target File ==> $^"


$(OBJS) : %.o:%.c func.h
	@gcc -o $@ -c $<

# yandeMacBook-Pro:08自动生成依赖关系 yanwallis$ make
# Target File ==> func.o main.o
# yandeMacBook-Pro:08自动生成依赖关系 yanwallis$ make
# Target File ==> func.o main.o
# yandeMacBook-Pro:08自动生成依赖关系 yanwallis$ 
```

存在问题：

- 头文件作为依赖条件出现于每个目标对应的规则中
- 当头文件改动， 任何源文件都会重新编译， 效率低下
- 当项目中头文件多， Makefile很难维护



#### 6.Makefile更改版2

##### 方案：

- 通过命令自动生成对头文件的依赖
- 将生成的依赖自动包含进Makefile中
- 当头文件改动后， 自动确认需要重新编译的文件

##### 预备工作：

- Linux sed命令
- 编译器依赖生成选项` gcc -MM (gcc -M)`

##### linux的sed命令

- sed 是一个流编辑器， 用于流文本的修改
- sed可用于流文本中的字符串替换
- sed的字符串替换方法为：`sed 's:src:des:g'`

```shell
1.Sed 替换
流： 输入输出中的文本
将流重定向到sed中
yandeMacBook-Pro:08自动生成依赖关系 yanwallis$ echo "test=>abc+abc=abc" |sed "s:abc:xyz:g"
test=>xyz+xyz=xyz

2.sed正则表达式:替换匹配结果； 使用匹配的目标生成替换结果
sed 's,\(.*\)\.o[ :]*,objs/\1.o: ,g'  # 把前面的流的.o之后无论是空格还是冒号， 找到匹配项拼接成 objs/目标

yandeMacBook-Pro:08自动生成依赖关系 yanwallis$ echo "main.o: main.c func.h"
main.o: main.c func.h

yandeMacBook-Pro:08自动生成依赖关系 yanwallis$ echo "main.o: main.c func.h"|sed 's,\(.*\)\.o[ :]*,objs/\1.o: ,g'
objs/main.o: main.c func.h

yandeMacBook-Pro:08自动生成依赖关系 yanwallis$ echo "/a/b/c/d/main.o: main.c func.h"|sed 's,\(.*\)\.o[ :]*,objs/\1.o: ,g'
objs//a/b/c/d/main.o: main.c func.h

```

##### gcc 关键编译选项生成依赖关系

- 获取目标的完整依赖关系：`gcc -M test.c`
- 获取目标的部分依赖关系：`gcc -MM test.c`



##### 命令综合使用

```shell
yandeMacBook-Pro:08自动生成依赖关系 yanwallis$ gcc -M main.c
main.o: main.c \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/stdio.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/_stdio.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/cdefs.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_symbol_aliasing.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_posix_availability.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/Availability.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/AvailabilityInternal.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/_types.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/machine/_types.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/i386/_types.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_pthread/_pthread_types.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_va_list.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/machine/types.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/i386/types.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_int8_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_int16_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_int32_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_int64_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_u_int8_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_u_int16_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_u_int32_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_u_int64_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_intptr_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_uintptr_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_size_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_null.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/stdio.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_off_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_ssize_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/secure/_stdio.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/secure/_common.h \
  func.h
yandeMacBook-Pro:08自动生成依赖关系 yanwallis$ gcc -MM main.c
main.o: main.c func.h
yandeMacBook-Pro:08自动生成依赖关系 yanwallis$ gcc -MM -E main.c
main.o: main.c func.h
yandeMacBook-Pro:08自动生成依赖关系 yanwallis$ gcc -MM -E main.c | sed 's,\(.*\)\.o[ :]*,objs/\1.o: ,g'
objs/main.o: main.c func.h
yandeMacBook-Pro:08自动生成依赖关系 yanwallis$ gcc -M -E main.c | sed 's,\(.*\)\.o[ :]*,objs/\1.o: ,g'
objs/main.o: main.c \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/stdio.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/_stdio.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/cdefs.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_symbol_aliasing.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_posix_availability.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/Availability.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/AvailabilityInternal.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/_types.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/machine/_types.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/i386/_types.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_pthread/_pthread_types.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_va_list.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/machine/types.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/i386/types.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_int8_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_int16_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_int32_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_int64_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_u_int8_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_u_int16_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_u_int32_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_u_int64_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_intptr_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_uintptr_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_size_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_null.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/stdio.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_off_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/sys/_types/_ssize_t.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/secure/_stdio.h \
  /Library/Developer/CommandLineTools/SDKs/MacOSX10.14.sdk/usr/include/secure/_common.h \
  func.h
yandeMacBook-Pro:08自动生成依赖关系 yanwallis$ 
```



##### 小技巧：拆分目标的依赖

将目标的完整依赖拆分为多个部分依赖

```makefile
.PHONY : a b c
# test 依赖a b c
test:a b c
	@echo "$^"
	
# 等价于
.PHONY : a b c
# test 依赖a b c
test:a b
test:b c
test:
	@echo "$^"
	
# yandeMacBook-Pro:08自动生成依赖关系 yanwallis$ make
# a b c
```



#### 7.include关键字

makefile 的include 关键字：

- 类似于C语言的include
- 将其他文件的内容原封不动的搬入当前文件

Make对include关键字的处理方式：在当前目录或者指定目录搜索目标文件

- 搜索成功：将目标文件内容搬入当前Makefile
- 搜索失败：产生警告
  - 以文件名作为目标查找并执行对应规则
  - 当文件名对应的规则不存在时， 最终产生错误

```makefile
.PHONY :all

include test.txt

all:
	@echo "this is $@"

# test.txt:
# 	@echo "test.txt"
# 	@touch test.txt


# 不存在， 执行test.txt 规则， touch 一个test.txt 文件
# yandeMacBook-Pro:08自动生成依赖关系 yanwallis$ make
# makefile:3: test.txt: No such file or directory
# test.txt
# this is all

# 在test.txt 当中定义规则
# yandeMacBook-Pro:08自动生成依赖关系 yanwallis$ make
# this is other
# yandeMacBook-Pro:08自动生成依赖关系 yanwallis$ make all
# this is all
# yandeMacBook-Pro:08自动生成依赖关系 yanwallis$ 
```



#### 8.makefile 中命令的执行机制

- 规则中每个命令默认是在一个新的进程中执行（shell）
- 可以通过连接符（；）将多个命令组合成一个新命令
- 组合的命令一次在同一个进程被执行
- Set -e 指定发生错误后立即退出执行

```makefile
.PHONY : all

all:
	set -e;\
	mkdir test;\
	cd test;\
	mkdir  subtest

# 连接符保证代码在一个进程执行
# yandeMacBook-Pro:08自动生成依赖关系 yanwallis$ make
# set -e;\
#         mkdir test;\
#         cd test;\
#         mkdir  subtest
# ├── test
# │   └── subtest
```



#### 9.自动生成依赖关系解决方案初步思路：

- 通过gcc -MM 和sed 得到.dep依赖文件（目标的部分依赖）
  - 技术点：规则中命令的连续执行
- 通过include指令包含所有的.dep依赖文件
  - 技术点：当.dep 依赖文件不存在时， 使用规则自动生成

```makefile
.PHONY : all clean

MKDIR := mkdir
RM := rm -rf

SRCS := $(wildcard *.c)
DEPS := $(SRCS:.c=.dep)

# include $(DEPS)
# 一般会产生警告， 使用- 没有警告
-include $(DEPS)

all:
	@echo "this is all"

%.dep : %.c
	@echo "Creating $@ ..."
	@set -e;\
	$(CC) -MM -E $^ | sed 's,\(.*\)\.o[ :]*,objs/\1.o : ,g' >$@

clean :
	$(RM) $(DEPS)


# yandeMacBook-Pro:04 yanwallis$ make
# makefile:9: func.dep: No such file or directory
# makefile:9: main.dep: No such file or directory
# Creating main.dep ...
# Creating func.dep ...
# make: Nothing to be done for `objs/func.o'.
# yandeMacBook-Pro:04 yanwallis$ tree
# .
# ├── func.c
# ├── func.dep
# ├── func.h
# ├── main.c
# ├── main.dep
# └── makefile

# 0 directories, 6 files
# yandeMacBook-Pro:04 yanwallis$ 
```

思考： 如何组织依赖文件相关的规则与源码编译相关的规则， 进而行程完整的Makefile程序？





#### 10.把依赖文件生成到指定文件夹

解决思路：

当include发现.dep 不存在时：

- 通过规则和命令创建deps文件夹
- 将所有的.dep 文件创建到deps文件夹
- .dep文件中记录目标文件的依赖关系

##### 方案1：

```makefile
.PHONY : all clean

MKDIR := mkdir
RM := rm -rf
CC := gcc

DIR_DEPS := deps

SRCS := $(wildcard *.c)
DEPS := $(SRCS:.c=.dep)


include $(DEPS)

all: $(DIR_DEPS)
	@echo "this is all"

$(DIR_DEPS) :
	$(MKDIR) $@

%.dep : %.c
	@echo "Creating $@ ..."
	@set -e;\
	$(CC) -MM -E $^ | sed 's,\(.*\)\.o[ :]*,objs/\1.o : ,g' >$@

clean :
	$(RM) $(DEPS)

# 结果：
# 先创建.dep 文件， 后生成deps 文件夹
# yandeMacBook-Pro:05 yanwallis$ make all
# makefile:13: func.dep: No such file or directory
# makefile:13: main.dep: No such file or directory
# Creating main.dep ...
# Creating func.dep ...
# mkdir deps
# this is all
yandeMacBook-Pro:05 yanwallis$ tree
.
├── deps
├── func.c
├── func.dep
├── func.h
├── main.c
├── main.dep
└── makefile

```

##### 方案2：

```makefile
.PHONY : all clean

MKDIR := mkdir
RM := rm -rf
CC := gcc

DIR_DEPS := deps

SRCS := $(wildcard *.c)
DEPS := $(SRCS:.c=.dep)
DEPS := $(addprefix $(DIR_DEPS)/, $(DEPS))

include $(DEPS)

all:
	@echo "this is all"

$(DIR_DEPS) :
	$(MKDIR) $@

# 依赖DIR_DEPS ， 没有先创建DIR_DEPS 文件夹
$(DIR_DEPS)/%.dep : $(DIR_DEPS) %.c
	@echo "Creating $@ ..."
	@set -e;\
	$(CC) -MM -E $(filter %.c, $^) | sed 's,\(.*\)\.o[ :]*,objs/\1.o : ,g' >$@

clean :
	$(RM) $(DEPS)

# 结果：
# 先创建.dep 文件， 后生成deps 文件夹
# yandeMacBook-Pro:05 yanwallis$ make all
# makefile:13: func.dep: No such file or directory
# makefile:13: main.dep: No such file or directory
# Creating main.dep ...
# Creating func.dep ...
# mkdir deps
# this is all

```

为什么一些.dep 依赖文件会被重复多次创建？判断

##### 方案3：

```makefile
.PHONY : all clean

MKDIR := mkdir
RM := rm -rf
CC := gcc

DIR_DEPS := deps

SRCS := $(wildcard *.c)
DEPS := $(SRCS:.c=.dep)
DEPS := $(addprefix $(DIR_DEPS)/, $(DEPS))

include $(DEPS)

all:
	@echo "this is all"

$(DIR_DEPS) :
	$(MKDIR) $@

# 当前目录是否有DIR_DEPS 文件夹， 没有指向创建依赖， 有， 不执行创建依赖
ifeq ("$(wildcard $(DIR_DEPS))", "")
# 依赖DIR_DEPS ， 没有先创建DIR_DEPS 文件夹
$(DIR_DEPS)/%.dep : $(DIR_DEPS) %.c
else
$(DIR_DEPS)/%.dep : %.c
endif 
	@echo "Creating $@ ..."
	@set -e;\
	$(CC) -MM -E $(filter %.c, $^) | sed 's,\(.*\)\.o[ :]*,objs/\1.o : ,g' >$@

clean :
	$(RM) $(DEPS)

# 结果：
# 先创建.dep 文件， 后生成deps 文件夹
# yandeMacBook-Pro:05 yanwallis$ make all
# makefile:13: func.dep: No such file or directory
# makefile:13: main.dep: No such file or directory
# Creating main.dep ...
# Creating func.dep ...
# mkdir deps
# this is all

```



##### 方案4

```makefile
.PHONY : all clean

MKDIR := mkdir
RM := rm -rf
CC := gcc

DIR_DEPS := deps

SRCS := $(wildcard *.c)
DEPS := $(SRCS:.c=.dep)
DEPS := $(addprefix $(DIR_DEPS)/, $(DEPS))

all:
	@echo "this is all"

# 对输入参数判断
ifeq ("$(MKKECMDGOALS)", "all")
include $(DEPS)
endif

ifeq ("$(MKKECMDGOALS)", "")
include $(DEPS)
endif



$(DIR_DEPS) :
	$(MKDIR) $@

ifeq ("$(wildcard $(DIR_DEPS))", "")
# 依赖DIR_DEPS ， 没有先创建DIR_DEPS 文件夹
$(DIR_DEPS)/%.dep : $(DIR_DEPS) %.c
else
$(DIR_DEPS)/%.dep : %.c
endif 
	@echo "Creating $@ ..."
	@set -e;\
	$(CC) -MM -E $(filter %.c, $^) | sed 's,\(.*\)\.o[ :]*,objs/\1.o : ,g' >$@

clean :
	$(RM) $(DIR_DEPS)

# 结果：
# 先创建.dep 文件， 后生成deps 文件夹
# yandeMacBook-Pro:05 yanwallis$ make all
# makefile:13: func.dep: No such file or directory
# makefile:13: main.dep: No such file or directory
# Creating main.dep ...
# Creating func.dep ...
# mkdir deps
# this is all

```



#### 11.include 暗黑操作

##### 1.使用（-）不但关闭了include的警告， 同时关闭了错误， 当错误发生时， make将忽略这些错误

##### 2.如果include 触发规则创建文件， 之后还发生了什么？

```makefile
.PHONY : all

include test.txt

all:
	@echo "this is $@"

test.txt:
	@echo "Creating $@ ..."
	@echo "other:;@echo "this is other" " >test.txt

# 创建test.txt 是一个规则， 搬入， 执行
# yandeMacBook-Pro:06 yanwallis$ make
# makefile:3: test.txt: No such file or directory
# Creating test.txt ...
# this is other

# yandeMacBook-Pro:06 yanwallis$ make all
# makefile:3: test.txt: No such file or directory
# Creating test.txt ...
# this is all
```

##### 3.如果include包含的文件存在， 之后发生什么？

1.先看文件是否有， 如果有

2.查看文件规则的依赖的时间戳， 如果依赖比较新， 会执行该规则， 反之不执行

```makefile
.PHONY : all

include test.txt

all:
	@echo "this is $@"

# b.txt 时间戳比test.txt时间戳新， 会继续向下执行
test.txt: b.txt
	@echo "Creating $@ ..."

# yandeMacBook-Pro:06 yanwallis$ tree
# .
# ├── b.txt
# ├── makefile
# ├── makefile1
# └── test.txt

# 0 directories, 4 files
# yandeMacBook-Pro:06 yanwallis$ make all
# Creating test.txt ...
# this is all
# yandeMacBook-Pro:06 yanwallis$ touch test.txt
# yandeMacBook-Pro:06 yanwallis$ make all
# this is all
# yandeMacBook-Pro:06 yanwallis$ 
```

##### 4.include 会重新加载文件内容

```makefile
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
```

5.include 总结

- 当目标文件不存在
  - 以文件名查找规则并执行
  - 查找到规则中创建了目标文件
    - 将创建成功的目标文件包含进当前Makefile
- 当目标文件存在
  - 将目标文件包含进当前Makefile
  - 以目标文件名查找使用有相应规则
    - 有， 比较规则的依赖关系， 决定是否执行规则的命令
    - 无， 无操作



#### 12.自动生成依赖关系demo

当.dep 生成之后， 如果动态改变头文件依赖关系， make可能检测不到头文件改变 

```makefile
.PHONY : all clean rebuild

MKDIR := mkdir
RM := rm -rf
CC := gcc

DIR_DEPS := deps
DIR_EXES := exes
DIR_OBJS := objs

DIRS := $(DIR_DEPS) $(DIR_EXES) $(DIR_OBJS)

EXE := app.out
EXE := $(addprefix $(DIR_EXES)/, $(EXE))

SRCS := $(wildcard *.c)

OBJS := $(SRCS:.c=.o)
OBJS := $(addprefix $(DIR_OBJS)/, $(OBJS))

DEPS := $(SRCS:.c=.dep)
DEPS := $(addprefix $(DIR_DEPS)/, $(DEPS))

all: $(DIR_OBJS) $(DIR_EXES) $(EXE)

# 对输入参数判断
ifeq ("$(MKKECMDGOALS)", "all")
include $(DEPS)
endif

ifeq ("$(MKKECMDGOALS)", "")
include $(DEPS)
endif

$(EXE) : $(OBJS)
	$(CC) -o $@ $^
	@echo "succeddful! Target=>$(EXE)"

$(DIR_OBJS)/%.o : %.c
	$(CC) -o $@ -c $(filter %.c, $^)


$(DIRS) :
	$(MKDIR) $@

ifeq ("$(wildcard $(DIR_DEPS))", "")
# 依赖DIR_DEPS ， 没有先创建DIR_DEPS 文件夹
$(DIR_DEPS)/%.dep : $(DIR_DEPS) %.c
else
$(DIR_DEPS)/%.dep : %.c
endif 
	@echo "Creating $@ ..."
	@set -e;\
	$(CC) -MM -E $(filter %.c, $^) | sed 's,\(.*\)\.o[ :]*,objs/\1.o $@: ,g' >$@

clean :
	$(RM) $(DIRS)

rebuild :
	@$(MAKE) clean
	@$(MAKE) all


# yandeMacBook-Pro:07 yanwallis$ tree
# .
# ├── define.h
# ├── func.c
# ├── func.h
# ├── main.c
# ├── makefile
# ├── new.h
# └── other.h

# 0 directories, 7 files
# yandeMacBook-Pro:07 yanwallis$ make 
# makefile:32: deps/func.dep: No such file or directory
# makefile:32: deps/main.dep: No such file or directory
# mkdir deps
# Creating deps/main.dep ...
# Creating deps/func.dep ...
# mkdir objs
# mkdir exes
# gcc -o objs/func.o -c func.c
# gcc -o objs/main.o -c main.c
# gcc -o exes/app.out objs/func.o objs/main.o
# succeddful! Target=>exes/app.out
# yandeMacBook-Pro:07 yanwallis$ tree
# .
# ├── define.h
# ├── deps
# │   ├── func.dep
# │   └── main.dep
# ├── exes
# │   └── app.out
# ├── func.c
# ├── func.h
# ├── main.c
# ├── makefile
# ├── new.h
# ├── objs
# │   ├── func.o
# │   └── main.o
# └── other.h

# 3 directories, 12 files


```



## 11.make的隐式规则









## 12.make中的路径搜索

## 13.路径搜索的综合实例

## 14.打造专业的编译环境

## 15.模块独立编译的支持

## 16.第三方库的使用支持



