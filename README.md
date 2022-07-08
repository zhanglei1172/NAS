# NAS

总结各种NAS方法的最核心思想

## Training-free NAS

- Zero-cost NAS
    - 使用jocov、snip、grasp、synflow等作为score，来表征**架构**的性能
- FreeDARTS
    - 将剪枝的权重重要性度量指标挪用到NAS的**架构参数**(op系数)上

## Meta

- MetaAS
  - 权重和架构一同作为元学习对象(分布),其中架构作为cate分布，用gumble近似优化

## BBO-for-NAS

- DE
  - 使用连续space，evaluate时再离散
- BANANAS
  - path encoding架构

## No retraining

- BigNAS
  - sandwich rule训练supernet
  - inplace-distill大网络蒸馏小网络
  - initialize：残差块最后一项BN gamma为0
  - 大模型收敛比小模型快，所以修改lr策略
  - 只对最大模型正则
  - BN校正
  - coarse-to-fine select
- OFA
  - progressive训练
  - 映射kernel
  - 需要fine-turning
- attentiveNAS
  - bestUP、worstUP
  - 优化不同flops限制的子网
  - 估计某flops下的op分布，采样，reject sampling

## one-shot

- SPOS
  - uniform Sample
  - ES
- fairNAS
  - 绝对公平采样：batch backward前保证所有op都有被采样到
- greedyNAS
  - 搜索空间大，要求预测所有子网条件苛刻
  - 训练差路径会对好路径干扰
  - 做法：一次性采样很多路径，只使用在验证集表现好的几条训练
- cream
  - 引入蒸馏，来提高所有子网的训练效果
  - 维护一个好(teacher)网络pool
  - 引入meta-network来挑选匹配的教师网络
  - 第二阶段从pool中选最佳网络，省去ES、RS

## Differential NAS

- Darts
  - 超网内的cell中每个op有一个松弛参数
  - bi-level优化
  - 优化架构参数时，使用一阶近似或二阶近似
- ProxylessDartrs
  - 针对DARTS优化显存过多问题
  - 训练权重，只有一条路径
  - 训练架构，取两条路径
  - 两种解决latency不可微方式
    - loss上加入latency
    - lantency是关于op的函数
  - Reinforced
- PDARTS
  - progressice增加深度、减少op搜索空间
  - 正则
    - Dropout对skip-connect，并逐渐减小rate
    - 限制最终skip数量
- VIM-NAS 
  - 用NN生成DARTS里的alpha
  - implicit distribution
- DrNAS
  - DARTS的alpha建模为Dirichlet分布

## other

- MnasNet
  - block之间线性堆叠，搜索每个block内到底啥操作
  - RL，reward由proxy acc和实际phone上的lat构成
- EfficientNet
  - 同时对深度、宽度、分辨率缩放
  - 按一定公式比例调整可以获得不同复杂度的模型
- FewShotNAS
  - 缓解超网权重耦合，将空间划分
- GM-NAS
  - 将few-shot的划分方式改进
    - 依据gradient方向匹配
- RANK-NOSH
  - 金字塔
    - 最底层是zero-cost指标
    - 类似SH算法，但config有机会被恢复
- Randomly-WiredNAS
  - 随机连线的NAS也有不错的性能
- ProxyBO
  - zero-cost与BO的组合
  - 前期sample少更信任zero-cost，后期sample多信任BO
- TSE
  - 根据训练速度：loss曲线下面积