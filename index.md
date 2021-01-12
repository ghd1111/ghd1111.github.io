
##  Online learning and online convex optimization

### 一、Online learning

- offLine learning 离线学习
- **online learning**在线学习

一种模型的训练方法，能够根据线上反馈的数据，实时快速的进行模型调整，使得模型及时反映线上的变化，提高线上预测的准确率。Online Learning的流程包括：将模型的预测结果展现给用户，然后收集用户的反馈数据，再用来训练模型，形成闭环的系统。

根据线上预测的结果动态地调整模型。最终的目标是使整体的损失最小。（每次预测后，其结果用来更新修正预测模型，用于对以后数据进行更好的预测）

在线学习算法强调的是训练的实时性，面向流式数据，每次训练不使用全量数据，而是以之前训练好的参数为基础，每次利用一个样本更新一次模型，是属于增量学习的一部分，从而快速更新模型，提高模型的时效性。



#### introduction

在线学习是在一系列连续的回合（round) 中进行的；

在回合![img](http://latex.codecogs.com/gif.latex?t),学习机（learner）被给一个question：![img](http://latex.codecogs.com/gif.latex?%7Bx_t%7D)(一个向量，即为特征向量),![img](http://latex.codecogs.com/gif.latex?%7Bx_t%7D)为从instance domain：![img](http://latex.codecogs.com/gif.latex?%5Cchi)采样得到的。学习机给出一个预测值：![img](http://latex.codecogs.com/gif.latex?%7Bp_t%7D)，然后得到正确的答案：![img](http://latex.codecogs.com/gif.latex?%7By_t%7D)，![img](http://latex.codecogs.com/gif.latex?%7By_t%7D)从target domain：![img](http://latex.codecogs.com/gif.latex?%7B%5Crm%7BY%7D%7D)采样得到，定义损失函数为![img](http://latex.codecogs.com/gif.latex?l(%7Bp_t%7D,%7By_t%7D))。定义损失函数为![img](http://latex.codecogs.com/gif.latex?l(%7Bp_t%7D,%7By_t%7D))。在大多数情况下，![img](http://latex.codecogs.com/gif.latex?%7Bp_t%7D)在![img](http://latex.codecogs.com/gif.latex?%7B%5Crm%7BY%7D%7D)中，但是，允许学习者从更大的集合中选择预测有时很方便，我们用D表示prediction domain。

学习框架：

![image-20191123203548538](https://tva1.sinaimg.cn/large/008eGmZEly1gml7jhk55aj30kd088gm8.jpg)

**学习机的最终目标就是随着运行最小化累计损失**，也就是产生尽可能小的预测差错，努力去从原来的回合中提取信息然后去提高它对当下和未来问题的更好的预测能力。



#### two restrictions

两个限制条件

- 限制一，适合于在线分类的情况

假设所有的answer都是由一些target mapping生成的:![img](http://latex.codecogs.com/gif.latex?%7Bh%5E%20*%20%7D:%5Cchi%20%20%5Cto%20Y)，![img](http://latex.codecogs.com/gif.latex?%7Bh%5E%20*%20%7D)取自固定集合，称为假设类，由H表示，这是学习者已知的。由于这种对序列的限制，我们称之为realizable case，学习者应该尽可能少犯错误，假设![img](http://latex.codecogs.com/gif.latex?%7Bh%5E%20*%20%7D)和问题的顺序可以由对手来选择。对于在线学习算法A，我们用![img](http://latex.codecogs.com/gif.latex?%7BM_A%7D%5Cleft(%20H%20%5Cright))表示A在一系列用![img](http://latex.codecogs.com/gif.latex?%7Bh%5E%20*%20%7D%20%5Cin%20H)标记的例子上可能犯的最大错误数。我们再次强调![img](http://latex.codecogs.com/gif.latex?%7Bh%5E%20*%20%7D)和问题的顺序可以由对手来选择。![img](http://latex.codecogs.com/gif.latex?%7BM_A%7D%5Cleft(%20H%20%5Cright))的上界称为mistake bound，我们将研究如何设计![img](http://latex.codecogs.com/gif.latex?%7BM_A%7D%5Cleft(%20H%20%5Cright))最小的算法。

- 限制二，relaxation of the realizable assumption

我们不再假设所有答案都是由![img](http://latex.codecogs.com/gif.latex?%7Bh%5E%20*%20%7D%20%5Cin%20H)产生，但是我们要求学习机与来自H最好的固定预测器竞争。这被算法的regret所捕获，regret度量了回顾过去，学习机有多“抱歉”没有遵循一些假设![img](http://latex.codecogs.com/gif.latex?%7Bh%5E%20*%20%7D%20%5Cin%20H)的预测。形式上，算法相对于![img](http://latex.codecogs.com/gif.latex?%7Bh%5E%20*%20%7D)的regret当在一系列T实例上运行时定义为：

![image-20191123204125175](https://tva1.sinaimg.cn/large/008eGmZEly1gml7hqc2rtj30kv037jri.jpg)

算法相对于假设类H的regret是

![image-20191123204140373](https://tva1.sinaimg.cn/large/008eGmZEly1gml7i4nqrnj30id0220sr.jpg)

学习机的目标是相对于H具有尽可能低的regret。我们有时会对“low regret”算法感到满意，我们认为与轮数T呈次线性增长，其意味着当T变为无穷大时，学习机的平均损失与H中最佳假设的平均损失之间的差异趋于零。

次线性增长：![img](http://latex.codecogs.com/gif.latex?%5Cmathop%20%7B%5Clim%20%7D%5Climits_%7BT%20%5Cto%20%5Cinfty%20%7D%20%5Cfrac%7B%7B%7B%5Cmathop%7B%5Crm%20Re%7D%5Cnolimits%7D%20gre%7Bt_T%7D%7D%7D%7BT%7D%20=%200)



#### examples

- 在线回归（online regression)
- 专家建议预测（prediction with expert advice)
- 在线排名（online ranking)



#### online classification prblem

**在线分类问题**

为了回复Cover's impossibility result,我们进一步限制对抗环境的能力，下面有两种方法。

- 方法一：增加额外的假设

  - **Consistent 算法**

    - Consistent算法维持一个与![image-20191123204233399](https://tva1.sinaimg.cn/large/008eGmZEly1gml7hrtcb5j308801b745.jpg)一致的所有假设的集合![img](http://latex.codecogs.com/gif.latex?%7BV_t%7D)。此集合通常称为version space。然后它从中选择任何假设并根据该假设进行预测。

    ![image-20191123205424772](https://tva1.sinaimg.cn/large/008eGmZEly1gml7indhcbj30et08eq3k.jpg)

    - Conisstent算法的mistake bound

    ![image-20191123205437826](https://tva1.sinaimg.cn/large/008eGmZEly1gml7i77xjjj30i902gq3a.jpg)

    

  - **Halving算法**

    - 根据Vt中大部分的假设做预测，而不是根据Vt中的一些假设做出武断的预测。这样的话，就会保证无论何时都会移去verson space 中至少一半的假设。

    ![image-20191123205239540](https://tva1.sinaimg.cn/large/008eGmZEly1gml7inm558j30f1088aas.jpg)

    - Halving算法的mistake bound

    ![image-20191123205256085](https://tva1.sinaimg.cn/large/008eGmZEly1gml7itw93yj30ie02laaf.jpg)



- 方法二：随机化（randomization)
  - 凸性是推导在线学习算法的中心主题





### 二、online convex optimization

![image-20191123205143764](https://tva1.sinaimg.cn/large/008eGmZEly1gml7jiyri7j30et062aai.jpg)

凸集的定义：![img](https://images2018.cnblogs.com/blog/1473673/201809/1473673-20180901105532105-1429571368.png)

一个向量u的Regret定义为

![image-20191123205158466](https://tva1.sinaimg.cn/large/008eGmZEly1gml7hwriawj30dz029weg.jpg)

算法相对于竞争向量的集合U的Regret被定义为

![image-20191123205214549](https://tva1.sinaimg.cn/large/008eGmZEly1gml7ixv32rj308001jt8l.jpg)



#### convexification by randomization

#### 两种凸化技术

- 通过随机凸化

考虑**专家建议的预测问题**：每个在线回合中，学习者必须从d位给定专家的建议中进行选择。![img](http://latex.codecogs.com/gif.latex?%7Bp_t%7D%20%5Cin%20%5C%7B%201,2,...,d%5C%7D)表示选到的专家，然后学习机收到一个向量![img](http://latex.codecogs.com/gif.latex?%7By_t%7D%20%5Cin%20%7B%5B0,1%5D%5Ed%7D)，其中![img](http://latex.codecogs.com/gif.latex?%7By_t%7D%5Bi%5D)表示听从第![img](http://latex.codecogs.com/gif.latex?i)个专家的建议所遭受的损失，学习机需要支付的损失为![img](http://latex.codecogs.com/gif.latex?%7By_t%7D%5B%7Bp_t%7D%5D)。在这种情况下，decision space是离散的，因此非凸。

有限假设类（finite hypothesis class）的在线分类问题可以很容易地作为具有专家建议问题的预测的特例。 因此，Cover’s impossibility result意味着没有算法可以通过专家建议问题获得预测的low Regret。

然而，正如我们在下面所示，通过允许学习者随机化他的预测，我们可以将问题转化为在线凸优化框架，因此可以获得针对该问题的low Regret算法。令![img](http://latex.codecogs.com/gif.latex?S%20=%20%5C%7B%20w%20%5Cin%20%7BR%5Ed%7D:w%20%5Cge%200%20%5Cwedge%20%7B%5Cleft%5C%7C%20w%20%5Cright%5C%7C_1%7D%20=%201%5C%7D)是probability simplex，S是一个凸集。

在第 t回合，学习者选择![image-20191123204613430](https://tva1.sinaimg.cn/large/008eGmZEly1gml7hu3uccj302o0163ya.jpg)，并且基于![image-20191123204627255](https://tva1.sinaimg.cn/large/008eGmZEly1gml7irt6qxj301b016a9t.jpg)根据![image-20191123204651015](https://tva1.sinaimg.cn/large/008eGmZEly1gml7hsakv1j305r01bq2r.jpg)随机抽取一个专家，学习机支付期望损失：![image-20191123204704902](https://tva1.sinaimg.cn/large/008eGmZEly1gml7jgogtzj30d002mjre.jpg)

- Convexification by Surrogate Loss Functionsx'z

  通过替代损失函数凸化

从有限假设类的在线分类具体问题开始，回想一下，我们用来回避 Cover’s impossibility result的技术之一依赖于可实现性假设（realizability assumption）。我们假设存在![img](http://latex.codecogs.com/gif.latex?%7Bh%5E%20*%20%7D%20%5Cin%20H)使得对于所有的t有![img](http://latex.codecogs.com/gif.latex?%7By_t%7D%20=%20%7Bh%5E%20*%20%7D(%7Bx_t%7D))。有了这个假设，我们描述了Halving算法并且表明它最多![img](http://latex.codecogs.com/gif.latex?%7B%5Clog%20_2%7D(%5Cleft%7C%20H%20%5Cright%7C))个预测错误。我们现在使用在线凸优化语言得出类似的保证:

![img](https://images2018.cnblogs.com/blog/1473673/201809/1473673-20180901160119316-575003312.png)

![img](https://images2018.cnblogs.com/blog/1473673/201809/1473673-20180901160322834-680294912.png)



S是一个凸集，![img](http://latex.codecogs.com/gif.latex?%7Bf_t%7D)对于所有t是一个凸函数，我们转化得到一个在线凸优化问题。

接下来的部分中，我们将推导出在线凸优化问题的算法。 特别是，这些算法之一具有如下的regret bound：

![image-20191123205003157](https://tva1.sinaimg.cn/large/008eGmZEly1gml7hqywiaj30do02hmx7.jpg)

其中，![img](http://latex.codecogs.com/gif.latex?%5Ceta)是一个参数，在这里设置为1/4，![img](http://latex.codecogs.com/gif.latex?%7BL_t%7D)是函数![img](http://latex.codecogs.com/gif.latex?%7Bf_t%7D)关于L1范数的Lipschitz参数。在我们的案例中，

![image-20191123205015749](https://tva1.sinaimg.cn/large/008eGmZEly1gml7jfqh43j30d9021weh.jpg)

通过![img](http://latex.codecogs.com/gif.latex?%7Bf_t%7D)的 surrogate property，我们获得：

![image-20191123205029238](https://tva1.sinaimg.cn/large/008eGmZEly1gml7i21f8oj3072023wed.jpg)

这种类型的界限，其中错误的数量受到 competing hypothesis的convex surrogate loss的上限，通常被称为relative loss bound。

在realizable的情况下，我们可以进一步简化 relative loss bound如下。 由于bound适用于所有u∈S，因此它特别适用于向量u=(0，...，0,1,0，...，0)，其中1位于对应于 true hypothesis ![img](http://latex.codecogs.com/gif.latex?%7Bh%5E%20*%20%7D%20%5Cin%20H)的位置。

通过我们的构造，对于所有t，![image-20191123205053796](https://tva1.sinaimg.cn/large/008eGmZEly1gml7issrbpj302h00u3ya.jpg)，产生![image-20191123205104100](https://tva1.sinaimg.cn/large/008eGmZEly1gml7jilambj303y0193yb.jpg)



#### FTL算法

**follow-the-leader**

最自然的学习规则是使用任何在过去回合中损失最小的向量。这与Consistent算法的精神相同，它在在线凸优化中通常被称为Follow-The-Leader，**最小化累积损失**。

![image-20191123205630757](https://tva1.sinaimg.cn/large/008eGmZEly1gml7jh3qp2j30et03sq33.jpg)

![image-20191123205641235](https://tva1.sinaimg.cn/large/008eGmZEly1gml7hpq33cj30ie04ewf0.jpg)

采用数学归纳法证明如下：

将上述不等式的两边同时减去![image-20191123205703109](https://tva1.sinaimg.cn/large/008eGmZEly1gml7iwkw9dj302u015jr6.jpg)，得到![image-20191123205724580](https://tva1.sinaimg.cn/large/008eGmZEly1gml7hurjxqj3069023t8l.jpg)

当T = 1时，由定义代入上述不等式成立。

假设不等式支持T-1，得![image-20191123205806726](https://tva1.sinaimg.cn/large/008eGmZEly1gml7iyxkbaj306j02djra.jpg)，对不等式两边同时加上![image-20191123205824900](https://tva1.sinaimg.cn/large/008eGmZEly1gml7hv3q6bj302n014742.jpg)，得

![image-20191123205838825](https://tva1.sinaimg.cn/large/008eGmZEly1gml7hrck6dj309k02c748.jpg)，该不等式支持所有的u,特别是![image-20191123205857236](https://tva1.sinaimg.cn/large/008eGmZEly1gml7iol3hbj302q00wwe9.jpg)，得

![image-20191123205928996](https://tva1.sinaimg.cn/large/008eGmZEly1gml7isczhqj309n0293yg.jpg)，根据定义，![image-20191123210006707](https://tva1.sinaimg.cn/large/008eGmZEly1gml7htmvdcj302n00uq2p.jpg),因此得到

![image-20191123210017284](https://tva1.sinaimg.cn/large/008eGmZEly1gml7ivm8gqj30bg02cweh.jpg)

- 例子1：Online Quadratic Optimization

![image-20191123210047347](https://tva1.sinaimg.cn/large/008eGmZEly1gml7jg6yv7j30i302yjrv.jpg)

- failure of FTL

![img](https://tva1.sinaimg.cn/large/008eGmZEly1gml7irbxp9j31120f3n0o.jpg)



#### FTRL算法

**follow-the-regularized-leader**

通过在FTL的基础上再加一个正则式来使他更稳定

![image-20191124132821897](https://tva1.sinaimg.cn/large/008eGmZEly1gml7iqiaulj30ey041mxd.jpg)

![image-20191124133029913](https://tva1.sinaimg.cn/large/008eGmZEly1gml7i0mipgj30ig04j74u.jpg)

![image-20191124133102971](https://tva1.sinaimg.cn/large/008eGmZEly1gml7io4afuj30xi0hlgoa.jpg)



#### OGD在线梯度下降

线性化凸函数

带有欧几里得正则式的FTRL算法

![image-20191124133258137](https://tva1.sinaimg.cn/large/008eGmZEly1gml7jjl07vj30ew04ewet.jpg)

![image-20191124133336104](https://tva1.sinaimg.cn/large/008eGmZEly1gml7hzpluuj30ai02ht8n.jpg)

- 在线梯度下降算法的来源

![image-20191124133414408](https://tva1.sinaimg.cn/large/008eGmZEly1gml7i6rnckj30id09nwg1.jpg)

![image-20191124133438667](https://tva1.sinaimg.cn/large/008eGmZEly1gml7it9nnhj312f0hmn03.jpg)

- **凸函数**以及**次梯度**的概念

  满足一下式子的函数为凸函数，其中，z若不唯一，为此凸函数的次梯度；z若唯一，为此凸函数的梯度。

  ![image-20191124133552633](https://tva1.sinaimg.cn/large/008eGmZEly1gml7i6a6j4j30ih03w74p.jpg)

  ![image-20191124133758281](https://tva1.sinaimg.cn/large/008eGmZEly1gml7jkywe0j30xx0aedh2.jpg)

  ![image-20191124133942232](https://tva1.sinaimg.cn/large/008eGmZEly1gml7i5seiej307r01fq2s.jpg)

![image-20191124133905560](https://tva1.sinaimg.cn/large/008eGmZEly1gml7ji6yxyj30c2027weg.jpg)



#### 强凸正则化

- 强凸的概念

![image-20191124134053624](https://tva1.sinaimg.cn/large/008eGmZEly1gml7i3011xj30id03x74t.jpg)

![image-20191124134122298](https://tva1.sinaimg.cn/large/008eGmZEly1gml7i2nof7j30sd0b7jsl.jpg)

- 欧几里得正则化

![image-20191124134141768](https://tva1.sinaimg.cn/large/008eGmZEly1gml7i0a3jej30if0390tf.jpg)

- Entropic 正则化

![image-20191124134156808](https://tva1.sinaimg.cn/large/008eGmZEly1gml7iozx8zj30i80aotaq.jpg)



#### OMD在线镜像下降

![image-20191124134334520](https://tva1.sinaimg.cn/large/008eGmZEly1gml7iwzt3zj30ew0620t8.jpg)

- 算法由来：凸函数为线性函数的FRTL算法

![image-20191124134355320](https://tva1.sinaimg.cn/large/008eGmZEly1gml7iuka1oj30e80dkwf6.jpg)

- 当![image-20191124134546873](https://tva1.sinaimg.cn/large/008eGmZEly1gml7i178xsj306m00uglf.jpg)时，在线镜像下降为在线梯度下降。在线梯度下降为在线镜像下降的最简形式。
- 悔界

![image-20191124134742267](https://tva1.sinaimg.cn/large/008eGmZEly1gml7jf4yfxj314c0jfn19.jpg)





### 三、online calssification

#### Weighted Majority √

![image-20191124135002599](https://tva1.sinaimg.cn/large/008eGmZEly1gml7iw49doj30ha0740tk.jpg)

#### SOA标准最优化算法

##### H shattered tree

![image-20191124135116939](https://tva1.sinaimg.cn/large/008eGmZEly1gml7iv5js5j30xa0gqjuu.jpg)

- 概念

![image-20191124135239367](https://tva1.sinaimg.cn/large/008eGmZEly1gml7izc4i5j30ia03x752.jpg)

![image-20191124135304424](https://tva1.sinaimg.cn/large/008eGmZEly1gml7ixffm3j30op08wq3h.jpg)

##### Ldim

- 概念

![image-20191124135426743](https://tva1.sinaimg.cn/large/008eGmZEly1gml7hvjxpnj30ii02cmxk.jpg)

- 重要性质

![image-20191124135458730](https://tva1.sinaimg.cn/large/008eGmZEly1gml7jk81ecj30ru0d4abo.jpg)

##### SOA算法

![image-20191124135602128](https://tva1.sinaimg.cn/large/008eGmZEly1gml7i1mborj30el093759.jpg)

- 重要定理

![image-20191124135745670](https://tva1.sinaimg.cn/large/008eGmZEly1gml7hsqul6j30ik01pjri.jpg)

![image-20191124135639296](https://tva1.sinaimg.cn/large/008eGmZEly1gml7hxflmsj30fw0i5gn7.jpg)



#### Percepron and Winnnow √√



### 四、Limited feedback (bandits)

在这个在线学习问题中，学习机知道它预测的向量的损失函数，但它不知道在其他点的损失函数。

**MAB：这里和专家建议预测问题相似，但是在每一轮，学习机只能观察到它选择的专家的代价而不能观察到其他专家的代价。**

#### 估计梯度的在线镜像下降

这里![image-20191125091846806](https://tva1.sinaimg.cn/large/008eGmZEly1gml7izvyqoj301200w741.jpg)不再表示损失函数的次梯度，而是一个随机向量，![image-20191125091905733](https://tva1.sinaimg.cn/large/008eGmZEly1gml7hyd0mmj305k016wec.jpg)。

![image-20191125091950107](https://tva1.sinaimg.cn/large/008eGmZEly1gml7hyr8unj30g407jaas.jpg)

![image-20191125092213932](https://tva1.sinaimg.cn/large/008eGmZEly1gml7i3havdj30ig0b60uh.jpg)

​         

#### The multi-armed bandit problem

有d个手臂，在每个在线回合学习机应该选择其中一个手臂，用pt表示，被选择的手臂可以是一个随机变量。然后他收到一个选择这个手臂的代价，![image-20191125094132666](https://tva1.sinaimg.cn/large/008eGmZEly1gml7i3x2sfj303a00tmwy.jpg) ，向量![image-20191125094225467](https://tva1.sinaimg.cn/large/008eGmZEly1gml7hz9outj302o00q742.jpg) 和每一个手臂的代价相联系，但是学习机只能看到他推动的那个手臂的代价。              

这个问题就和专家建议预测问题相似。唯一的不同就是学习机不知道他没选择的那些专家的代价。学习机的目标就是产生尽可能的后悔。                                                      

这个问题可以看作是**利用-开发的权衡**(exploit-explore  EE问题）。一方面，我们可能更愿意选择那些依靠以前回合的经验有最低的代价的手臂（利用我们所知道的最好的选择）；另一方面，也许最好应该探索其他的有更小代价的手臂（冒一些风险去收集未知的信息），最好的长期战略可能包括短期的牺牲。例如，一次探索试验可能完全失败，但他警告我们将来不要频繁地采取这种行动。

![image-20191125101313816](https://tva1.sinaimg.cn/large/008eGmZEly1gml7iycqtjj30ep09cmy1.jpg)

![image-20191125101256409](https://tva1.sinaimg.cn/large/008eGmZEly1gml7iqxl8bj30il063gmd.jpg)



#### Gradient descent without a gradient

我们只有一个访问损失函数的黑盒，不能直接计算出次梯度（上一章的多臂老虎机问题是一个特例，他的损失函数是线性的）。

![image-20191125105440695](https://tva1.sinaimg.cn/large/008eGmZEly1gml7ipgbg7j30ez08cmy0.jpg)
=======