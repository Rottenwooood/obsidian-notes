- [ ] https://www.bilibili.com/video/BV1FQTfzMEhF
- [ ] https://photin1a.github.io/posts/20af9ceb.html
- [ ] https://github.com/niejnan/OpenVLA
- [ ] https://zhuanlan.zhihu.com/c_1896002998137771819
- [ ] Robotwin
- https://blog.csdn.net/iamjackjin/article/details/145538265
- https://zhuanlan.zhihu.com/p/23846224178
guide
- https://github.com/TianxingChen/Embodied-AI-Guide

- OpenVLA
	- https://github.com/OpenVLA/OpenVLA
	- 复现版:https://github.com/niejnan/OpenVLA
- TinyVLA
	- https://github.com/liyaxuanliyaxuan/TinyVLA
	- https://huggingface.co/hz1919810/TinyVLA-droid_diffusion_metaworld
	- [TinyVLA：面向机器人操控的快速、数据高效、视觉-语言-动作模型 - 知乎](https://zhuanlan.zhihu.com/p/833511695)
	- [从TinyVLA到SmolVLA——又快又省的Vision-Language-Action模型 - 知乎](https://zhuanlan.zhihu.com/p/1915072936903156771)
	- [端到端-tinyvla本地部署 - 知乎](https://zhuanlan.zhihu.com/p/28397268638)
	- https://huggingface.co/lesjie/Llava-Pythia-400M/tree/main
	- https://github.com/HanshangZhu/vla-vlm-test
	- https://huggingface.co/hz1919810/TinyVLA-droid_diffusion_metaworld
- SmolVLA
	- https://zhuanlan.zhihu.com/p/1913919321010320812
	- [SmolVLA Paper   SmolVLA 论文](https://huggingface.co/papers/2506.01844)
	- [SmolVLA Blogpost   SmolVLA 博客文章](https://huggingface.co/blog/smolvla)
	- [Code  代码](https://github.com/huggingface/lerobot/blob/main/lerobot/common/policies/smolvla/modeling_smolvla.py)
	- [Train using Google Colab Notebook使用 Google Colab 笔记本训练](https://colab.research.google.com/github/huggingface/notebooks/blob/main/lerobot/training-smolvla.ipynb#scrollTo=ZO52lcQtxseE)
	- [SmolVLA HF DocumentationSmolVLA HF 文档](https://huggingface.co/docs/lerobot/smolvla)
	-  
	- https://huggingface.co/satvikahuja/smolvla_so100_veggies30k
	- https://huggingface.co/orsoromeo/smolvla_so101_pick_and_place/tree/main
	- https://huggingface.co/un1c0rnio/smolvla_so101_box_pencil4_180000/tree/main
	- https://maniskill.readthedocs.io/en/latest/robots/so100/
- RDT-2
	- https://github.com/thu-ml/RoboticsDiffusionTransformer#
	- https://rdt-robotics.github.io/rdt-robotics/
	- https://huggingface.co/robotics-diffusion-transformer/maniskill-model/tree/main/rdt
	- export PYTHONPATH=/home/c6h4o2/文档/vla/RoboticsDiffusionTransformer:$PYTHONPATH
	- 

仿真
- maniskill
	- https://colab.research.google.com/github/haosulab/ManiSkill/blob/main/examples/tutorials/1_quickstart.ipynb
	- https://www.maniskill.ai/
	- https://github.com/haosulab/ManiSkill
	- https://maniskill.readthedocs.io/en/latest/user_guide/getting_started/quickstart.html
- metaworld
	- https://github.com/Farama-Foundation/Metaworld
	- https://metaworld.farama.org/
- **[Gymnasium](https://github.com/Farama-Foundation/Gymnasium)**
	- https://github.com/Farama-Foundation/Gymnasium
算法库
- https://github.com/MarkFzp/act-plus-plus
数据集
- https://zhuanlan.zhihu.com/p/16477744603
- [Open X-Embodiment dataset](https://zhida.zhihu.com/search?content_id=257894023&content_type=Article&match_order=1&q=Open+X-Embodiment+dataset&zhida_source=entity) :是一个由 DeepMind 创建并开源的超大规模机器人数据集，汇集了来自 22 种不同机器人类型的数据. RT-2也这个数据集上训练的，简称OXE dataset

关于lerobot，我的理解是：它是一个框架，数据集集中起来，用统一的几个机械臂来采集数据，从而减少需要的数据量。机械臂价廉，容易直接部署某些模型。缺点是官方不自带仿真，需要用maniskill自己搓。如果要复现没有用lerobot的模型，也得用maniskill自己搓建模。所以要跑通在仿真环境中部署VLA的流程。

- smolvla+metaworld/maniskill
	- maniskill能部署so100,有现成策略头
	- 先安装maniskill
	- 导入URDF
	- 获取VIT，SmolVLM2，Policy Head
	- 编写代码
- tinyvla+metaworld/maniskill

|                                          |                                                                                |
| ---------------------------------------- | ------------------------------------------------------------------------------ |
| prism-qwen25-extra-dinosiglip-224px-0_5b | https://huggingface.co/Stanford-ILIAD/prism-qwen25-extra-dinosiglip-224px-0_5b |
| minivla-libero-90                        | https://hf-mirror.com/InspireVLA/minivla-libero-90                             |
| minivla-inspire-libero-90                | https://hf-mirror.com/InspireVLA/minivla-inspire-libero-90                     |
| pretrain_vq                              | https://hf-mirror.com/Stanford-ILIAD/pretrain_vq                               |