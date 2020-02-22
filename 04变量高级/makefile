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