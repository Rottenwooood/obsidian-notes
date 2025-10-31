```
conda create -y -n xlerobot python=3.10
conda activate xlerobot
pip install mani-skill
```

```
# export HF_ENDPOINT=https://hf-mirror.com
python -m mani_skill.utils.download_asset "ReplicaCAD"
```
[另一种下载方式](https://drive.google.com/file/d/1mqImztNX1LYZFBzt9z895C814RsyGe4N/view?usp=sharing)
~/.maniskill/data/scene_datasets/replica_cad_dataset

```
# 测试是否安装成功
python -m mani_skill.examples.demo_random_action -e "ReplicaCAD_SceneManipulation-v1" \\
  --render-mode="human" --shader="rt-fast"
```

```
pip install pygame
pip install rerun-sdk
```

```
cd ~/miniconda3/envs/lerobot/lib/python3.10/site-packages/mani_skill
```
替换三个文件/文件夹：
/agents, /assets, and /envs/scenes/base_env.py

	可选：去掉光追功能：shader=”rt-fast” to “default”
修复opengl的bug
```
export LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libstdc++.so.6
export MUJOCO_GL=osmesa
```

```
python -m mani_skill.examples.demo_ctrl_action -e "ReplicaCAD_SceneManipulation-v1"   --render-mode="human" --shader="rt-fast" -c "pd_joint_delta_pos_dual_arm"
```

```
python -m mani_skill.examples.demo_ctrl_action_ee_cam_rerun -e "ReplicaCAD_SceneManipulation-v1"   --render-mode="human" --shader="rt-fast" -c "pd_joint_delta_pos_dual_arm"
```