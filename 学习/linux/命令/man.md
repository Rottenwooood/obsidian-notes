```Terminal
(base) c6h4o2@DESKTOP-HHJ65BM:~$ man
What manual page do you want?
For example, try 'man man'.
(base) c6h4o2@DESKTOP-HHJ65BM:~$ man man
man: can't resolve man7/groff_man.7
(base) c6h4o2@DESKTOP-HHJ65BM:~$ man -k printf
printf (1)           - format and print data
(base) c6h4o2@DESKTOP-HHJ65BM:~$ man 1 printf
```
## 基本用法
```bash
man command_name
```
例如：
```bash
man ls
```
---
## 常用操作（在 man 页面中使用）

| 操作 | 说明 |
|------|------|
| `空格键` | 向下翻一页 |
| `b`    | 向上翻一页 |
| `回车键` | 向下滚动一行 |
| `/keyword` | 向下搜索关键词 |
| `n` | 继续向下查找下一个匹配项（配合 `/` 使用） |
| `N` | 向上查找下一个匹配项 |
| `q` | 退出 man 页面 |

---
## 其他用法

### 查看特定章节的内容

有些名称可能在多个章节中有解释，可以通过指定章节来查看：
```bash
man section_number command_name
```
例如：
```bash
man 3 printf   # 查看 C 函数 printf 的帮助（第3章）
```
---
### 常见手册章节简介

| 章节 | 内容类型 |
|------|----------|
| 1 | 用户命令（普通命令） |
| 2 | 系统调用 |
| 3 | C 库函数 |
| 4 | 特殊文件（如设备文件） |
| 5 | 文件格式和约定 |
| 6 | 游戏 |
| 7 | 杂项（宏包、规范等） |
| 8 | 系统管理命令 |

---
### 显示所有可用的 man 页面
```bash
man -k keyword
```
例如：
```bash
man -k network
```
---
### 显示命令的所有 man 页面（不同章节）
```bash
man -a command_name
```
