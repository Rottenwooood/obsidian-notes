```
python -m lerobot.scripts.rl.gym_manipulator --config_path path/to/your/env_config.json
```

```
python -m lerobot.scripts.find_joint_limits \
    --robot.type=so100_follower_end_effector \
    --robot.port=/dev/ttyACM0 \
    --robot.id=black \
    --teleop.type=gamepad \
    --teleop.id=blue

```

```
{
    "type": "gym_manipulator",
    "robot": {
        "type": "so100_follower_end_effector",
        "port": "/dev/ttyACM0",
        "urdf_path": "lerobot/src/lerobot/model/so101_new_calib.urdf",
        "target_frame_name": "gripper_frame_link",
        "cameras": {
            "front": {
                "type": "opencv",
                "index_or_path": 0,
                "height": 720,
                "width": 1280,
                "fps": 30
            },
            "wrist": {
                "type": "opencv",
                "index_or_path": 1,
                "height": 720,
                "width": 1280,
                "fps": 30
            }
        },
        "end_effector_bounds": {
            "min": [
                0.24, 
                0.20, 
                0.10
            ],
            "max": [
                0.16,
                -0.08, 
                0.03
            ]
        },
        "end_effector_step_sizes": {
            "x": 0.025,
            "y": 0.025,
            "z": 0.025
        }
    },
    "teleop": {
        "type": "joycon",
        "use_gripper": true
    },
    "wrapper": {
        "display_cameras": false,
        "add_joint_velocity_to_observation": true,
        "add_current_to_observation": true,
        "add_ee_pose_to_observation": true,
        "crop_params_dict": null,
        "resize_size": [
            128,
            128
        ],
        "control_time_s": 20.0,
        "use_gripper": true,
        "gripper_quantization_threshold": null,
        "gripper_penalty": -0.02,
        "gripper_penalty_in_reward": false,
        "fixed_reset_joint_positions": [
            0.0,
            0.0,
            0.0,
            90.0,
            0.0,
            5.0
        ],
        "reset_time_s": 2.5,
        "control_mode": "joycon"
    },
    "name": "real_robot",
    "mode": "record",
    "repo_id": "ZBKC6H4O2/so101_real_test",
    "dataset_root": null,
    "task": "pick_and_lift",
    "num_episodes": 2,
    "episode": 0,
    "pretrained_policy_name_or_path": null,
    "device": "cuda",
    "push_to_hub": true,
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
                15
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
        "observation.images.front": "observation.images.side",
        "observation.images.wrist": "observation.images.wrist",
        "observation.state": "observation.state",
        "action": "action"
    },
    "reward_classifier_pretrained_path": null
}
```

```
lerobot-find-camera
python /home/yukun/projects/lerobot/tests/cameras/test.py
```

```
python -m lerobot.scripts.rl.gym_manipulator --config_path lerobot/src/lerobot/configs/env_config_so100.json
```

键盘按 `s` 表示成功，按 `esc` 表示失败。在在线训练过程中，按 `space` 接管策略，再按 `space` 将控制权交还给策略
shift


```
INFO:root:Keyboard control wrapper initialized with provided teleop_device.
Keyboard controls:
  Arrow keys: Move in X-Y plane
  Shift and Shift_R: Move in Z axis
  Right Ctrl and Left Ctrl: Open and close gripper
  f: End episode with FAILURE
  s: End episode with SUCCESS
  r: End episode with RERECORD
  i: Start/Stop Intervention
```

```
python -m lerobot.scripts.rl.gym_manipulator --config_path lerobot/src/lerobot/configs/env_config_so100.json

```

```
python -m lerobot.scripts.visualize_dataset \
--repo-id ZBKC6H4O2/so101_real_test034 \
--episode-index 0
```

```
sudo git clone https://github.com/paroj/xpad.git /usr/src/xpad-0.4
sudo dkms install -m xpad -v 0.4
sudo git clone https://github.com/paroj/xpad.git /usr/src/xpad-0.4
sudo dkms install -m xpad -v 0.4
lsmod | grep xpad
dmesg | tail -10
```

```
python -m lerobot.scripts.rl.crop_dataset_roi --repo-id ZBKC6H4O2/so101_real_test034
```

```
lerobot-train --config_path lerobot/src/lerobot/configs/reward_classifier_train_config.json
```

```
python -m lerobot.scripts.rl.learner --config_path lerobot/src/lerobot/configs/train_config_hilserl_so100.json
python -m lerobot.scripts.rl.actor --config_path lerobot/src/lerobot/configs/train_config_hilserl_so100.json

```

```
lsof -i :50051
```

```
sudo chmod 666 /dev/ttyACM0

```

```
scp -r /home/c6h4o2/.cache/huggingface/lerobot/ZBKC6H4O2/so101_hil_serl_move_doll001_cropped_resized henu@10.95.16.55:/home/henu/.cache/huggingface/lerobot/ZBKC6H4O2/so101_hil_serl_move_doll001_cropped_resized
```