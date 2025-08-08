- wsl中安装
	- wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh 
	- bash Miniconda3-latest-Linux-x86_64.sh
	- yes，yes，ENTER
-  如果你不希望每次启动都进入 Conda base 环境（但仍想使用 Conda）：
	- 运行以下命令来禁用自动激活 base 环境：
	- conda config --set auto_activate_base false
	- 这样你就保留了使用 Conda 的能力，但不会每次都进入 (base) 环境。 
python -m lerobot.scripts.visualize_dataset     --repo-id lerobot/pusht     --root ./my_local_data_dir    --local-files-only 1     --episode-index 0

```
python -m lerobot.scripts.visualize_dataset --repo-id lerobot/pusht --episode-index 0 --root ~/lerobot/datasets/pusht/
```
pip install -e . -i https://pypi.tuna.tsinghua.edu.cn/simple/
```
conda create -n 环境名 -y
# -y 表示同意
conda activate 环境名

```