前置论文:[[Link Prediction Based on Graph Neural Networks]]
[原文PDF]([ieeexplore.ieee.org/ielx7/6287639/10005208/10144318.pdf](https://ieeexplore.ieee.org/ielx7/6287639/10005208/10144318.pdf))
```table-of-contents
title: 
style: nestedList # TOC style (nestedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
debugInConsole: false # Print debug info in Obsidian console
```

### 基于图神经网络的链接预测架构
#### GNN一般架构:MPNN
MPNN(massage pass neural network) 是图神经网络的一种架构:
1. **Massage pass(aggerate)** : 在第l层网络中，对于消息函数$M_{l}$用于聚合目标节点的邻居特征，包括**目标节点的自身特征$h_{v}^l$、其邻居节点特征$h_{u}^l$、其与邻居节点链接的连边特征$e_{uv}^l$ ，使用消息传递函数进而形成一个消息向量$m_v^{l+1}$传递给目标节点**，公式如下:$$m_{v}^{l+1}=\sum_{u \in N(v)}M_{l}(h_{v}^l,h_{u}^l,e_{uv})$$
2. **Update** : 在获取到周围邻居的消息传递向量后，再通过一个**更新函数$\sigma$**来更新该层的结点特征表示$$h_{v}^{l+1}=\sigma (m_{v}^{l+1},h_{v}^l)$$
3. **(Pooling)**:如果是对图进行分类，或者是需要全图的信息来做下游任务，则需要对全图的节点特征进行**池化(Pooling)**,图神经网络的Pooling方法有很多，详情参考

在通过多层堆叠得到最终的节点嵌入表示后，用于下游的任务(例如使用MLP进行分类)

## 本论文提出的方法
### 整体算法
![[Lel_GNN_algriothm.png]]
其中
- (8)式是封闭子图的提取方法:$$G_{\nu_1,\nu_2}^k=\{\nu|min(d(\nu,\nu_1),d(\nu,\nu_2))\leq k\}$$
- (9)式是节点标记函数，在前置论文[[Link Prediction Based on Graph Neural Networks]]中提出
- (6)式是在边采样Edge Sampling中计算局部网络连接质量的公式$\alpha$
- (10)和(11)是线图边特征的生成公式[[#边特征]]
### 边采样 Edge Sampling
#### 为什么要进行边采样
边采样是指在图中随机选取边的子集。
该方法可以快速消除重复特征，并将图转换为新的尺度，使得节点的特征能够有效地传播到其远端邻居。
边采样方法使用新的子图从输入是h-hop封闭较少的特点。为了生成一系列大小不同的子图，我们可以重复地对图进行聚合。为了预测链路的存在，我们提出的模型以多尺度封闭子图作为输入，然后提取层次属性。我们提出的策略是基于这样一个假设，即不同大小的封闭图可以为链路预测提供相互增强的数据。
#### 边采样的具体做法

设$c_{ij}$表示无向图$G=(V，E)$中`i`和`j`的共享邻居(论文中近包括二阶共享邻居)的总数，其中$(i，j)∈E$。
首先关注所有边的所有$c_{ij}$值已知的情况。为了实现这一点，我们可以并行精确地计算它们，或者使用称为“邻居采样”的采样技术来获得近似值。利用一个概率为p的多项式分布模型，从$G$中随机选取m条边，构造一个稀疏子图的$H$(**稀疏子图是一个子图，它保留了父图的连通性特征，但具有相当大的图，但有相当少的边**)
选出稀疏子图的方法可以采用: **最小化必须计算的边数**，该论文使用的是$$p_{ij}=\frac{\frac2{c_{ij}+2}}{\sum_{(i,j)\in E}\frac2{c_{ij}+2}}$$来计算一条边$(i,j)\in E$被选中的概率。如果一条边被选中($k \geq 1$)后加入稀疏子图$H$，就赋予这条边权重$k(mp_{ij})^{-1}$，注意$c_{ij}$仅包括二阶邻居。
此外，论文中还提出了一个用于表示局部网络连接质量的参数$\alpha$:$$\alpha=\frac1n\sum_{(i,j)\in E}\frac2{c_{ij}+2}.$$
通过$p_{ij}$和$\alpha$，就可以选出在一个封闭子图中对于可能对模型性能贡献最大的边，然后保留这些必须被计算的边，以此达到最小化必须被计算的边的个数。

#### 动态的
在虽然 α 基于图的当前状态计算得出，但在多次迭代或训练过程中，选择保留哪些边的策略可以根据模型的性能反馈进行调整。这意味着虽然每次的 α 是根据当前图结构计算的，但整个采样策略可以动态适应模型训练的需要
### 线图 Line Graph
#### 为什么要用线图
当我们试图提取一个特定大小的特征向量用于后续预测时，我们会遇到一些数据丢失。
为了解决这个问题，我们建议在封闭子图之外创建一个线图来显示初始图的边之间的连接。所以，图卷积神经网络可以用来预测一个链接的性质，通过查看其线图表示。
#### 线图定义
Line Graph 定义 :
1. 在创建线图$L(G)$时，初始图G的链接用作顶点。
2. 在$L(G)$中，两个顶点之间的连接仅在其各自的链接都指向同一节点时发生
例图
![[Pasted image 20240319141901.png]]
在图中，边(A,D)和(A,B)被转换成了节点`AD`和`AB`，由于节点`D`和`B`都与节点`A`相连，因此在线图$L(G)$中，`AB`和`AD`也应该相连
如果G是具有x个顶点和y个边的图，则相应的线图$L(G)$中的顶点数量为y。$L(G)$具有以下数量的边$$e_{L(G)}=\frac12\sum_{i=1}^{x}d_{i}^{2}-y$$
其中$d_i$是节点`i`的度。
该条件确保学习线图特征不会显著增加计算复杂度。此外，由初始图G生成线图$L(G)$的时间复杂度是线性的

### 节点标记
节点标记在论文[[Link Prediction Based on Graph Neural Networks]]中提出，其主要采用的思想是:
节点标签为函数$f_{l}:V→ N$，它将一个整数标签$f_{l}(i)$赋给封闭子图中的每个节点`i`。其目的是使用不同的标签标记节点在封闭子图中的不同角色：
- 中心节点x和y是链接所在的目标节点
- 相对于中心具有不同相对位置的节点对于链接具有不同的结构重要性:适当的节点标签应该标记这些差异。如果我们不标记这些差异，GNNs将无法判断应该预测链路存在的目标节点的位置，并丢失结构信息。

因此，基于上述准则，作者提出了Double-Radius Node Labeling (DRNL):
1. 中心节点`x` ，`y` 标记为1
2. 对于其他节点`i`,标记数字为基于距离的标记：对于图中的其他节点，它们的标签根据节点相对于目标节点的距离来确定。具体来说，一个节点的标签由它到目标节点的最小和最大跳数(hops)决定，$$f_l(i)=1+min(d_x,d_y)+(d/2)[(d/2)+(d\%2)-1],$$其中$d_x:=d(i,x),d_y:=d(i,y),d:=d_x+d_y$
3. 对于不可达点`j` ,$d(j, x) = ∞ \quad or \quad d(j, y) = ∞$，则$f_{l}(j)=0$

### 边特征
在把原图$G$转换成线图$L(G)$时，原图$G$仅为节点进行标记，而节点和边的关系是不一样的，因此不能直接把原图中节点的标签复制给线图中的边，而是需要一个转换函数把原图中一条边上的两个节点的特征转换到线图中节点的特征。$$l_{(\nu_i,\nu_j)}=\|(\min(f_l(\nu_i),f_l(\nu_j)),\\max(f_l(\nu_i),f_l(\nu_j)),avg(f_l(\nu_i),f_l(\nu_j)))$$
其中，$v_{i},v_{j}$是边的两个端点，$f_{l}(\cdot)$是DRNL标记函数。$\mid\mid$是连接操作 . 该转换函数可以:保留节点的结构相关性信息但是只能适用于非属性图
在属性图中,例如论文引用中，原图中的论文节点会把摘要作为节点的特征，但是这个公式并没看考虑到节点本身的特征。如果要在属性图中使用，则需要用$$l_{(\nu_i,\nu_j)}=\|(min(f_l(\nu_i),f_l(\nu_j)),\\max(f_l(\nu_i),f_l(\nu_j)),\\a\nu g(f_l(\nu_i),f_l(\nu_j)),x_{\nu_i}+x_{\nu_j})。$$ 其中$x_{v_{i}},x_{v_{j}}$是原图中节点本身的属性特征

### Result
#### 基准模型测试

![[Pasted image 20240319145726.png]]
与其他链接预测算法比起来有少量提升
#### 边可视化

![[Pasted image 20240319151447.png]]