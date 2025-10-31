### 简单版本
```
# 编译器
CC = gcc

# 编译选项
CFLAGS = -Wall -g

# 目标文件
TARGET = myprogram

# 源文件
SOURCES = main.c utils.c

# 生成目标
$(TARGET): $(SOURCES)
    $(CC) $(CFLAGS) -o $(TARGET) $(SOURCES)

# 清理生成文件
clean:
    rm -f $(TARGET)

# 伪目标
.PHONY: clean
```
	对于.c文件
	对于.cpp:g++
### 复杂版本
```
# 变量定义
CC = gcc
CFLAGS = -Wall -Wextra -std=c99 -g
TARGET = myprogram

# 自动获取源文件
SOURCES = $(wildcard *.c)
OBJECTS = $(SOURCES:.c=.o)

# 默认目标
all: $(TARGET)

# 链接目标文件
$(TARGET): $(OBJECTS)
    $(CC) $(OBJECTS) -o $(TARGET)

# 编译源文件为目标文件
%.o: %.c
    $(CC) $(CFLAGS) -c $< -o $@

# 清理
clean:
    rm -f $(OBJECTS) $(TARGET)

# 重新编译
rebuild: clean all

# 伪目标声明
.PHONY: all clean rebuild
```
---
```
make           # 编译程序
make clean     # 清理生成文件
make rebuild   # 重新编译
```
### 常用 Makefile 语法说明

- `$@` - 目标文件名
- `$<` - 第一个依赖文件名
- `$^` - 所有依赖文件名
- `%.o: %.c` - 模式规则
- `-include` - 包含文件（即使不存在也不报错）
### 小贴士
1. **缩进必须使用 Tab** ，不是空格
2. **变量使用 `=` 或 `:=`** 定义
3. **`.PHONY`** 声明伪目标
4. **`-Wall`** 开启所有警告
5. **`-g`** 包含调试信息