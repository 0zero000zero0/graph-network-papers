[原文PDF]([1802.09691.pdf (arxiv.org)](https://arxiv.org/pdf/1802.09691.pdf))
## 传统link prediction算法

```table-of-contents
title: 
style: nestedList # TOC style (nestedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
debugInConsole: false # Print debug info in Obsidian console
```
### 经典算法
现有的启发式可以根据计算分数所需的邻居的最大跳数(**k-hop**)进行分类
例如:
#### one-hop
- Common Neighbors 算法基于这样一个假设，即两个节点共有的邻居数量越多，它们之间存在链接的可能性就越大:$$\mathrm{CN}(u,v)=|\Gamma(u)\cap\Gamma(v)|$$其中，$\Gamma(v)$表示节点v的邻居
- Preferential Attachment算法基于“富者更富”或“优先连接”的概念，即流行的节点(即邻居数量多的节点)更有可能在未来获得更多的链接。这个想法来源于无标度网络的成长模型，其中一些节点因为具有较高的连接度而更容易吸引新的连接:$$\mathrm{PA}(u,v)=|\Gamma(u)|\times|\Gamma(v)|$$

#### two-hop

- Adamic-Adar:Adamic-Adar算法基于这样一个假设，即不是所有的共享邻居都同等重要。具体来说，如果一个共享邻居与很多其他节点相连(即度较高)，那么它对于链接预测的贡献应该比度较低的邻居小。因此，AA算法倾向于给那些连接度较低的共享邻居更高的权重。$$\mathrm{AA}(u,v)=\sum_{z\in\Gamma(u)\cap\Gamma(v)}\frac1{{\log|\Gamma(z)|}}$$
- Resource Allocation:Resource Allocation算法受到物理学中的资源分配过程的启发。在这个模型中，每个节点都被视为有一定资源的实体，这些资源可以通过它们的共享邻居进行分配。如果两个节点通过共享邻居接收到更多的资源，那么它们之间存在链接的可能性就更大。RA算法假设资源的分配是均等的，不同于AA算法中对低度节点的偏好。$$\mathrm{RA}(u,v)=\sum_{z\in\Gamma(u)\cap\Gamma(v)}\frac1{|\Gamma(z)|}$$
#### k-hop 

- PageRank
- Katz index
- SimRank



### 缺陷
**启发式方法对何时可能存在链接有很强的假设**:由于启发式算法可以看作是预先定义的图结构特征
例如，公共邻居启发式假设如果两个节点有许多公共邻居，则它们更有可能连接。这种假设在社交网络中可能是正确的，但是在交通和物流网络中，两个节点之间的连接可能更多地受到地理距离和交通便利性的影响，而与它们共享的其他节点数量关系不大。在这类网络中，基于距离和流量优化的算法可能比传统启发式算法更有效。


## γ-decaying heuristic

#### 封闭子图 enclosing graph
对于图$G=(V，E)$，给定两个结点$x，y∈V$，则$(x，y)$的$h-hop$封闭子图是由G的结点集$\{ i | d(i，x)≤h\quad or\quad d(i，y)≤h \}$导出的子图$G_{x,y}^h$

#### 定义
对于节点$x，y$，$\gamma$-descaying heuristic定义为:
$$\mathcal{H} (x,y)=\eta\sum_{l=1}^{\infty}\gamma^{l}f(x,y,l)$$
其中:
- H表示节点对 (x, y) 之间存在链接的启发式评分
- γ是介于0和1之间的衰减因子
- η是正常数或γ的正函数，其上界为常数，
- $f$是给定网络下$x，y，l$的非负函数，并且$f$满足:
	- $f (x, y, l) ≤ λl$ where $λ < \frac{1}{γ}$ ; 
	-  $f (x, y, l)$   is calculable from $G_{x,y}^{h}$ for$l = 1, 2, \cdots, g(h)$, where $g(h) = ah+b$with $a, b ∈ N$ and $a > 0$,

#### 误差
γ衰竭理论解释误差随聚合半径呈现指数级减小:
$$|\mathcal{H}(x,y)-\widetilde{\mathcal{H}}(x,y)|=\eta\sum_{l=g(h)+1}^\infty\gamma^lf(x,y,l)\leq\eta\sum_{l=ak+b+1}^\infty\gamma^l\lambda^l=\eta(\gamma\lambda)^{ak+b+1}(1-\gamma\lambda)^{-1}.$$

#### 理论解释
一个成功的链接预测启发式算法最好将指数级较小的权重放在远离目标的结构上，因为网络的远程部分节点上对链接存在的贡献很小，这它们意味着**局部封闭子图**已经包含足够的信息来学习良好的图结构特征，以便进行链接预j测，考虑到从整个网络学习通常是不可行的


### SEAL: An implemetation of the theory using GNN

SEAL框架是一个基于图神经网络的链接预测框架，通过包括以下三个步骤
1. [[#封闭子图 enclosing graph]]
2. [[#节点信息矩阵]]
	1. [[#γ-decaying heuristic#Node labeling]]
	2. [[#node embedding]]
	3. [[#Node attributes]]
3. 使用GNN进行链接预测


#### 提取封闭子图

封闭子图提取公式:
$$G_{\nu_1,\nu_2}^k=\{\nu|min(d(\nu,\nu_1),d(\nu,\nu_2))\leq k\}$$
也就是说， 到两个待预测点的距离小于k的所有节点组成的子图
给定一个网络，我们的目标是自动学习一个“启发式”，最好地解释链接的形成。受理论结果的启发，该函数以链接周围的局部封闭子图为输入，输出链接存在的可能性。为了学习这样一个函数，我们在封闭子图上训练一个图神经网络（GNN）。因此，在提起封闭子图后，用提取一组采样正链（观察到）和一组采样负链（未观察到）的封闭子图来构造训练数据

#### 节点信息矩阵
##### Node labeling
X中的第一个组件是每个节点的结构标签。节点标签为函数$f_{l}:V→ N$，它将一个整数标签$f_{l}（i）$赋给封闭子图中的每个节点`i`。其目的是使用不同的标签标记节点在封闭子图中的不同角色：
- 中心节点x和y是链接所在的目标节点
- 相对于中心具有不同相对位置的节点对于链接具有不同的结构重要性:适当的节点标签应该标记这些差异。如果我们不标记这些差异，GNNs将无法判断应该预测链路存在的目标节点的位置，并丢失结构信息。

因此，基于上述准则，作者提出了Double-Radius Node Labeling (DRNL):
1. 中心节点`x` ，`y` 标记为1
2. 对于其他节点`i`,标记数字为基于距离的标记：对于图中的其他节点，它们的标签根据节点相对于目标节点的距离来确定。具体来说，一个节点的标签由它到目标节点的最小和最大跳数（hops）决定，$$f_l(i)=1+min(d_x,d_y)+(d/2)[(d/2)+(d\%2)-1],$$其中$d_x:=d(i,x),d_y:=d(i,y),d:=d_x+d_y$
3. 对于不可达点`j` ,$d(j, x) = ∞ \quad or \quad d(j, y) = ∞$，则$f_{l}(j)=0$

例子:
![[double-redius label.png|207]]

1. 初始化:要预测的两个节点:A=B=1
2. 计算其他节点距离并赋予标签：
	1. C和D直接与A，B相连-->C=2
	2. 节点E直接与节点B相连，但与节点A的最小距离为2（通过B）。因此E=3
	3. 节点F与两个目标节点A和B的最小距离都大于1（到A是2，到B是3），因此F=4

##### Node embedding
在论文中，节点嵌入使用的是 node2vec 方法
但是，node2vec(在整个graph)的节点嵌入隐含了连接的特征，导致过拟合
解决方法:
**负样本注入**(negative injection):把负样本采样的边也加入到整个图中，再进行node2vec
可能的缺陷:
1. 改变图结构，尤其是在蛋白质链接预测中
2. 平衡问题：如何决定加入多少负样本以及选择哪些负样本。
3. 加入太多负样本可能会过度扭曲图的结构 

##### Node attributes
结点本身的特征(元数据)，例如用户的年龄、性别、兴趣点等

#### GNN
最后使用信息矩阵X训练GNN
论文中使用的GNN是DGCNN架构
1. 图卷积
2. sortPooing:DGCNN引入了一种名为SortPooling的层，它的目的是将不同大小的图转换成固定大小的表示，以便可以被传统的卷积神经网络（CNN）处理。SortPooling层通过对节点特征进行排序并选择固定数量的节点来实现这一点，这样做既保留了图的结构信息，又使得图的大小统一化。
3. 一维卷积和全连接层：经过SortPooling层处理后，图的表示被送入一系列的一维卷积层，这些卷积层进一步提取特征并捕捉节点间的局部模式。
4. 全连接层:最后，通过全连接层将特征向量转换成图的分类结果。


### 训练框架
1. 选定两个目标节点:
2. 确定领域半径(h)
1. 提取封闭子图
2. 标记封闭子图中节点的标签(DRNL)
3. 负样本注入
4. node2vec获取节点嵌入矩阵
5. 获取节点属性矩阵
6. 把4,6,7的矩阵拼在一起获得信息矩阵X
7. 使用GNN进行训练(DGCNN)

