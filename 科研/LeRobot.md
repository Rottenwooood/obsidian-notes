https://huggingface.co/docs/lerobot/installation
## gym_hil
```
pip install -e ".[hilserl]" -i https://pypi.tuna.tsinghua.edu.cn/simple/
```
https://huggingface.co/datasets/aractingi/lerobot-example-config-files/tree/main
env_config_gym_hil_il.json
放在src\lerobot\configs\il_sim\env_config_gym_hil_il.json
```
{
    "type": "hil",
    "wrapper": {
        "gripper_penalty": -0.02,
        "display_cameras": false,
        "add_joint_velocity_to_observation": true,
        "add_ee_pose_to_observation": true,
        "crop_params_dict": {
            "observation.images.front": [
                0,
                0,
                128,
                128
            ],
            "observation.images.wrist": [
                0,
                0,
                128,
                128
            ]
        },
        "resize_size": [
            128,
            128
        ],
        "control_time_s": 15.0,
        "use_gripper": true,
        "fixed_reset_joint_positions": [
            0.0,
            0.195,
            0.0,
            -2.43,
            0.0,
            2.62,
            0.785
        ],
        "reset_time_s": 2.0,
        "control_mode": "keyboard" // gamepad
    },
    "name": "franka_sim",
    "mode": "record",
    "repo_id": "lxc_test_sim", //仓库名称
    "dataset_root": null,
    "task": "PandaPickCubeKeyboard-v0", //键盘或手柄
    "num_episodes": 30, //数量
    "episode": 0,
    "pretrained_policy_name_or_path": null,
    "device": "cuda", //训练设备 cpu/cuda/mps
    "push_to_hub": false, //上传到huggingface
    "fps": 10,
    "features": {
        "observation.images.front": {
            "type": "VISUAL",
            "shape": [
                3,
                128,
                128
            ]
        },
        "observation.images.wrist": {
            "type": "VISUAL",
            "shape": [
                3,
                128,
                128
            ]
        },
        "observation.state": {
            "type": "STATE",
            "shape": [
                18
            ]
        },
        "action": {
            "type": "ACTION",
            "shape": [
                4
            ]
        }
    },
    "features_map": {
        "observation.images.front": "observation.images.front",
        "observation.images.wrist": "observation.images.wrist",
        "observation.state": "observation.state",
        "action": "action"
    },
    "reward_classifier_pretrained_path": null
}

```

```
python -m lerobot.scripts.rl.gym_manipulator --config_path src/lerobot/configs/il_sim/env_config_gym_hil_il.json

python -m lerobot.scripts.rl.gym_manipulator --config_path src/lerobot/configs/il_sim/env_config_gym_hil_il.json

python src\lerobot\scripts\rl\gym_manipulator.py --config_path lerobot/configs/il_sim/env_config_gym_hil_il.json
```
## wandb
```
wandb login
```
在配置文件中启用

RT-2
pai0
helix

```
conda install ffmpeg -c conda-forge
export LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libstdc++.so.6
export MUJOCO_GL=osmesa
sudo apt install speech-dispatcher
python -m lerobot.scripts.rl.gym_manipulator --config_path src/lerobot/configs/il_sim/env_config_gym_hil_il.json
```
aria2c -x 16 -s 16 https://utexas.box.com/shared/static/04k94hyizn4huhbv5sz4ev9p2h1p6s7f.zip 
https://utexas.box.com/shared/static/avkklgeq0e1dgzxz52x488whpu8mgspk.zip https://utexas.box.com/shared/static/avkklgeq0e1dgzxz52x488whpu8mgspk.zip https://utexas.box.com/shared/static/cv73j8zschq8auh9npzt876fdc1akvmk.zip
wget https://utexas.box.com/shared/static/04k94hyizn4huhbv5sz4ev9p2h1p6s7f.zip # LIBERO-Spatial
wget https://utexas.box.com/shared/static/avkklgeq0e1dgzxz52x488whpu8mgspk.zip # LIBERO-Object
wget https://utexas.box.com/shared/static/avkklgeq0e1dgzxz52x488whpu8mgspk.zip # LIBERO-Goal
wget https://utexas.box.com/shared/static/cv73j8zschq8auh9npzt876fdc1akvmk.zip # LIBERO-100, including 90 and 10