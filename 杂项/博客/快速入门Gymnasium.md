

![Gymnasium Logo](https://gymnasium.farama.org/_images/gymnasium-text.png)

## 什么是Gymnasium？

Gymnasium是一个专为强化学习研究设计的标准化环境库，它是OpenAI Gym库的一个维护分支。正如官网所述："Gymnasium is a maintained fork of OpenAI's Gym library." 它提供了一个简单、Pythonic且功能强大的API，能够表示各种强化学习问题，并且包含了一个兼容旧版Gym环境的包装器。

与原始Gym相比，Gymnasium解决了原项目停止维护的问题，继续为强化学习社区提供高质量的环境和稳定的API。它默认支持实时画面输出、动图生成和文本渲染三种模式，让研究人员可以直观地观察和分析算法表现。

## 为什么选择Gymnasium？

在强化学习领域，环境是算法验证和性能评估的基础。Gymnasium之所以广受欢迎，主要有以下几点原因：

1. **标准化API**：提供统一的接口，使不同环境之间的切换变得简单
2. **丰富的环境集合**：包含从简单控制问题到复杂游戏的各种环境
3. **轻量级设计**：易于安装和使用，适合快速原型开发
4. **社区支持**：拥有活跃的开发者社区和详尽的文档

## 安装指南

### 基础安装

最简单的安装方式是通过pip：

```bash
pip install gymnasium
```

注意：与旧版Gym不同，Gymnasium使用`gymnasium`作为包名，导入时也应使用`import gymnasium as gym`。



### 安装扩展环境

Gymnasium提供了多种环境类别，可以根据需要安装：

```bash
# Atari游戏环境
pip install gymnasium[atari]

# Box2D物理引擎环境（如LunarLander）
pip install gymnasium[box2d]

# Mujoco物理模拟环境
pip install gymnasium[mujoco]
```

### 解决下载问题

在国内，由于网络原因，pip安装可能会很慢或失败。可以使用国内镜像源加速下载：

```bash
pip install gymnasium -i https://pypi.tuna.tsinghua.edu.cn/simple/
```

## 快速入门

下面是一个使用Gymnasium的完整示例，以LunarLander（月球登陆器）环境为例：

```python
import gymnasium as gym

# 初始化环境，设置渲染模式为"human"可实时显示画面
env = gym.make("LunarLander-v3", render_mode="human")

# 重置环境，生成初始观察值
observation, info = env.reset(seed=42)

for _ in range(1000):
    # 这里可以插入你的策略（policy）
    # 当前示例使用随机动作
    action = env.action_space.sample()
    
    # 执行动作，获取环境反馈
    observation, reward, terminated, truncated, info = env.step(action)
    
    # 如果回合结束，则重置环境
    if terminated or truncated:
        observation, info = env.reset()

# 关闭环境，释放资源
env.close()
```

### 代码解析

1. **环境初始化**：`gym.make("LunarLander-v3", render_mode="human")`
   - `"LunarLander-v3"`指定使用月球登陆器环境的第3版本
   - `render_mode="human"`设置渲染模式为实时显示画面

2. **环境重置**：`env.reset(seed=42)`
   - 重置环境状态，返回初始观察值和额外信息
   - `seed`参数确保实验可重复性

3. **交互循环**：
   - `env.action_space.sample()`：从动作空间中随机采样一个动作
   - `env.step(action)`：执行动作，返回：
     - `observation`：新的观察值
     - `reward`：获得的奖励
     - `terminated`：回合是否正常结束
     - `truncated`：回合是否被截断（如超时）
     - `info`：额外的调试信息

4. **环境关闭**：`env.close()`
   - 释放环境占用的资源，特别是图形资源

## 核心概念详解

### 环境（Environment）

在Gymnasium中，环境是强化学习问题的具体实现。每个环境都有：

- **观察空间（observation_space）**：描述环境状态的可能取值范围
- **动作空间（action_space）**：描述智能体可以执行的动作
- **奖励机制（reward）**：评估动作好坏的标准

```python
print(f"Observation space: {env.observation_space}")
print(f"Action space: {env.action_space}")
```

### 包装器（Wrappers）

Gymnasium提供了丰富的环境包装器，可以修改环境行为：

```python
from gymnasium.wrappers import RecordVideo

# 录制视频的包装器
env = RecordVideo(env, "videos", episode_trigger=lambda x: x % 50 == 0)

# 帧跳过包装器
from gymnasium.wrappers import FrameSkip
env = FrameSkip(env, skip=4)
```

常用包装器包括：
- `RecordVideo`：录制环境运行视频
- `TimeLimit`：限制每个回合的最大步数
- `NormalizeObservation`：标准化观察值
- `FrameStack`：将连续几帧堆叠作为输入

### 自定义环境

Gymnasium支持创建自定义环境，这对于特定领域的研究至关重要：

```python
import gymnasium as gym
from gymnasium import spaces

class CustomEnv(gym.Env):
    def __init__(self):
        self.action_space = spaces.Discrete(2)
        self.observation_space = spaces.Box(low=-1, high=1, shape=(2,))
        
    def step(self, action):
        # 实现环境逻辑
        observation = [0.0, 0.0]
        reward = 1.0
        terminated = False
        truncated = False
        info = {}
        return observation, reward, terminated, truncated, info
        
    def reset(self, seed=None, options=None):
        # 重置环境
        observation = [0.0, 0.0]
        info = {}
        return observation, info
```

## 实际应用案例

### 1. 机器人控制

Gymnasium可以与机器人仿真软件（如PyBullet、MuJoCo）结合，用于机械臂控制、人形机器人行走等任务：

```python
import gymnasium as gym

# 使用MuJoCo的Ant机器人环境
env = gym.make("Ant-v4", render_mode="human")
```

### 2. 游戏AI

Atari游戏环境是测试强化学习算法的经典基准：

```python
# 安装Atari环境后
env = gym.make("Pong-v0", render_mode="human")
```

### 3. 连续控制问题

对于需要精确控制的问题，如倒立摆、月球登陆：

```python
# 连续动作空间的倒立摆
env = gym.make("Pendulum-v1", render_mode="human")
```

## 小技巧

1. **固定随机种子**：确保实验可重复
   ```python
   env = gym.make("CartPole-v1", seed=42)
   ```

2. **使用向量化环境**：加速训练
   ```python
   from gymnasium.vector import SyncVectorEnv
   envs = SyncVectorEnv([lambda: gym.make("CartPole-v1") for _ in range(4)])
   ```

3. **监控训练过程**：使用`RecordEpisodeStatistics`包装器
   ```python
   from gymnasium.wrappers import RecordEpisodeStatistics
   env = RecordEpisodeStatistics(env, 10)
   ```

4. **保存和加载环境状态**：便于调试和恢复
   ```python
   state = env.state()
   # ... later ...
   env.set_state(state)
   ```

Happy coding and happy learning!