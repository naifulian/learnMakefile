# Makefile 基础

## Ex1
将 a.txt 和 b.txt 的内容合并然后写入到一个中间文件 m.txt，再将中间文件 m.txt 和 c.txt 的内容写入到 x.txt

可以看出 x.txt 依赖于 m.txt 和 c.txt，而 m.txt 又依赖于 a.txt 和 b.txt

## 规则

1. Makefile由若干条规则（Rule）构成，每一条规则指出一个目标（Target），
若干依赖文件（prerequisites），以及生成目标文件的命令。
- target：目标,需要生成的文件或伪目标
- pre1 pre2 ... : 构建 target 所需的文件或其他目标
- recipe: 生成 target 的命令，必须以 tab 开头

```bash
target: pre1 pre2 ...
    recipe
```


2. make 默认执行 makefile 的第一条规则

3. make 使用文件的创建和修改时间来判断是否需要更新一个目标文件，这样可以做到增量编译，对于大型项目来说，增量编译可以节省很多时间(比如对于某一个模块的修改只涉及到几个文件，但是使用全量编译需要重新编译整个项目)
    - 比如在 make 之后生成了最终的文件 x.txt 且再也没有修改生成 x.txt 需要依赖的文件，即 x.txt 的创建时间晚于它依赖的 m.txt 和 c.txt 的最后修改时间，此时 make 不会再执行任何规则
    - 当修改了 m.txt 后再 make 会触发 x.txt 的更新，但是不会触发 m.txt 的更新，原因是 m.txt 依赖的 a.txt 和 b.txt 并没有修改
    - 当修改了 a.txt 或 b.txt 之后，m.txt 和 x.txt 都会更新

4. 是否能正确地实现增量更新，取决于我们的规则写得对不对，make 本身并不会检查规则逻辑是否正确。


## 伪目标

像是 clean 这个规则就是一个伪目标，表示"清理"这个动作而不是生成 clean 这个文件
- clean 没有依赖文件，要执行 clean 必须使用命令 make clean
- make clean 时，make 会执行 target clean 下面的命令，没有创建一个名为 clean 的文件，所以由于目标文件 clean 不存在，每次运行 make clean，都会执行这个规则的命令
- target 的名字和某一个文件名的刚好一样时，比如此时 Ex1 目录下有个 clean 文件，再 make clean 时，clean 规则就不会执行了

如果希望 make 将 clean 视为一个动作而不是文件，可以将 clean 添加进 .PHONY 
```bash
.PHONY: clean
clean:
    rm m.txt x.txt
```

- 大型项目通常会提供clean、install这些约定俗成的伪目标名称，方便用户快速执行特定任务
- 一般来说，并不需要用 .PHONY 标识 clean 等约定俗成的伪目标名称，除非有人不小心手动了和伪目标名字相同的的文件

## 执行多条命令

一个规则下可以有多条命令:
```bash
cd_f:
	pwd
	cd ..
	pwd 
```

但是当 make cd 之后并没有切换工作目录到上一级目录，这是因为**make针对每行命令，都会创建一个独立的Shell环境**

解决方法：
1. 将多条命令以 ; 分割，写到一行
```bash
cd_t:
    pwd;cd ..;pwd;
```
- 必须在同一行，以 ; 分割命令
- makefile 的命令是在用户的 shell 创建的子 shell 中执行的，不会影响当前用户的 shell 环境

2. 还可以类似于 C/C++ 的函数宏中使用的 \ 将一行语句拆成多行，但效果还是多行的
```bash
cd_t:
    pwd\
    cd ..\
    pwd\
```
3. 使用 && 
```bash
cd_ok:
	cd .. && pwd
```


## 控制打印

默认情况下，make会打印出它执行的每一条命令。如果我们不想打印某一条命令，可以在命令前加上@，表示不打印命令（但是仍然会执行）
```bash
no_output:
	@echo 'not display'
	echo 'will display'
```

make 的参数
1. -s：相当于每条指令前面都加上 @，不会输出任何命令
2. -n：仅打印命令，不执行

## 验证 shell 特性
```bash
test_simple:
	@echo "Line 1 PID: $$$$"
	@echo "Line 2 PID: $$$$"
	
test_one_line:
	@echo "Line 1 PID: $$$$"; \
	echo "Line 2 PID: $$$$"
```

上面这两种写法的一个运行结果：
```bash
$ make test_simple 
Line 1 PID: 260980
Line 2 PID: 260981
$ make test_one_line 
Line 1 PID: 261082
Line 2 PID: 261082
```

## 控制错误

make在执行命令时，会检查每一条命令的返回值，如果返回错误（非0值），就会中断执行

比如 target clean 的命令 rm 的时候不加 -f 参数删除一个不存在的文件（通常是已经 make clean 过了）时会报错 
- rm -f 用于强制删除文件
    - 强制删除：忽略不存在的文件和参数，不会提示确认。在删除文件时，如果文件不存在，它不会显示错误消息
    - 忽略确认：通常，如果用户没有写权限，rm会提示用户是否删除。使用-f选项后，将直接删除而不进行任何提示

```bash
make clean_error 
rm m.txt x.txt
rm: cannot remove 'm.txt': No such file or directory
rm: cannot remove 'x.txt': No such file or directory
make: *** [Makefile:44: clean_error] Error 1
```

由于 make clean 删除的一般都是编译的过程文件和最终文件，我们想忽略错误，继续执行后续命令，可以在需要忽略错误的命令前加上 `-`


