# Make 默认执行第一条规则
# 由于 x.txt 依赖的一个文件 m.txt 不存在，make 会找
# 有没有生成 m.txt 的规则
x.txt: m.txt c.txt
	cat m.txt c.txt > x.txt
# 先执行规则 m.txt 再执行规则 x.txt
m.txt: a.txt b.txt
	cat a.txt b.txt > m.txt
clean:
	rm m.txt x.txt