GDB（GNU Debugger）是Linux下强大的程序调试工具。以下是详细的使用指南：
## 1. 基本准备
### 编译时添加调试信息
```bash
gcc -g -o program program.c
# 或者
gcc -g3 -O0 -o program program.c
```
## 2. 启动GDB
```bash
# 启动GDB并加载程序
gdb ./program

# 或者启动后加载
gdb
(gdb) file ./program

# 直接带参数启动
gdb --args ./program arg1 arg2
```
## 3. 常用命令
### 运行控制
```bash
run [args]          # 运行程序 (r)
run arg1 arg2       # 带参数运行
start               # 运行到main函数第一行
continue            # 继续执行 (c)
kill                # 终止当前程序
quit                # 退出GDB (q)
```
### 断点设置
```bash
break main          # 在main函数设置断点 (b main)
break 15            # 在第15行设置断点 (b 15)
break test.c:20     # 在test.c第20行设置断点
break *0x400526     # 在指定地址设置断点
info breakpoints    # 查看所有断点 (info b)
delete 1            # 删除编号为1的断点
delete              # 删除所有断点
disable 1           # 禁用断点
enable 1            # 启用断点
```
### 单步执行
```bash
step                # 单步执行，进入函数 (s)
next                # 单步执行，不进入函数 (n)
stepi               # 单步执行一条汇编指令 (si)
nexti               # 单步执行一条汇编指令，不进入函数 (ni)
finish              # 执行完当前函数并返回
until 20            # 执行到第20行
```
### 查看变量和内存
```bash
print variable      # 打印变量值 (p variable)
print *pointer      # 打印指针指向的内容
print array[0]@10   # 打印数组前10个元素
print/x variable    # 以十六进制显示
print/d variable    # 以十进制显示
print/c variable    # 以字符显示
display variable    # 每次停止时自动显示变量值
info locals         # 显示局部变量
info args           # 显示函数参数
```
### 栈和函数调用
```bash
backtrace           # 显示调用栈 (bt)
frame 2             # 切换到第2层栈帧
info frame          # 显示当前栈帧信息
info registers      # 显示寄存器内容
```
### 内存查看
```bash
x/10i $pc           # 显示程序计数器附近的10条指令
x/10x 0x601040      # 以十六进制显示地址内容
x/10c 0x601040      # 以字符显示地址内容
x/10d 0x601040      # 以十进制显示地址内容
```
## 4. 实际调试示例
假设有以下程序 `test.c`：
```c
#include <stdio.h>

int factorial(int n) {
    if (n <= 1) return 1;
    return n * factorial(n - 1);
}

int main() {
    int num = 5;
    int result = factorial(num);
    printf("Factorial of %d is %d\n", num, result);
    return 0;
}
```
调试过程：
```bash
$ gcc -g -o test test.c
$ gdb ./test

(gdb) break main        # 在main函数设置断点
(gdb) run               # 运行程序
(gdb) next              # 执行到下一行
(gdb) print num         # 查看num的值
(gdb) step              # 进入factorial函数
(gdb) backtrace         # 查看调用栈
(gdb) continue          # 继续执行
(gdb) quit              # 退出
```
## 5. 高级功能
### 条件断点
```bash
break 10 if i == 5      # 当i等于5时在第10行中断
```
### 观察点
```bash
watch variable          # 当变量改变时中断
rwatch variable         # 当变量被读取时中断
awatch variable         # 当变量被访问时中断
```
### 命令脚本
```bash
commands 1              # 为断点1设置命令
>print variable
>continue
>end
```
## 6. 实用技巧
### 快捷键
- `Tab`：自动补全
- `Enter`：重复上一条命令
- `Ctrl+L`：清屏
### 历史命令
```bash
(gdb) set history save on    # 保存历史命令
(gdb) set history filename ~/.gdb_history
```
### 调试正在运行的进程
```bash
gdb -p PID              # 附加到进程
gdb ./program PID       # 附加并加载符号表
```
## 7. 常见问题解决
### 没有调试信息
```bash
# 确保编译时使用 -g 参数
gcc -g -o program program.c
```
### 多线程调试
```bash
info threads            # 查看所有线程
thread 2                # 切换到线程2
thread apply all bt     # 所有线程的调用栈
```
