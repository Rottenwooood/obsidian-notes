## 参考
- https://grok.com/share/c2hhcmQtMw%3D%3D_deb8ae22-90cc-4898-ab79-2022eae713df
- https://zhuanlan.zhihu.com/p/1934570701471331551
- https://chat.qwen.ai/s/b69a121f-e077-4531-8d08-760b637fa47c?fev=0.0.203
- 对比： https://zhuanlan.zhihu.com/p/1934570701471331551
https://www.bilibili.com/video/BV1xih7ecEMb
	是什么
- 反向加噪，即去噪，
![[Pasted image 20250707135409.png]]
![[Pasted image 20250707135637.png]]
- 把第一个过程看作每步加一个N(0,1)高斯分布，则diffusion即预测噪声，并逐步去除的过程
- 加噪前后都符合正态分布
	`diffusion前向过程：
- ![[Pasted image 20250707140720.png]]
- 从N(0,1)到N(μ,σ²)
- 加噪前为signal，N(0,1)为noise，有信噪比的关系
- ![[Pasted image 20250707141030.png]]
- 递归地展开此公式，发现两次展开的公式中，两次加噪能用一次加噪的形式表示，同理**n次加噪能用一次加噪表示**，如图所示
	`反向加噪的过程
- ![[Pasted image 20250707142159.png]]
- 反向加噪，即去噪是，假设反向的过程同样符合高斯分布，训练一个神经网络去拟合这个噪声
- 具有马尔科夫性质
- 
- ![[Pasted image 20250707143201.png]]
- 已知xt，x0，求从xt到xt-1去噪的过程，经数学变换，知道xt-1到xt加噪的过程，x0到xt-1加噪的过程，x0到xt加噪的过程，即可求得
- ![[Pasted image 20250707143543.png]]
- 此去噪过程，同样满足高斯分布。接下来的工作是去掉x0
- ![[Pasted image 20250707143809.png]]
- 结合第一个公式，转化为求εt，即从x0到xt的整体的噪声，但不能一次成功，要多次分阶段
- ![[Pasted image 20250707144625.png]]
- 不是很懂

	`如何训练
- ![[Pasted image 20250707145125.png]]
- ![[Pasted image 20250707145058.png]]