https://pdos.csail.mit.edu/6.S081/2021/tools.html
https://aistudio.google.com/prompts/1qB3grKfRgzqMr4nOu2kmhgf0N2YdK3sm
https://chat.qwen.ai/c/ea4c5f04-fb9f-463f-8bf3-1fa2acd4553f

```
sudo apt-get install git build-essential gdb-multiarch qemu-system-misc gcc-riscv64-linux-gnu binutils-riscv64-linux-gnu


git clone https://gitlab.com/qemu-project/qemu.git
cd qemu

git checkout v8.0.0

sudo apt update
sudo apt install -y ninja-build

sudo apt install -y meson

./configure --target-list=riscv64-softmmu --prefix=/usr/local

ninja -C build
sudo ninja -C build install

/usr/local/bin/qemu-system-riscv64 --version

```

# 001
以下是对你提供的《第一章：操作系统接口》内容的**系统化、条理清晰的笔记整理**，适合用于复习、教学或进一步学习。笔记结构包括：核心概念、关键机制、系统调用详解、代码示例解析和设计思想总结。

---

## 一、操作系统的核心职责

操作系统是计算机资源的管理者与抽象者，主要承担以下三大功能：

1. **资源共享与多路复用（Multiplexing）**  
   多个程序“同时”运行，实为CPU在进程间快速切换（分时调度），实现并发假象。

2. **硬件抽象（Abstraction）**  
   隐藏底层硬件差异（如硬盘类型），为应用程序提供统一、简洁的高级接口。

3. **可控交互（Controlled Interaction）**  
   提供安全机制，使多个程序可以共享数据并协同工作，同时防止相互干扰。

> ✅ **目标**：通过“机制 + 组合”设计出简单但通用的操作系统接口。

---

## 二、xv6 简介

- xv6 是一个教学用类 Unix 操作系统，基于 RISC-V 架构实现。
- 实现了 Ken Thompson 和 Dennis Ritchie 设计的原始 Unix 的基本接口与内核结构。
- 具有典型的**单体内核（monolithic kernel）**结构：
  - 内核是一个特权程序，负责管理所有资源。
  - 用户进程运行在用户空间，通过系统调用进入内核执行服务。
  
*进程与内核的关系：每个进程拥有独立内存空间，内核唯一且具有特权*

---

## 三、操作系统接口：系统调用

### 1. 什么是系统调用？
- 是用户程序请求操作系统服务的唯一方式。
- 调用时会触发从**用户态 → 内核态**的切换，由硬件支持（如中断/陷阱）。
- 内核完成任务后返回结果，控制权交还给用户程序。

### 2. xv6 的系统调用列表（图1.2）

| 系统调用 | 功能说明 |
|---------|--------|
| `fork()` | 创建子进程，复制父进程内存；返回值区分父子：父→PID，子→0 |
| `exit(status)` | 终止当前进程，释放资源，status 表示退出状态 |
| `wait(&status)` | 等待任意子进程结束，回收其资源，获取PID和退出码 |
| `kill(pid)` | 终止指定 PID 的进程 |
| `getpid()` | 获取当前进程的 PID |
| `sleep(n)` | 让进程休眠 n 个时钟周期 |
| `exec(file, argv)` | 加载并执行新程序，替换当前进程镜像，成功不返回 |
| `sbrk(n)` | 扩展堆空间 n 字节，返回新增内存起始地址 |
| `open(file, flags)` | 打开文件/设备，返回文件描述符 fd |
| `read(fd, buf, n)` | 从 fd 读取最多 n 字节到 buf，返回实际读取字节数 |
| `write(fd, buf, n)` | 向 fd 写入 n 字节，返回实际写入字节数 |
| `close(fd)` | 关闭文件描述符，释放资源 |
| `dup(fd)` | 复制一个文件描述符，共享同一偏移量 |
| `pipe(p[])` | 创建管道，p[0]为读端，p[1]为写端 |
| `chdir(dir)` | 改变当前工作目录 |
| `mkdir(dir)` | 创建目录 |
| `mknod(file, major, minor)` | 创建设备文件 |
| `fstat(fd, &st)` / `stat(file, &st)` | 获取文件元信息（类型、大小、链接数等） |
| `link(file1, file2)` | 为 file1 添加硬链接 file2 |
| `unlink(file)` | 删除文件名，引用计数归零时真正删除文件 |

> ⚠️ 默认规则：**成功返回0，失败返回-1**（少数例外如 `fork`, `getpid`, `wait` 返回非负整数）

---

## 四、核心抽象与机制

### 4.1 进程与内存管理（1.1）

#### 1. 进程（Process）
- 每个进程包含：
  - 用户空间内存：指令、数据、栈
  - 内核维护的状态：寄存器、PID、打开的文件等
- 特性：**隔离性**（各自内存）、**并发性**（分时共享 CPU）

#### 2. 关键系统调用
| 调用 | 作用 |
|------|------|
| `fork()` | 创建子进程（完全复制父进程内存） |
| `exec()` | 替换当前进程的内存映像（加载新程序） |
| `exit()` | 终止进程，释放资源 |
| `wait()` | 父进程等待子进程结束，避免僵尸进程 |

#### 3. fork-exec 模式
```c
if (fork() == 0) {
    exec("program", argv);  // 子进程执行新程序
} else {
    wait(0);                // 父进程等待
}
```
- 分离 `fork` 与 `exec` 的好处：
  - 允许在 `exec` 前修改子进程环境（如重定向 I/O）
  - 支持 shell 实现管道、重定向等功能

#### 4. 内存增长：`sbrk(n)`
- 动态扩展堆区（heap），用于 `malloc` 等操作
- xv6 自动处理初始内存分配（`fork` 复制，`exec` 加载 ELF 文件）

> 💡 **ELF 格式**：Executable and Linkable Format，定义可执行文件结构（代码段、数据段、入口地址等）

---

### 4.2 I/O 与文件描述符（1.2）

#### 1. 文件描述符（File Descriptor）
- 小整数（0, 1, 2...），代表内核中某个 I/O 对象（文件、管道、设备等）
- 抽象统一了不同资源的访问方式：都视为字节流

#### 2. 标准文件描述符约定
| fd | 名称 | 用途 |
|----|------|------|
| 0 | stdin | 标准输入 |
| 1 | stdout | 标准输出 |
| 2 | stderr | 标准错误 |

#### 3. read/write 的行为
- 从当前**文件偏移量**读写，自动递增偏移量
- `read()` 返回 0 表示 EOF，<0 表示错误
- `write()` 返回值应等于 n，否则表示写入失败

#### 4. 示例：cat 程序核心逻辑
```c
char buf[512];
int n;
while ((n = read(0, buf, sizeof(buf))) > 0)
    write(1, buf, n);
if (n < 0) fprintf(2, "read error\n");
```
- 不关心输入来源（文件/终端/管道），体现了抽象优势

#### 5. close 与 dup
- `close(fd)`：释放 fd，后续可用作新打开操作
- `dup(fd)`：创建新的 fd，指向相同底层对象，**共享偏移量**

> 🔁 **文件描述符分配规则**：总是使用最小未使用的整数（如关闭 0 后 open 会返回 0）

#### 6. I/O 重定向实现原理
```c
if (fork() == 0) {
    close(0);
    open("input.txt", O_RDONLY);  // 新打开的 fd=0
    exec("cat", argv);
}
```
- 利用 `fork` 复制文件表，`exec` 保留文件表
- 在子进程中调整标准输入，不影响父进程

#### 7. 共享偏移量现象
```c
if (fork() == 0) {
    write(1, "hello ", 6);
    exit(0);
} else {
    wait(0);
    write(1, "world\n", 6);
}
// 输出：hello world\n
```
- 子进程写完后偏移量前进，父进程接着写
- 适用于 `(echo hello; echo world) > out.txt`

#### 8. dup 应用：错误重定向
```bash
ls good bad > tmp 2>&1
```
- `2>&1` 表示将 stderr 复制到 stdout（即 `dup(1)`）
- 所有输出（正常+错误）都被重定向到同一文件

---

### 4.3 管道（Pipes）（1.3）

#### 1. 管道本质
- 内核中的小缓冲区，提供两个 fd：一个读、一个写
- 单向通信：写入一端，从另一端读出

#### 2. pipe() 系统调用
```c
int p[2];
pipe(p);  // p[0]=read end, p[1]=write end
```

#### 3. 使用管道连接两个进程
```c
int p[2]; pipe(p);
if (fork() == 0) {
    close(0); dup(p[0]); close(p[0]); close(p[1]);
    exec("wc", argv);
} else {
    close(p[0]);
    write(p[1], "hello\n", 6);
    close(p[1]);
    wait(0);
}
```
- 子进程将标准输入重定向到管道读端
- 父进程向管道写端写入数据

#### 4. 管道的阻塞特性
- `read()` 若无数据则阻塞，直到：
  - 有数据写入
  - 所有写端被关闭（此时返回 0，表示 EOF）
- 因此子进程必须关闭不必要的写端，否则 `wc` 永远不会看到 EOF

#### 5. Shell 中的管道实现
- 如 `grep fork sh.c | wc -l`
- shell 创建管道，分别 fork 左右命令，建立连接
- 形成进程树：内部节点等待子进程结束

#### 6. 管道 vs 临时文件
| 对比项 | 管道 | 临时文件 |
|--------|------|----------|
| 自动清理 | ✅ 是 | ❌ 需手动删除 |
| 数据长度限制 | 无（流式） | 受磁盘容量限制 |
| 并行性 | ✅ 可流水线执行 | ❌ 必须先写完再读 |
| 效率 | 更高（内核缓冲，阻塞同步） | 较低（需磁盘I/O） |

---

### 4.4 文件系统（1.4）

#### 1. 文件系统结构
- 层次化目录树，根目录 `/`
- 支持绝对路径 `/a/b/c` 和相对路径 `b/c`

#### 2. 目录与文件操作
| 系统调用 | 说明 |
|---------|------|
| `mkdir("/dir")` | 创建目录 |
| `open(..., O_CREATE)` | 创建并打开文件 |
| `mknod("/dev", major, minor)` | 创建设备文件 |

#### 3. Inode 与链接（Linking）
- **Inode**：文件的实际存储单元，包含元数据（size, type, nlink, 位置等）
- **硬链接（hard link）**：多个文件名指向同一个 inode
  ```c
  open("a", O_CREATE | O_WRONLY);
  link("a", "b");  // b 是 a 的硬链接
  ```
  - 修改 a 或 b 内容相同
  - `fstat` 显示相同 ino，nlink=2

#### 4. unlink 删除文件
- 仅删除一个文件名
- 当所有链接被删除且无打开 fd 时，才真正释放 inode 和磁盘空间

#### 5. 临时文件惯用法
```c
fd = open("/tmp/xyz", O_CREATE | O_RDWR);
unlink("/tmp/xyz");  // 删除名字，但仍可通过 fd 访问
// 进程退出时自动释放
```

#### 6. cd 为何在 shell 内实现？
- 若作为外部命令运行，则是在子进程中改变目录，不影响父 shell
- 因此必须由 shell 自身调用 `chdir()` 来修改自己的当前目录

---

## 五、设计哲学与现实意义（1.5）

### 1. Unix 设计思想
- **一切皆文件**：文件、设备、管道统一通过文件描述符访问
- **组合优于内置**：工具链（如 ls, grep, wc）通过管道组合，形成强大表达力
- **shell 是普通程序**：可替换、可扩展，推动脚本语言发展
### 2. xv6 的局限性
- 不符合 POSIX 标准（缺少 lseek、信号、网络等）
- 无用户权限系统（所有进程以 root 身份运行）
- 目标是**教学清晰性**而非工业实用性


---

## 六、练习建议（1.6）

### 练习题：双向管道通信性能测试

**目标**：使用两个管道让两个进程交换一个字节，测量每秒交换次数。

#### 思路步骤：
1. 创建两个管道：`pipe1`（A→B），`pipe2`（B→A）
2. `fork()` 创建子进程
3. 父进程：
   - 写 'X' 到 pipe1
   - 从 pipe2 读取回应 'Y'
4. 子进程：
   - 从 pipe1 读取 'X'
   - 写 'Y' 到 pipe2
5. 循环多次，统计总时间，计算吞吐量（交换次数/秒）

#### 提示：
- 使用 `gettimeofday()` 或 `clock_gettime()` 测时间
- 注意关闭不需要的 fd，避免死锁
- 可尝试不同循环次数取平均值

#### 示例伪代码框架：
```c
int p1[2], p2[2];
pipe(p1); pipe(p2);

if (fork() == 0) {
    // child
    close(p1[1]); close(p2[0]);
    for (int i = 0; i < N; i++) {
        read(p1[0], &c, 1);
        write(p2[1], &c, 1);
    }
    exit(0);
} else {
    // parent
    close(p1[0]); close(p2[1]);
    start_timer();
    for (int i = 0; i < N; i++) {
        write(p1[1], "X", 1);
        read(p2[0], &c, 1);
    }
    double t = stop_timer();
    printf("Rate: %.0f exchanges/sec\n", N / t);
    wait(0);
}
```
\