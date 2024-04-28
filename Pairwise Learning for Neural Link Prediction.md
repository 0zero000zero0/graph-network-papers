## 论文地址
http://arxiv.org/abs/2112.02936

## 现有的问题
现有的图神经网络过度重视神经网络的 **架构** 来增加其表达能力，但是忽略了在链接预测问题上的一些基本特性。例如，把链接预测问题建模成二分类问题并且使用交叉熵损失函数，由于
- 由于大多数图的自然稀疏性，链接分类是极不平衡的，也就是邻接矩阵是稀疏的，大多数节点之间都没有真正的链接(正样本)
- 大多数链接预测优化的目标不是将正对标记为1，将负对标记为0，而是要求正对的排名高于负对，这并不是直接求解链接预测问题，而是间接优化
采用交叉熵函数对链路预测任务的目标似乎并不那么直接
## 相关工作
1. 新的链接预测框架: 逐对链接预测(pair-wise，这样翻译是对标element-wise，逐元素)
2. 根据不同的目标/图的结构，使用不同的评分函数
3. 几种对于不同问题的负采样策略
4. 使用有效排序损失(近似直接优化AUC)进行优化(对标解决第二个问题)
5. 使用Random walk 进行图增强

##  提出的方法
###  新的链接预测框架
如图所示，该链接预测框架主要由三部分组成:Negative Sampler，Neighborhood Encoder，Link Predictor。

![[PLNLP_architecture.png|853]]

#### Negative Sampler
考虑到不同的图需要学习的图表示不一样，因此负采样策略也应该对应选择不用的策略
1. Global Sample: 全局采样是在**整个图**上进行均匀采样，**这采样策略适用于需要学习图的全局信息**，例如，在蛋白质相互作用中，我们感兴趣的是所有可能的节点对中的潜在节点对，这些节点对值得进一步分析(进行卷积/池化)
2. Local Sample: 局部采样是从局部子图中进行采样生成负样本，由于是从局部进行的，因此可以采样除了均匀分布以外的其他分布进行采样，例如基于节点度的采样策略(作者这里没有继续阐述，基于我的理解 ，应该是与伯努利采样相反，如果一个节点的度越大，那么采样的概率就越大，因为节点的度很大意味着该节点的特征很典型，需要模型区分)，**这种策略适用于对单个节点进行良好排序的情况**
3. Adversarial Sample: 类似于GAN(生成对抗网络)，该采样策略生成的负样本是由一个模型生成的，该负样本生成模型会和一个判别模型进行对抗，链路预测模型和负样本生成模型进行对抗博弈。**通过不断提供高质量的阴性样本，对抗性抽样比随机抽样更具稳健性**。
4. Negative Sample Sharing: 由于该框架的 逐对进行预测的，如果每次训练都重新生成负样本，那么时间复杂度就会非常高。因此该采样策略是一次性生成 $m$个负样本，然后每次训练时对负样本进行随机排序(重新计算下标)，随机排序。超参数$num_{neg}-1$表示机制将随机排列$（num_{ neg}-1）$次负样本。对于逐对链接预测框架，这种采样方式可以有效减少计算量![[Pasted image 20240427221616.png]]


## Neighborhood Encoder
邻居编码器主要由两个部分组成:Node neighborhood encoder(NNE)和Node-pair Neighborhood encoder(或者是Edge level Neighborhood Encoder (ENE)) 
### NNE
对于一对输入的节点$v_{i},v_{j}$(在架构图中是A,B)，在采样后生成负样本，使用GNN(例如GCN，GraphSAGE，GAT)对正负样本，进行学习编码，提取正样本和负样本周围的结构信息和周围节点的信息，
![[PLNLP_NNE.png]]
其中，$x_i$一般是节点$v_i$的输入特征(例如对节点的属性进行编码)，$\mathcal{N}^h(v_{i})$是在节点$v_i$周围提取的子图，节点$v_{j}$同理。由于该框架只考虑同质图(hemogenous graph，所有节点的类型都是一致的)，因此，所有节点共享用一个GNN网络参数。经过GNN后，会得到一些节点的嵌入
### ENE
ENE是对子图进行编码，使用(SEAL，NIAN等用于解决链接预测问题的其他框架/架构)，例如:SEAL[[Link Prediction Based on Graph Neural Networks]]:
1. 从目标节点对提取到达目标节点对的距离小于$k$的节点组成封闭子图
2. 使用DRNL(doubel-radius node label)对封闭子图中的节点进行位置编码，获取结构信息
3. 负样本注入
4. 使用node2vec生成嵌入
5. 把节点本身的元数据(例如用户的年龄、性别、兴趣点等)加入到特征矩阵中
6. 使用GNN训练
通过SEAL框架训练后得到一对节点的嵌入，这些节点嵌入包含了目标节点对，负样本对的元数据特征，结构特征，以及周围邻居的。由于ENE是对 节点对进行编码，因此可以视作为这两个节点之间的边的特征$$h_{ij}=ENE(x_{i},x_{j},\{\mathbf{x_{i}}|v_{k\in \mathcal{G^h}(v_{i},v_{j})}\})$$其中$\mathcal{G^h}$是
## Link Score Predictor
链接分数预测器的主要作用是把上述Neighborhood encoder的结果进行融合，充分考虑节点(通过NNE编码)和边(通过ENE编码)的特征。融合的方式有:
1.  Dot:直接把两个向量进行点乘$$s_{ij}=\mathrm{h}_i\cdot\mathrm{h}_j  $$
2. Bilinear Dot:双线性点乘，由于点乘操作只适用于无向图(因为点乘时对称的，$a\cdot b=b \cdot a$)，在面对有向图时，使用双线性点乘来大批对称性质$$s_{ij}=h_{i}Wh_{j}$$其中$W$是可学习的参数
3. MLP:使用多层感知机把两个编码后的特征进行降维$$s_{ij}=MLP(h_{ij})$$，在进入MLP之前，还可以先对两个特征进行一些预处理，例如哈达玛积或者是把两个向量连接起来$$ s_{ij}=MLP(h_{i}\odot h_{j}) $$$$s_{ij}=\mathrm{MLP}(\mathbf{h}_i||\mathbf{h}_j)$$
Link Score Predictor也就是评分模型，输出的结果是分数
## 目标函数
使用交叉熵损失函数优化模型是间接求解链接预测问题，此外，由于图中链接与非链接存在极大不平衡，因此作者采用了排序的思想来优化目标，具体是$$s_{ij}>s_{kl},\forall(v_{i},v_{j})\in E\mathrm{~and~}\forall(v_{k},v_{l})\in E^{-}$$其中$s$是Link Score Predictor输出的分数，$E^-$是生成的负样本(集)，实际上，上述的目标函数优化的是正样本的分数大于样本的分数，这与AUC(Area Under the Curve)是一致的，$$\mathrm{AUC}=\sum_{\begin{array}{c}(v_i,v_j)\in E\\\end{array}}\sum_{\begin{array}{c}(v_k,v_l)\in E^-\\\end{array}}\frac{\mathbb{1}[f_{\boldsymbol{\theta}}(v_i,v_j)>f_{\boldsymbol{\theta}}(v_k,v_l)]}{|V\times V|}$$ 
其中$f_{\theta}(v_{i},v_{j})=s_{ij}$  $\mathbb{1}$是指示函数，如果$f_{\theta}(v_{i},v_{j})>f_{\theta}(v_{k},v_{l})$则为1，否则为0。$\mathbf{V}$是样本大小。由于该目标函数的偏导数要么为0，要不不存在，因此无法直接使用该函数进行反向传播优化模型，但是可以使用其他损失函数优化模型，例如逻辑损失，指数损失。还有一个选择是近似AUC，$$O_{\mathrm{AUC}}=\min_{\theta}\sum_{(v_i,v_i)\in E,(v_i,v_k)\in E^-}\left(1-f_{\theta}\left(v_i,v_j\right)+f_{\theta}\left(v_i,v_k\right)\right)^2+\frac\lambda2||\theta||^2$$
$\frac{\lambda}{2}\Vert \theta\Vert^2$是正则项；在优化这个损失函数时，就是在强制要求正负样本评分距离为1。实际情况下如果正负样本的距离大于1也是可行的，因此可以改良为$$O_{\mathrm{AUC}}=\min_{\theta}\sum_{(v_i,v_i)\in E,(v_i,v_k)\in E^-}\gamma_{ij}\max(0,\left(\gamma_{ij}-f_{\theta}\left(v_i,v_j\right)+f_{\theta}\left(v_i,v_k\right)\right)^2)+\frac\lambda2||\theta||^2$$这样优化的含义就是让正负样的分数距离大于等于1，其中$\gamma_{ij}$为margin coefficient，可以调整正负样本分数的最小距离

## 通过随机游走增强数据
在一些图中:
1. 高阶邻居的信息可能会很重要，需要对一些高阶邻居进行采样
2. 缓解GNN过渡从局部邻居中聚合信息导致过平滑问题
3. 一些高度节点的邻居太多导致计算效率低下

因此随机游走生成一些节点序列，从这个序列中节点聚合信息$$RW(v_{i})={v_{k+1,\cdots,v_{k+l} }}$$增强后的数据为$$E_{\mathbf{aug}}=E\cup\{(v_i,v_j)|v_j\in\mathrm{RW}(v_i),\forall v_i\in V\}$$
然后就可以使用这些数据训练模型


## 实验结果
实验配置
![[PLNLP实验配置.png|734]]
链接预测实验结果:
![[PLNLP链接预测实验结果.png|734]]
