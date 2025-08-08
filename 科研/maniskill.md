## 链接
https://www.maniskill.ai/
https://colab.research.google.com/github/haosulab/ManiSkill/blob/main/examples/tutorials/1_quickstart.ipynb
https://maniskill.readthedocs.io/en/latest/
https://github.com/haosulab/ManiSkill
https://www.google.com/url?q=https%3A%2F%2Fgymnasium.farama.org%2F
[构建自定义任务教程](https://www.google.com/url?q=https%3A%2F%2Fmaniskill.readthedocs.io%2Fen%2Flatest%2Fuser_guide%2Ftutorials%2Fcustom_tasks%2Fintro.html)
[Controller]([https://maniskill.readthedocs.io/en/latest/user_guide/concepts/controllers.html](https://maniskill.readthedocs.io/en/latest/user_guide/concepts/controllers.html))
[远程操作](https://maniskill.readthedocs.io/en/latest/user_guide/data_collection/teleoperation.html#click-drag-system)
https://github.com/vwxyzjn/cleanrl/
https://benchmark.cleanrl.dev/
[robot](https://www.google.com/url?q=https%3A%2F%2Fmaniskill.readthedocs.io%2Fen%2Flatest%2Frobots%2Findex.html)
## 安装
```
# setup vulkan
!mkdir -p /usr/share/vulkan/icd.d
!wget -q https://raw.githubusercontent.com/haosulab/ManiSkill/main/docker/nvidia_icd.json
!wget -q https://raw.githubusercontent.com/haosulab/ManiSkill/main/docker/10_nvidia.json
!mv nvidia_icd.json /usr/share/vulkan/icd.d
!mv 10_nvidia.json /usr/share/glvnd/egl_vendor.d/10_nvidia.json
!apt-get install -y --no-install-recommends libvulkan-dev
# dependencies
!pip install --upgrade mani_skill tyro
```
## CPU模拟
```python
# Import required packages
import gymnasium as gym
import mani_skill.envs
import time
env = gym.make("PegInsertionSide-v1")
obs, _ = env.reset(seed=0)
env.unwrapped.print_sim_details() # print verbose details about the configuration
done = False
start_time = time.time()
while not done:
    obs, rew, terminated, truncated, info = env.step(env.action_space.sample())
    done = terminated or truncated
N = info["elapsed_steps"].item()
dt = time.time() - start_time
FPS = N / (dt)
print(f"Frames Per Second = {N} / {dt} = {FPS}")
```

## 渲染
要渲染其外观，可以使用 `env.render`。请注意，我们之前将 render_mode 设置为 `"rgb_array"`，这遵循 Gymnasium API 并配置了 `env.render` 返回的内容。
- 如果 render_mode 设置为 `"sensors"`，则返回一个 rgb 数组，显示如果你选择基于视觉的观察模式时实际接收到的视觉数据。
- 如果 render 模式设置为 `all`，则结合 `"rgb_array"` 和 `"sensors"` 模式。
- 如果 render 模式设置为 `"human"`（默认值），则打开一个 GUI 查看器，你可以使用它来与环境交互（需要显示器）。
下面的示例展示了一个非常简单的环境渲染。在构建环境时，ManiSkill 支持各种渲染选项（如修改光照、阴影、纹理、环境贴图等）。
导出一张仿真环境渲染图
```python
import matplotlib.pyplot as plt
env = gym.make("PegInsertionSide-v1", render_mode="rgb_array")
env.reset()
plt.imshow(env.render()[0].cpu().numpy()) # we take [0].numpy() as everything is a batched tensor
```
## GPU模拟State_only
运行 GPU 模拟非常简单，只需将 num_envs 参数设置为大于 1 的数值即可。在 Colab 的免费套餐（T4 GPU）上，你应该能够运行多达 2048 个并行的 PickCube-v1 任务环境，并达到约 20,000 FPS。你也可以运行多个环境，这对于保持训练和评估环境对 ML 工作流程很有用。请注意，模拟 FPS 很大程度上取决于模拟和控制频率、场景中的物体等因素，因此你自己的环境的 FPS 可能会有很大的差异。

在 GPU 并行化模拟中，ManiSkill 中的几乎所有内容默认都是批量处理并以 torch 张量形式存在。此外，由于 ManiSkill 基于底层 SAPIEN 引擎的结构，大多数创建的任务都可以在 CPU 和 GPU 上模拟。
```python
### Make sure to restart the notebook if you already ran a CPU sim!! ###
# Import required packages
import gymnasium as gym
import mani_skill.envs
import torch
import time
num_envs = 2048 # you can go up to 4096 on better GPUs
env = gym.make("PickCube-v1", num_envs=num_envs)
env.unwrapped.print_sim_details()
obs, _ = env.reset(seed=0)
done = False
start_time = time.time()
total_rew = 0
while not done:
    # note that env.action_space is now a batched action space
    obs, rew, terminated, truncated, info = env.step(torch.from_numpy(env.action_space.sample()))
    done = (terminated | truncated).any() # stop if any environment terminates/truncates
N = num_envs * info["elapsed_steps"][0].item()
dt = time.time() - start_time
FPS = N / (dt)
print(f"Frames Per Second = {N} / {dt} = {FPS}")
```
输出：
```
# -------------------------------------------------------------------------- #
Task ID: PickCube-v1, 2048 parallel environments, sim_backend=**gpu**
obs_mode=**state**, control_mode=pd_joint_delta_pos
render_mode=None, sensor_details=RGBD(128x128)
sim_freq=100, control_freq=20
observation space: Box(-inf, inf, (2048, 42), float32)
(single) action space: Box(-1.0, 1.0, (8,), float32)
# -------------------------------------------------------------------------- #
Frames Per Second = 65536 / 2.6711995601654053 = 24534.295743872426
```
## GPU模拟State_Visual
在 Colab 的免费套餐（T4 GPU）上，你应该能够运行 512 个 PickCube-v1 任务的并行环境，并在采样随机动作时达到大约 3500FPS。
```python
# Import required packages
import gymnasium as gym
import mani_skill.envs
import torch
import time
num_envs = 512 # you can go up higher on better GPUs, this is mostly memory constrained
env = gym.make("PickCube-v1", num_envs=num_envs, obs_mode="rgbd")
env.unwrapped.print_sim_details()
obs, _ = env.reset(seed=0)
done = False
start_time = time.time()
total_rew = 0
while not done:
    # note that env.action_space is now a batched action space
    obs, rew, terminated, truncated, info = env.step(torch.from_numpy(env.action_space.sample()))
    done = (terminated | truncated).any() # stop if any environment terminates/truncates
N = num_envs * info["elapsed_steps"][0].item()
dt = time.time() - start_time
FPS = N / (dt)
print(f"Frames Per Second = {N} / {dt} = {FPS}")
```
	更改：obs_mode="rgbd"
输出：
```
#-------------------------------------------------------------------------- #
Task ID: PickCube-v1, 512 parallel environments, sim_backend=gpu
obs_mode=rgbd, control_mode=pd_joint_delta_pos
render_mode=None, sensor_details=RGBD(128x128)
sim_freq=100, control_freq=20
observation space: Dict('agent': Dict('qpos': Box(-inf, inf, (512, 9), float32), 'qvel': Box(-inf, inf, (512, 9), float32)), 'extra': Dict('is_grasped': Box(False, True, (512,), bool), 'tcp_pose': Box(-inf, inf, (512, 7), float32), 'goal_pos': Box(-inf, inf, (512, 3), float32)), 'sensor_param': Dict('base_camera': Dict('extrinsic_cv': Box(-inf, inf, (512, 3, 4), float32), 'cam2world_gl': Box(-inf, inf, (512, 4, 4), float32), 'intrinsic_cv': Box(-inf, inf, (512, 3, 3), float32))), 'sensor_data': Dict('base_camera': Dict('depth': Box(-32768, 32767, (512, 128, 128, 1), int16), 'rgb': Box(0, 255, (512, 128, 128, 3), uint8))))
(single) action space: Box(-1.0, 1.0, (8,), float32)
# -------------------------------------------------------------------------- #
Frames Per Second = 4096 / 1.0706079006195068 = 3825.863789749591
```
	变化：obs_mode，observation space，frames pre second
```python
# visualize the image data from the environment and inspect the data
print(obs.keys())
print(obs['sensor_data'].keys())
print(obs['sensor_data']['base_camera'].keys())
print(obs['sensor_data']['base_camera']['rgb'].shape)
import matplotlib.pyplot as plt
plt.imshow(obs['sensor_data']['base_camera']['rgb'][0].cpu().numpy())
```
	会输出一张图片
## 更改观察模式和控制器模式
```python
# Import required packages
import gymnasium as gym
from tqdm.notebook import tqdm
import numpy as np
import mani_skill.envs
import matplotlib.pyplot as plt
```

```python
#@markdown Run this cell to display the action space of the chosen controller as well as the current view of the environment
# Can be any env_id from the list of Rigid-Body envs: https://maniskill.readthedocs.io/en/latest/tasks/index.html
env_id = "PickCube-v1" #@param ['PickCube-v1', 'PegInsertionSide-v1', 'StackCube-v1']

# choose an observation type and space, see https://maniskill.readthedocs.io/en/latest/user_guide/concepts/observation.html for details
obs_mode = "rgb+depth+segmentation" #@param can be one of ['pointcloud', 'rgb+depth+segmentation', 'state_dict', 'state']

# choose a controller type / action space, see https://maniskill.readthedocs.io/en/latest/user_guide/concepts/controllers.html for a full list
control_mode = "pd_joint_delta_pos" #@param can be one of ['pd_ee_delta_pose', 'pd_ee_delta_pos', 'pd_joint_delta_pos', 'arm_pd_joint_pos_vel']

reward_mode = "dense" #@param can be one of ['sparse', 'dense']

robot_uids = "panda" #@param can be one of ['panda', 'fetch']

# create an environment with our configs and then reset to a clean state
env = gym.make(env_id,
               num_envs=4,
               obs_mode=obs_mode,
               reward_mode=reward_mode,
               control_mode=control_mode,
               robot_uids=robot_uids,
               enable_shadow=True # this makes the default lighting cast shadows
               )
obs, _ = env.reset()
print("Action Space:", env.action_space)

# take a look at the current state of the 4 parallel environments we created
fig, axs = plt.subplots(2, 2, figsize=(8, 8))
rgbs = env.render_rgb_array() # this is a easy way to get the rgb array without having to set render_mode
for i, ax in enumerate(axs.flatten()):
    ax.imshow(rgbs[i].cpu().numpy())
    ax.axis("off")
plt.suptitle("Current States viewed from external cameras")
fig.tight_layout()
env.close()
```
## 创建回放&录制视频
要创建一个回放视频，记录某个回合中发生的情况，我们可以使用下面的 `RecordEpisode` 环境包装器，并指定我们想要记录的内容。

`RecordEpisode` 总是会使用在环境创建时指定的 `render_mode`，在下面的代码中我们使用了之前提到的 "rgb_array"。 "rgb_array" 对应 RGB 摄像头，会渲染它们所显示的内容。还有 "sensors" 渲染模式，它会将所有传感器数据（例如 RGB 和深度）一起显示出来。

ManiSkill 有一个交互式查看器，如果你在带有 GUI 的本地机器上运行，就可以使用。你可以使用 `env.render_human()` 渲染当前状态，这会打开一个查看器。下面的脚本会在检测到它不是在 Colab 中运行时显示交互式查看器。
```python
# Import required packages
import gymnasium as gym
import torch
import mani_skill.envs
from tqdm.notebook import tqdm
from mani_skill.utils.wrappers import RecordEpisode
# to make it look a little more realistic, we will enable shadows which make the default lighting cast shadows
env = gym.make("PickCube-v1", num_envs=4, render_mode="rgb_array", enable_shadow=True)
env = RecordEpisode(
    env,
    "./videos", # the directory to save replay videos and trajectories to
    # on GPU sim we record intervals, not by single episodes as there are multiple envs
    # each 100 steps a new video is saved
    max_steps_per_video=100
)

# step through the environment with random actions
obs, _ = env.reset()
for i in tqdm(range(100)):
    action = env.action_space.sample() # 数据由随机采样得来
    obs, reward, terminated, truncated, info = env.step(torch.from_numpy(action))
    # env.render_human() # will render with a window if possible
env.close()
from IPython.display import Video
Video("./videos/0.mp4", embed=True, width=640) # Watch our replay
```
	将会输出一个随机采样的视频
## 远程操作
为了交互式控制机器人，我们计划开源一系列用于收集数据的工具，包括使用 VR 系统、基于计算机摄像头的系统等。

目前我们提供了一种基于 GUI 的远程操作工具，允许你移动机器人的末端执行器、切换抓取状态，并使其根据你的指令完成任务。下面展示了针对 `StackCube-v1` 任务的工作演示视频。
```
!python -m mani_skill.examples.teleoperation.interactive_panda -e "StackCube-v1"
```

## 强化学习
### 基于状态
```python
from IPython.display import Video
```

```
!wget https://raw.githubusercontent.com/haosulab/ManiSkill/main/examples/baselines/ppo/ppo.py -O ppo.py
!wget https://raw.githubusercontent.com/haosulab/ManiSkill/main/examples/baselines/ppo/ppo_rgb.py -O ppo_rgb.py
```

```
!python ppo.py --env_id="PushCube-v1" --exp-name="state-pushcube" \
  --num_envs=1024 --update_epochs=8 --num_minibatches=32 \
  --total_timesteps=600_000 --eval_freq=8 --num-steps=20
```
放视频
```

Video("runs/state-pushcube/videos/3.mp4", embed=True, width=1024) # Watch a replay during training evaluation
```
### 基于视觉
```
!python ppo_rgb.py --env_id="PushCube-v1" --exp-name="rgb-pushcube" \
  --num_envs=256 --update_epochs=8 --num_minibatches=16 \
  --total_timesteps=250_000 --eval_freq=10 --num-steps=20
```
我们可以像下面这样生成更多的评估轨迹，然后使用轨迹重放工具回放它们，以展示实际视觉输入的样子以及训练好的强化学习智能体如何利用这些输入来完成任务。下面的单元格将把一个.h5 文件保存到实验的 `test_videos` 文件夹中。
```
!python ppo_rgb.py --env_id="PushCube-v1" \
  --evaluate --checkpoint=runs/rgb-pushcube/ckpt_41.pt \
  --num_eval_envs=1 --num-eval-steps=100
```
使用重播工具重新运行我们上面生成的轨迹，并使用传感器渲染模式保存视频。详细说明 [在这里](https://www.google.com/url?q=https%3A%2F%2Fmaniskill.readthedocs.io%2Fen%2Flatest%2Fuser_guide%2Fdatasets%2Freplay.html)
```
!python -m mani_skill.trajectory.replay_trajectory \
  --traj-path=/content/runs/rgb-pushcube/test_videos/trajectory.h5 --use-env-states \
  --render-mode="sensors" --save-video --allow-failure
```

```
Video("runs/rgb-pushcube/test_videos/0.mp4", embed=True, width=256) # Watch our replay from the camera perspective we trained on
```

## 数据下载
- demo下载
- 数据集下载
- 轨迹修改
## 异构并行仿真
## 使用其他机器人
https://maniskill.readthedocs.io/en/latest/robots/index.html
```
!python -m mani_skill.utils.download_asset -y "anymal_c"
```

```python
# Import required packages
import gymnasium as gym
import torch
import mani_skill.envs
from tqdm.notebook import tqdm
from mani_skill.utils.wrappers import RecordEpisode
from IPython.display import Video
env = gym.make(
    "Empty-v1",
    obs_mode="none",
    reward_mode="none",
    enable_shadow=True,
    control_mode="pd_joint_pos", # allows us to easily hold joints mostly in place
    robot_uids="anymal_c",
    render_mode="rgb_array",
)
env = RecordEpisode(env, "./videos", save_trajectory=False)
print(env.agent.keyframes.keys())
env.reset()
kf = env.agent.keyframes["standing"]
env.agent.robot.set_pose(kf.pose)
env.agent.robot.set_qpos(kf.qpos)

# if running on a GPU environment you have to
# apply the changes to environment state
if env.gpu_sim_enabled:
    env.scene._gpu_apply_all() # applies changes
    env.scene.px.gpu_update_articulation_kinematics() # updates robot link poses necessary to render correctly
    env.scene._gpu_fetch_all() # updates GPU buffers
env.render() # call this to update the rendering
for i in tqdm(range(20)):
    # with pd_joint_pos control we try to get the robot to maintain the same joint
    # positions as the keyframe
    obs, reward, terminated, truncated, info = env.step(kf.qpos)
env.flush_video("example")
Video("./videos/example.mp4", embed=True, width=640) # Watch our replay
```
从高处放下来
```python
import sapien
env.reset()
kf = env.agent.keyframes["standing"]
# drop the robot from 1 meter above ground
env.agent.robot.set_pose(sapien.Pose([0, 0, 1]))
env.agent.robot.set_qpos(kf.qpos)
# if running on a GPU environment you have to
# apply the changes to environment state
if env.gpu_sim_enabled:
    env.scene._gpu_apply_all() # applies changes
    env.scene.px.gpu_update_articulation_kinematics() # updates robot link poses necessary to render correctly
    env.scene._gpu_fetch_all() # updates GPU buffers
env.render() # call this to update the rendering
for i in tqdm(range(60)):
    obs, reward, terminated, truncated, info = env.step(kf.qpos)
env.flush_video("example2")
Video("./videos/example2.mp4", embed=True, width=640) # Watch our replay
```
![[Pasted image 20250724151734.png]]
## 运动规划
[MPLib](https://github.com/haosulab/MPlib)
下面的代码使用了 PegInsertionSide 任务的预制解决方案，并生成了一条轨迹，同时将其保存到 demos 文件夹中。目前还没有运动规划的分步教程，但您可以在这里查看代码：[https://github.com/haosulab/ManiSkill/tree/main/mani_skill/examples/motionplanning/panda/solutions](https://github.com/haosulab/ManiSkill/tree/main/mani_skill/examples/motionplanning/panda/solutions)
```
!python -m mani_skill.examples.motionplanning.panda.run -e "PegInsertionSide-v1" \
  -n=1 --save-video --record-dir="demos" --traj-name="peginsertionside" --only-count-success
```