# 将 target 视为伪目标
.PHONY: clean test_pids
# Make 默认执行第一条规则
# 由于 x.txt 依赖的一个文件 m.txt 不存在，make 会找
# 有没有生成 m.txt 的规则
x.txt: m.txt c.txt
	cat m.txt c.txt > x.txt
# 先执行规则 m.txt 再执行规则 x.txt
m.txt: a.txt b.txt
	cat a.txt b.txt > m.txt

# 多条命令的指令，​​每一行命令都在独立的shell进程中执行，不起作用
cd_false1:
	pwd
	cd ..
	pwd

# 未写在同一行，同样不起作用
cd_false2:
	pwd;
	cd ..;
	pwd;

# ​​所有命令在同一行​​，所以在同一个shell进程中执行，但 make 使用的 shell 不会影响户当前的 shell
cd_true:
	pwd;cd ..;pwd;

no_output:
	@echo "not display"
	echo "display"


# 验证 make 的特性, echo $$$$ 显示当前 shell 的 PID
test_simple:
	@echo "Line 1 PID: $$$$"
	@echo "Line 2 PID: $$$$"
	
test_one_line:
	@echo "Line 1 PID: $$$$"; \
	echo "Line 2 PID: $$$$"

# 如果 m.txt 和 x.txt 已经不存在则 make_error 会报错
clean_error:
	rm m.txt x.txt

# 加上 -f 参数
clean_ok1:
	rm -f m.txt x.txt

# 加上 - 忽略错误，继续执行
clean_ok2:
	-rm m.txt x.txt\
	echo "i am here"