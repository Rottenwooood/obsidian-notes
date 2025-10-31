## 参考
- [模仿学习](https://huggingface.co/docs/lerobot/il_sim)
- [强化学习](https://huggingface.co/docs/lerobot/hilserl_sim)
## 需要的工具
- 一个好用的VPN
- 一个好用的AI工具（推荐qwen，gemini，grok）
- huggingface账号
- wandb账号（可选，用于实时观察训练过程）
- linux系统或者wsl
- 可选：使用手柄操控模拟环境中的机械臂
## 步骤
- 大概分为两部分：录制数据集和训练模型
	- 录制数据集需要在模拟环境录制或者使用真实机械臂（SO101）录制
	- 训练模型需要数据集（自行录制或使用huggingface上公开的对应格式的数据集）以及GPU（本地设备或者Colab或者租用云算力）
- 命令的含义可复制问ai
- 以下步骤在linux或者wsl中运行，windows中未经测试
1. 下载仓库.zip/用git clone仓库
![[Pasted image 20250816181145.png]]
Download ZIP并解压在合适的目录
或者
```
git clone https://github.com/huggingface/lerobot.git
```
2. 安装miniconda
下载安装脚本
```
cd ~
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
```
安装miniconda
```
bash Miniconda3-latest-Linux-x86_64.sh
```
（回车，Yes，回车，Yes）
```
source ~/.bashrc   # 如果你用的是 bash
```
重启终端（命令行）
3. 配置lerobot环境
```
conda create -y -n lerobot python=3.10
conda activate lerobot
# 此后所有命令行操作都要在lerobot环境中运行
# 进入方法：打开终端后 conda activate lerobot
conda install ffmpeg -c conda-forge
```
安装lerobot
```
cd 你clone的或者下载的仓库的路径/lerobot
例如： cd /home/user/lerobot
pip install -e .
```
安装`gym_hil`
```
pip install -e ".[hilserl]"
# 如果你没有配置vpn，则执行下边这一行代替
pip install -e ".[hilserl]" -i https://pypi.tuna.tsinghua.edu.cn/simple/
```

### 模仿学习
下载配置文件(或直接从这里复制)
- 命名为env_config_gym_hil_il.json
- 放在lerobot/src/lerobot/configs/il_sim/
- 需手动配置项（具体作用可问ai）
	- control_mode
	- repo_id（每次执行以下运行仿真环境的命令都得更改一次,或者删掉'/home/c6h4o2/.cache/huggingface/lerobot/{repo_id}'）
	- task
	- pretrained_policy_name_or_path
	- num_episodes
	- device
	- push_to_hub
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
    "repo_id": "huggingface的用户名/lxc_test_sim", //仓库名称
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
补丁
```
# 每次重新打开终端都重新运行
export LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libstdc++.so.6
export MUJOCO_GL=osmesa
export HF_ENDPOINT=https://hf-mirror.com
# 如果提示缺少下边这个包，则运行
sudo apt install speech-dispatcher
```
运行仿真
```
python -m lerobot.scripts.rl.gym_manipulator --config_path src/lerobot/configs/il_sim/env_config_gym_hil_il.json
```
操控方式
```
  Arrow keys: Move in X-Y plane
  Shift and Shift_R: Move in Z axis
  Right Ctrl and Left Ctrl: Open and close gripper
  ESC: Exit
```
如果提示RuntimeError: No CUDA GPUs are available
- 可能你没有显卡（把device:cuda改成device:cpu）
- 可能你没有安装过显卡驱动（这个比较复杂,选择安装显卡驱动或者把device:cuda改成device:cpu）
- 可能是显卡掉了，重启电脑解决
录制数据集：
模仿学习是学习复刻数据集中的动作，因此要录制数据集，可以录制多个（episode的个数）抓取立方体并放下的例子
训练ACT模型
- 需求：使用A100训练需要1小时
- 如果本地有性能比较好的显卡，可以尝试训练，可能需要十个小时左右
- 如果没有性能比较好的显卡（如笔记本），可以尝试使用Colab进行训练
本地训练指令
- 需确保数据集放在了~/.cache/huggingface/lerobot/
```
python lerobot/scripts/train.py \
  --dataset.repo_id=todateman/il_gym0 \
  --policy.type=act \
  --policy.repo_id=zbkc6h4o2/il_sim_test0 \
  --output_dir=/kaggle/outputs/train/il_sim_test0 \
  --job_name=il_sim_test \
  --policy.device=cuda \
  --wandb.enable=true
```
Colab训练
https://colab.research.google.com/github/huggingface/notebooks/blob/main/lerobot/training-act.ipynb
- 依次运行每个单元格
- 运行训练ACT模型的单元格，其中`--dataset.repo_id=`后边需要改成自己的数据集（先前设置的repo_id），并且上传到huggingface上边
- 训练预计需要几个小时，期间不要关闭界面
评估Policy
- 需手工配置项：
	- "pretrained_policy_name_or_path": /home/c6h4o2/EVAL/checkpoint_step_060000/pretrained_model",
	- 将Colab上边的checkpoints放在此路径
	- "dataset": {
	        "repo_id": "todateman/il_gym0",
	- "control_mode": "keyboard"        
	- "task": "PandaPickCubeKeyboard-v0",
```
{
    "output_dir": null,
    "job_name": "default",
    "resume": false,
    "seed": 1000,
    "num_workers": 4,
    "batch_size": 256,
    "steps": 100000,
    "log_freq": 500,
    "save_checkpoint": true,
    "save_freq": 2000000,
    "wandb": {
        "enable": true,
        "project": "so100_real",
        "disable_artifact": true
    },
    "dataset": {
        "repo_id": "todateman/il_gym0",
        "use_imagenet_stats": false
    },
    "policy": {
        "type": "act",
        "n_obs_steps": 1,
        "normalization_mapping": {
            "VISUAL": "MEAN_STD",
            "STATE": "MEAN_STD",
            "ACTION": "MEAN_STD"
        },
        "input_features": {
            "observation.state": {
                "type": "STATE",
                "shape": [
                    18
                ]
            },
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
            }
        },
        "output_features": {
            "action": {
                "type": "ACTION",
                "shape": [
                    4
                ]
            }
        },
        "device": "cpu",
        "use_amp": false,
        "chunk_size": 100,
        "n_action_steps": 100,
        "vision_backbone": "resnet18",
        "pretrained_backbone_weights": "ResNet18_Weights.IMAGENET1K_V1",
        "replace_final_stride_with_dilation": false,
        "pre_norm": false,
        "dim_model": 512,
        "n_heads": 8,
        "dim_feedforward": 3200,
        "feedforward_activation": "relu",
        "n_encoder_layers": 4,
        "n_decoder_layers": 1,
        "use_vae": true,
        "latent_dim": 32,
        "n_vae_encoder_layers": 4,
        "temporal_ensemble_coeff": null,
        "dropout": 0.1,
        "kl_weight": 10.0,
        "optimizer_lr": 1e-05,
        "optimizer_weight_decay": 0.0001,
        "optimizer_lr_backbone": 1e-05
    },
    "env": {
        "type": "hil",
        "wrapper": {
            "gripper_penalty": 0,
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
            "control_mode": "keyboard"
        },
        "name": "franka_sim",
        "mode": "eval",
        "repo_id": null,
        "dataset_root": null,
        "task": "PandaPickCubeKeyboard-v0",
        "num_episodes": 30,
        "episode": 0,
        "pretrained_policy_name_or_path": "/home/c6h4o2/EVAL/checkpoint_step_060000/pretrained_model",
        "device": "cpu",
        "push_to_hub": false,
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
                    3
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
}



```

```
python -m lerobot.scripts.rl.eval_policy --config_path=src/lerobot/configs/eval_config_gym_hil.json
```
### 强化学习
补丁
```
# 每次重新打开终端都重新运行
export LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libstdc++.so.6
export MUJOCO_GL=osmesa
export HF_ENDPOINT=https://hf-mirror.com
# 如果提示缺少下边这个包，则运行
sudo apt install speech-dispatcher
```
下载配置文件(或直接从这里复制)
- 可配置项
    - "steps": 100000,
    - "save_freq": 2000,
	- "wandb": {
        "enable": false,
	- "dataset": {
        "repo_id": "todateman/il_gym0",
	- "policy": {    
        "repo_id": "zbkc6h4o2/hil_serl_model",
    - "repo_id": "zbkc6h4o2/hil_serl_test1",
    - "task": "PandaPickCubeKeyboard-v0",
    - "control_mode": "keyboard"
```
{    
    "output_dir": null,
    "job_name": "default",
    "resume": false,
    "seed": 1000,
    "num_workers": 4,
    "batch_size": 256,
    "steps": 100000,
    "log_freq": 1,
    "save_checkpoint": true,
    "save_freq": 2000,
    "wandb": {
        "enable": false,
        "project": "franka_sim",
        "disable_artifact": true
    },
    "dataset": {
        "repo_id": "todateman/il_gym0",
        "use_imagenet_stats": false
    },
    "policy": {
        "type": "sac",
        "n_obs_steps": 1,
        "repo_id": "zbkc6h4o2/hil_serl_model",
        "normalization_mapping": {
            "VISUAL": "MEAN_STD",
            "STATE": "MIN_MAX",
            "ENV": "MIN_MAX",
            "ACTION": "MIN_MAX"
        },
        "input_features": {
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
            }
        },
        "output_features": {
            "action": {
                "type": "ACTION",
                "shape": [
                    3
                ]
            }
        },
        "device": "cuda",
        "use_amp": false,
        "dataset_stats": {
            "observation.images.front": {
                "mean": [
                    0.485,
                    0.456,
                    0.406
                ],
                "std": [
                    0.229,
                    0.224,
                    0.225
                ]
            },
            "observation.images.wrist": {
                "mean": [
                    0.485,
                    0.456,
                    0.406
                ],
                "std": [
                    0.229,
                    0.224,
                    0.225
                ]
            },
            "observation.state": {
                "min": [
                    -0.6897139549255371,
                    -1.1421763896942139,
                    -0.5745007991790771,
                    -2.97829008102417,
                    -0.2710767090320587,
                    1.3246592283248901,
                    -0.04057434946298599,
                    -0.21261805295944214,
                    -0.4548068344593048,
                    -0.6540042757987976,
                    -0.3644964098930359,
                    -1.1057522296905518,
                    -0.40768879652023315,
                    -0.2220114767551422,
                    0.0,
                    0.19176171720027924,
                    -0.3013063669204712,
                    0.00362197193317115
                ],
                "max": [
                    0.5107022523880005,
                    0.5516204237937927,
                    0.5620884299278259,
                    -1.3330878019332886,
                    0.32758936285972595,
                    3.119610548019409,
                    1.8364211320877075,
                    0.25358933210372925,
                    0.36316126585006714,
                    0.14765967428684235,
                    0.49947625398635864,
                    0.144814133644104,
                    0.2820609211921692,
                    0.7382049560546875,
                    255.0,
                    0.6012658476829529,
                    0.3005995750427246,
                    0.5004003643989563
                ]
            }
        },
        "storage_device": "cpu",
        "vision_encoder_name": "helper2424/resnet10",
        "freeze_vision_encoder": true,
        "image_encoder_hidden_dim": 32,
        "shared_encoder": true,
        "online_steps": 1000000,
        "online_env_seed": 10000,
        "online_buffer_capacity": 100000,
        "offline_buffer_capacity": 100000,
        "online_step_before_learning": 100,
        "policy_update_freq": 1,
        "discount": 0.97,
        "temperature_init": 0.01,
        "num_critics": 2,
        "num_subsample_critics": null,
        "critic_lr": 0.0003,
        "actor_lr": 0.0003,
        "temperature_lr": 0.0003,
        "critic_target_update_weight": 0.005,
        "utd_ratio": 2,
        "state_encoder_hidden_dim": 256,
        "latent_dim": 64,
        "target_entropy": null,
        "use_backup_entropy": true,
        "grad_clip_norm": 10.0,
        "num_discrete_actions": 3,
        "critic_network_kwargs": {
            "hidden_dims": [
                256,
                256
            ],
            "activate_final": true,
            "final_activation": null
        },
        "actor_network_kwargs": {
            "hidden_dims": [
                256,
                256
            ],
            "activate_final": true
        },
        "policy_kwargs": {
            "use_tanh_squash": true,
            "std_min": 1e-5,
            "std_max": 5,
            "init_final": 0.05
        },
        "actor_learner_config": {
            "learner_host": "127.0.0.1",
            "learner_port": 50051,
            "policy_parameters_push_frequency": 4
        },
        "concurrency": {
            "actor": "threads",
            "learner": "threads"
        }
    },
    "env": {
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
        "control_mode": "keyboard"
    },
    "name": "franka_sim",
    "mode": null,
    "repo_id": "zbkc6h4o2/hil_serl_test1",
    "dataset_root": null,
    "task": "PandaPickCubeKeyboard-v0",
    "num_episodes": 30,
    "episode": 0,
    "pretrained_policy_name_or_path": null,
    "device": "cuda",
    "push_to_hub": false,
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
                3
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
}
```

- 命名为train_gym_hil_env.json
- 放在lerobot/src/lerobot/configs/
```
# 分别开两个终端运行
python -m lerobot.scripts.rl.actor --config_path src/lerobot/configs/train_gym_hil_env.json

python -m lerobot.scripts.rl.learner --config_path src/lerobot/configs/train_gym_hil_env.json
```