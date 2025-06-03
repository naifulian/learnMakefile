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



