## This repo introduces some wonderful works for action recognition, especially the skeleton-based action recognition method.

### action recognition review
#### (1). Datasets

- [Kinetics](https://deepmind.com/research/open-source/open-source-datasets/kinetics/)
- [UCF101](http://crcv.ucf.edu/data/UCF101.php)
- [HMDB51](<http://serre-lab.clps.brown.edu/resource/hmdb-a-large-human-motion-database/>)
- [Sports-1M](https://cs.stanford.edu/people/karpathy/deepvideo/index.html)
- [Charades](https://allenai.org/plato/charades/)

#### (2). Methods

- Two-stream based
  - Two-Stream Convolutional Networks for Action Recognition in Videos [paper](https://papers.nips.cc/paper/5353-two-stream-convolutional-networks-for-action-recognition-in-videos.pdf)
  - Temporal Segment Networks: Towards Good Practices for Deep Action Recognition [paper](https://arxiv.org/pdf/1608.00859.pdf)  [code](https://github.com/yjxiong/temporal-segment-networks)
- 3D convolution based
  - C3D: Generic Features for Video Analysis  [paper](https://arxiv.org/pdf/1412.0767v1.pdf)  [code](http://vlg.cs.dartmouth.edu/c3d/)
  - Temporal 3D ConvNets: New Architecture and Transfer Learning for Video Classification  [paper](https://arxiv.org/pdf/1711.08200.pdf)  [code](https://github.com/MohsenFayyaz89/T3D)
  - A Closer Look at Spatiotemporal Convolutions for Action Recognition  [paper](https://arxiv.org/pdf/1711.11248.pdf)  [code](https://github.com/facebookresearch/R2Plus1D)
  - Quo Vadis, Action Recognition? A New Model and the Kinetics Dataset  [paper](https://arxiv.org/pdf/1705.07750.pdf)  [code](https://github.com/deepmind/kinetics-i3d)
  - Non-local Neural Networks  [paper](https://arxiv.org/pdf/1711.07971.pdf)  [code](https://arxiv.org/pdf/1711.07971.pdf)  
- Skeleton based
  - Co-occurrence Feature Learning for Skeleton based Action Recognition using Regularized Deep LSTM Networks  [paper](https://arxiv.org/pdf/1603.07772.pdf)
  - An End-to-End Spatio-Temporal Attention Model for Human Action Recognition from Skeleton Data  [paper](https://arxiv.org/pdf/1611.06067.pdf)  [project](http://www.icst.pku.edu.cn/struct/Projects/AAAI_SSJ/aaai_2017_ssj.html)
  - View Adaptive Neural Networks for High Performance Skeleton-based Human Action Recognition  [paper](https://arxiv.org/pdf/1804.07453.pdf)
  - Co-occurrence Feature Learning from Skeleton Data for Action Recognition and Detection with Hierarchical Aggregation  [paper](https://arxiv.org/pdf/1804.06055.pdf)
  - Spatial Temporal Graph Convolutional Networks for Skeleton-Based Action Recognition  [paper](https://arxiv.org/pdf/1801.07455.pdf)  [code](https://github.com/yysijie/st-gcn)
- Others
  - ActionVLAD: Learning spatio-temporal aggregation for action classification [paper](https://arxiv.org/pdf/1704.02895.pdf)   [code](https://github.com/rohitgirdhar/ActionVLAD)

  - Appearance-and-Relation Networks for Video Classification  [paper](https://arxiv.org/pdf/1711.09125.pdf)  [code](https://github.com/wanglimin/ARTNet)  
  - ECO: Efficient Convolutional Network for Online Video Understanding [paper](https://arxiv.org/pdf/1804.09066.pdf) [code](https://github.com/mzolfaghari/ECO-efficient-video-understanding)


### skeleton-based action recognition summary

#### 1. Datasets

##### (1). pose estimation

给定图片，设计相关网络学习图片中人物的关键点信息

- [MPII](<http://human-pose.mpi-inf.mpg.de/>)
  - 样本数：25K images，关节点个数：16
  - 全身，单人/多人，40K people，410 human activities
- [MSCOCO](<http://cocodataset.org/#download>)
  - 样本数：>= 30W，关节点个数：18
  - 全身，多人，keypoints on 10W people
- [AI Challenge](<https://challenger.ai/competition/keypoint/subject>)
  - 样本数：21W Training, 3W Validation, 3W Testing，关节点个数：14
  - 全身，多人，38W people

##### (2). 3D activity analysis datasets

相对于只给出RGB视频的kinetics等数据集而言，该类动作数据集还提供了人物的skeleton信息等，各数据集大致对比如下图
![datasets comparison](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/datasets_comparison.jpg)

- NTU RGB+D
  - **目前最大的3d动作识别数据集**
  - 采集了RGB视频, 深度图像序列, skeleton信息(25个关键点的三维位置信息), 红外线视频帧
  - 两种评测体系: cross-subject和cross-view
    - cross-subject：按照不同人物拆分训练集/测试集
    - cross-view：按照摄像头的不同视角拆分训练集/测试集
- SBU Kinect Interaction
  - **每个动作由2个人物执行**
  - 282段视频, 8个动作类别，6822帧, 15个关键点信息
- PKU-MMD
  - 包含RGB视频, 深度图像信息, 红外线帧，skeleton信息
  - 本意是为action detection任务构建的
- SYSU 3D Human-Object Interaction Set (SYSU)
  - 12 个动作类别, 40个人物, 480段视频, 20个关键点
  - 动作之间有较大相似性
- UWA3D Multiview Activity II Dataset (UWA3D)
  - 1075段视频，30个动作, 10个人物, 4种不同的视角(front view, left side view, right side view, top view)
  - 视角的多样性, 自遮挡问题, 动作之间有较大相似性



#### 2. Methods

##### (1). pose estimation methods

##### [1]. [Stacked Hourglass Networks for Human Pose Estimation](https://arxiv.org/pdf/1603.06937.pdf)

- 估计姿势信息需要了解全局，不同关键点之间的连接关系分布在各个尺度上，设计的网络需要同时捕捉到这些不同尺度的特征并做出pixel-wise的预测

  - 重复bottom-up和top-down操作，以及使用中层监督方式

- hourglass网络结构
![hourglass](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/hourglass.jpg)
  - 每次降采样之前，分出上半路保留原尺度信息
  - 每次升采样之后，和上一个尺度的数据相加
  - 两次降采样之间，使用三个Residual模块提取特征
  - 两次相加之间，使用一个Residual模块提取特征
  - 网络的输出为一系列heatmaps，表示关键点在每个像素处存在的可能性

- 整体网络结构由几个hourglass网络堆叠而成，且在每个hourglass模块的输出处都引入监督信息

##### Other References 

https://github.com/handong1587/handong1587.github.io/blob/master/_posts/deep_learning/2015-10-09-pose-estimation.md



##### (2). 3d action recognition

##### 效果概览

| paper id | NTU RGB+D(cs, cv) |  SBU   |
| :------: | :---------------: | :----: |
|    1     |         -         | 90.41% |
|    2     |   73.4%, 81.2%    | 91.51% |
|    3     |  82.89%, 90.10%   |   -    |
|    4     |   89.1%, 94.7%    | 98.3%  |
|    5     |   86.5%, 91.1%    | 98.6%  |
|    6     |   81.5%, 88.3%    |   -    |

**some conclusion：**

(1). cv准则下的效果比cs准则下的效果好，这表明：不同人物在执行相同动作时的差异性比不同视角的差异性要大，即intra-class differences问题，这在其他任务上也比较常见

(2). 基于LSTM的效果不如基于CNN的效果


**some preview:**

- 基于LSTM的动作识别框架：
  - 每一帧的关键点信息(展成一个特定长度的向量，关键点个数*关键点维度)送入LSTM，之后再在时序上对LSTM的输出做融合
- 基于CNN的动作识别框架
  - 将skeleton序列表示为一张大小为(**序列长度, 关键点个数, 关键点维度**)的图像，如一段帧数为32且每帧包含16个关键点的二维信息的序列可以表示为(32, 16, 2)的tensor

##### [1]. [Co-occurrence Feature Learning for Skeleton based Action Recognition using Regularized Deep LSTM Networks](https://arxiv.org/pdf/1603.07772.pdf)

- 什么是**Co-occurrence**

  - 人的某个行为动作常常和骨架的一些特定关节点构成的集合，以及这个集合中节点的交互密切相关。如要判别是否在打电话，关节点“手腕”、“手肘”、“肩膀”和“头”的动作最为关键。不同的行为动作与之密切相关的节点集合有所不同，如对于“走路”的行为动作，“脚腕”、“膝盖”、“臀部”等关节点构成具有判别力的节点集合。**我们将这种几个关节点同时影响和决定判别的特性称为共现性（Co-occurrence）**

- 对LSTM网络的改进1：

  - 学习Co-occurrence，将LSTM每一层的N个神经元分为K个组，每组的神经元共同负责一类或多类动作的判别力强的关键点集合，它们共同地和某些关节点有更大的连接权值，而和其他关节点有较小的连接权值

    如下图所示，第k组的神经元都只和某些节点有连接
![1_1](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/1_1.jpg)
  - 如何实现分组神经元：
![1_2](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/1_2.jpg)
    - 在损失函数里增加co-occurrence正则项，$W_{x\beta, k}$表示第k组神经元的参数矩阵，若它能学到某些动作的关键点连接模式，那它将会是column sparse的，即loss最小

- 对LSTM网络的改进2：

  - 对最后一个LSTM的每个门都引入dropout，可以学习更好的参数

- 整体网络结构如下图所示

  ![1_3](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/1_3.jpg)

  - 网络由3个双向LSTM和2个fc层组成，co-occurrence learning均在第2个lstm网络之前完成

    

##### [2]. [An End-to-End Spatio-Temporal Attention Model for Human Action Recognition from Skeleton Data](https://arxiv.org/pdf/1611.06067.pdf)

- 出发点：

  - 对于每个动作而言，每一帧能提供的可判别信息不同，每一帧中的每个关键点的重要性也不同

- 对LSTM网络**引入注意力机制**

  - 引入joint-selection gates(关键点选择门)实现空间注意力机制，会自动选择判别能力强的关键点
  - 引入frame-selection gates(帧选择门)实现时序注意力机制，对每一帧的重要性分配不同的权重

- 整体网络结构如下图所示

![2_1](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/2_1.jpg)

  - 输入t时刻的关键点信息，经空间注意力机制模块后，每个关键点的信息被空间权重$\alpha$调制，后送入基本的LSTM分类网络，时序注意力机制模块输出的时序权重$$\beta$$加权不同时刻的LSTM输出

- 空间注意力机制模块

  - 网络如上图所示，由一个LSTM层，两个全连接层，一个归一化单元(即softmax函数)组成

  - 该模块的输出为：第k个关键点重要性的得分：

![2_2](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/2_2.jpg)

  - 归一化上述得分即可得到每个关键点的权重$\alpha$

- 时序注意力机制

  - 网络如上图所示，由一个LSTM层，两个全连接层，一个ReLU单元组成

  - 序列的类别得分由所有时刻的得分通过时序权重$\beta$加权得到

![2_3](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/2_3.jpg)

- 联合空间/时序注意力机制

  - 带正则的损失函数

![2_4](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/2_4.jpg)

	- 第一个正则项促使空间注意力机制去动态地关注更多关键点，而不是病态地忽略很多关键点
	- 第二个正则项可防止梯度消失
	- 第三个正则项可防止网络过拟合

##### [3]. [SKELETON-BASED ACTION RECOGNITION USING LSTM AND CNN](https://arxiv.org/pdf/1707.02356.pdf)

- 基于CNN的方法将skeleton序列表示为图片难免会损失时序信息，所以希望和lstm相辅相成

- SPF即Spatial-domain-feature作为LSTM的输入

  - 包括：R (relative position), J (distances between joints) and L (distances between joints and lines)

  - 关键点j和k之间的相对位置表示为：

    ![3_1](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/3_1.jpg)

  - 关键点j和k之间的欧式距离表示为：
![3_2](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/3_2.jpg)

  - 关键点n到线jk的距离表示为：
![3_3](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/3_3.jpg)

- TPF即temporal-domain-feature作为CNN的输入

  - 包括：joint distances map (JDM) and joint trajectories map (JTM)

  - 具体的特征提取过程详见references：

    https://arxiv.org/pdf/1611.02447.pdf

    http://cvlab.cse.msu.edu/pdfs/Zhang_Liu_Xiao_WACV2017.pdf

- 计算好上述提到的特征后，分别送入相应的lstm网络(3个)和cnn网络(7个)

  - lstm有3层，cnn网络是AlexNet的结构
![3_4](.https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/3_4.jpg)

##### [4]. [View Adaptive Neural Networks for High Performance Skeleton-based Human Action Recognition](https://arxiv.org/pdf/1804.07453.pdf)

- 现实场景中常包含多种不同视角的摄像头，而同一个动作在不同视角下存在着较大差异。这篇论文主要解决diversity of view的问题，学习到最合适的observation viewpoints，并将原始skeleton信息转换到此坐标系下的信息，之后再通过rnn或cnn进行动作分类，这样可以尽可能减少不同视角带来的影响

  - global coordinate system O
  - observation coordinate system $$O'_{t}$$
  - O坐标系的关键点$$v_{t,j} = [x_{t,j} , y_{t,j} , z_{t,j}]^T$$在$$O'_{t}$$坐标系下相对应的关键点为：

![4_1](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/4_1.jpg)

  其中，**R**为旋转矩阵，由网络学出的旋转参数${\alpha, \beta, \gamma}$构建得到，**d**为平移参数

  - 下图是更形象的表示：

![4_2](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/4_2.jpg)

- 基于RNN的视角自适应网络，View Adaptive Recurrent Neural Network (VA-RNN)

  ![4_3](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/4_3.jpg)

  - View Adaptation subnetwork的两个分支都由LSTM和fc层构成，分别学习转换矩阵的旋转参数${\alpha, \beta, \gamma}$和平移参数$d$

    下式的$h_t$表示第t时刻LSTM层的输出

    ![4_4](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/4_4.jpg)

    ![4_5](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/4_5.jpg)

- 基于CNN的视角自适应网络，View Adaptive Convolution Neural Network (VA- CNN)

  ![4_6](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/4_6.jpg)

  - 不再像rnn那样对每一帧都学习旋转参数，cnn对整个序列学习一致的旋转参数
  - View Adaptation subnetwork由2个卷积层和1个fc层构成，卷积层的配置均为：channels=128, kernel size=5, stride=2
  - main convet是ResNet-50

##### [5]. [Co-occurrence Feature Learning from Skeleton Data for Action Recognition and Detection with Hierarchical Aggregation](https://arxiv.org/pdf/1804.06055.pdf)

- 之前基于CNN的常规做法是将关键点维度信息放在channel维度，这样可以学到相互独立的point-level feature，但是更希望发掘不同关键点之间的关系，希望根据所有关键点学到一种全局响应，毕竟action是由所有关键点共同作用而成，那么此时就可以**将关键点放在channel维度****(卷积层的输出是所有输入通道的全局响应)**

- 整体网络结构如下图所示：

  - 输入固定大小的tensor，前2个卷积层学到point-level feature(关键点所在维度的核大小始终为1)，后将关键点置换到channel维度，再经过2个卷积层学习所有关键点的全局响应
  - 同时利用skeleton motion信息(简单地定义为两帧之间的skeleton信息的差)，motion信息的处理类似上述步骤
  - skeleton和skeleton motion信息各经过4个卷积层后concat到一起，再经过一些卷积层和全连接层后得到最终的分类结果

  ![5_1](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/5_1.jpg)

##### [6]. [Spatial Temporal Graph Convolutional Networks for Skeleton-Based Action Recognition](https://arxiv.org/pdf/1801.07455.pdf)

- 如何理解图卷积GCN：移步https://www.zhihu.com/question/54504471/answer/153095639

- 时空图的构造

  ![6_1](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/6_1.jpg)

  - 所有帧的全部关键点组成图的节点集合V
  - 同一帧的不同相连关键点之间建立spatial edges
  - 不同帧的同一关键点之间建立temporal edges

- Spatial Graph Convolutional Neural Network

  - 回顾二维的卷积操作，在空间位置x处的输出值取决于x的邻域点集合及在每一点上的权重大小(滤波器参数)

    ![6_2](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/6_2.jpg)

  - 类比卷积操作的实现，GCN需要做两件事

    - 定义采样函数：与根节点的距离小于D的节点构成邻域集合

    - 定义权重函数：CNN的邻域节点个数是固定的，而GCN的邻域节点个数不固定，所以需要将邻域划分为K个子集，属于该子集的所有节点拥有相同的权重大小

    - 具体公式如下

      ![6_3](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/6_3.jpg)

      其中$$B(v_{ti})$$表示$$v_{ti}$$的邻域集合，$$l_{ti}(v_{tj})$$表示该邻域节点$$v_{tj}$$的权重大小

  - 同理，空域GCN可推广到时空GCN(ST-GCN)，若两帧的时序距离小于$T/2$，则可认为属于这两帧的同一个关键点是邻域点

- 如何将邻域划分为K个子集

  - Uni-labeling
    - 邻域集合中的每个点拥有相同的权重
    - 会损失局部不同的信息
  - Distance partitioning
    - K=2，第一个子集只有根结点，第二个子集是其它邻域节点
  - Spatial configuration partitioning
    - 所有关键点的中心称为“重心”
    - K=3，第一个子集只有根结点，第二个子集是那些到根结点的距离比到重心的距离远的节点，第三个子集是剩下的节点

  三种不同的划分方式分别如下图b, c, d所示：

  ![6_4](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/6_4.jpg)

- 实现ST-GCN

  - 每一帧的关键点之间的连接方式通过邻接矩阵**A**和单位矩阵**I**表示

  - 根据上述公式，输出可表示为：

    ![6_5](https://github.com/shuangshuangguo/skeleton-based-action-recognition-review/raw/master/sources/6_5.jpg)

##### Other references：

1. [A new representation of skeleton sequences for 3d action recognition](https://arxiv.org/pdf/1703.03492.pdf)

2. [SKELETON-BASED ACTION RECOGNITION WITH CONVOLUTIONAL NEURAL NETWORKS](https://arxiv.org/pdf/1704.07595.pdf)

