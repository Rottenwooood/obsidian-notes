## 命令
```terminal
# 安装
wsl --update
wsl --install --web-download
# 
wsl --list --online
wsl --install kali-linux --web-download
# 
wsl --list -v
wsl --set-default kali-linux
# 
wsl -d ubuntu
exit
# 
wsl --unresgister ubuntu
# 迁移
wsl --export 发行版名称 E:\ubuntu.tar
wsl --import 新名称 E:\wsl_root\ubuntu_new E:\ubuntu.tar --version 2

```
## 特色
- 文件共享
- 显卡直通
- 命令混合