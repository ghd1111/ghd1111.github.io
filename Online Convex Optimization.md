------

------

## Online learning and online convex optimization

### 一、Online learning

* offLine learning 离线学习
* **online learning**在线学习

一种模型的训练方法，能够根据线上反馈的数据，实时快速的进行模型调整，使得模型及时反映线上的变化，提高线上预测的准确率。Online Learning的流程包括：将模型的预测结果展现给用户，然后收集用户的反馈数据，再用来训练模型，形成闭环的系统。

根据线上预测的结果动态地调整模型。最终的目标是使整体的损失最小。（每次预测后，其结果用来更新修正预测模型，用于对以后数据进行更好的预测）

在线学习算法强调的是训练的实时性，面向流式数据，每次训练不使用全量数据，而是以之前训练好的参数为基础，每次利用一个样本更新一次模型，是属于增量学习的一部分，从而快速更新模型，提高模型的时效性。



------

#### introduction

在线学习是在一系列连续的回合（round) 中进行的；

在回合![img](http://latex.codecogs.com/gif.latex?t),学习机（learner）被给一个question：![img](http://latex.codecogs.com/gif.latex?{x_t})(一个向量，即为特征向量),![img](http://latex.codecogs.com/gif.latex?{x_t})为从instance domain：![img](http://latex.codecogs.com/gif.latex?\chi)采样得到的。学习机给出一个预测值：![img](http://latex.codecogs.com/gif.latex?{p_t})，然后得到正确的答案：![img](http://latex.codecogs.com/gif.latex?{y_t})，![img](http://latex.codecogs.com/gif.latex?{y_t})从target domain：![img](http://latex.codecogs.com/gif.latex?{\rm{Y}})采样得到，定义损失函数为![img](http://latex.codecogs.com/gif.latex?l({p_t},{y_t}))。定义损失函数为![img](http://latex.codecogs.com/gif.latex?l({p_t},{y_t}))。在大多数情况下，![img](http://latex.codecogs.com/gif.latex?{p_t})在![img](http://latex.codecogs.com/gif.latex?{\rm{Y}})中，但是，允许学习者从更大的集合中选择预测有时很方便，我们用D表示prediction domain。

学习框架：

<img src="Online Convex Optimization.assets/image-20191123203548538.png" alt="image-20191123203548538" style="zoom:80%;" />

**学习机的最终目标就是随着运行最小化累计损失**，也就是产生尽可能小的预测差错，努力去从原来的回合中提取信息然后去提高它对当下和未来问题的更好的预测能力。



------

#### two restrictions

两个限制条件

* 限制一，适合于在线分类的情况

假设所有的answer都是由一些target mapping生成的:![img](http://latex.codecogs.com/gif.latex?{h^%20*%20}:\chi%20%20\to%20Y)，![img](http://latex.codecogs.com/gif.latex?{h^%20*%20})取自固定集合，称为假设类，由H表示，这是学习者已知的。由于这种对序列的限制，我们称之为realizable case，学习者应该尽可能少犯错误，假设![img](http://latex.codecogs.com/gif.latex?{h^%20*%20})和问题的顺序可以由对手来选择。对于在线学习算法A，我们用![img](http://latex.codecogs.com/gif.latex?{M_A}\left(%20H%20\right))表示A在一系列用![img](http://latex.codecogs.com/gif.latex?{h^%20*%20}%20\in%20H)标记的例子上可能犯的最大错误数。我们再次强调![img](http://latex.codecogs.com/gif.latex?{h^%20*%20})和问题的顺序可以由对手来选择。![img](http://latex.codecogs.com/gif.latex?{M_A}\left(%20H%20\right))的上界称为mistake bound，我们将研究如何设计![img](http://latex.codecogs.com/gif.latex?{M_A}\left(%20H%20\right))最小的算法。

* 限制二，relaxation of the realizable assumption

我们不再假设所有答案都是由![img](http://latex.codecogs.com/gif.latex?{h^%20*%20}%20\in%20H)产生，但是我们要求学习机与来自H最好的固定预测器竞争。这被算法的regret所捕获，regret度量了回顾过去，学习机有多“抱歉”没有遵循一些假设![img](http://latex.codecogs.com/gif.latex?{h^%20*%20}%20\in%20H)的预测。形式上，算法相对于![img](http://latex.codecogs.com/gif.latex?{h^%20*%20})的regret当在一系列T实例上运行时定义为：

<img src="Online Convex Optimization.assets/image-20191123204125175.png" alt="image-20191123204125175" style="zoom:80%;" />

算法相对于假设类H的regret是

<img src="Online Convex Optimization.assets/image-20191123204140373.png" alt="image-20191123204140373" style="zoom:80%;" />

学习机的目标是相对于H具有尽可能低的regret。我们有时会对“low regret”算法感到满意，我们认为与轮数T呈次线性增长，其意味着当T变为无穷大时，学习机的平均损失与H中最佳假设的平均损失之间的差异趋于零。

次线性增长：![img](http://latex.codecogs.com/gif.latex?\mathop%20{\lim%20}\limits_{T%20\to%20\infty%20}%20\frac{{{\mathop{\rm%20Re}\nolimits}%20gre{t_T}}}{T}%20=%200)



------

#### examples

* 在线回归（online regression)

* 专家建议预测（prediction with expert advice)
* 在线排名（online ranking)



------

#### online classification prblem

**在线分类问题**

为了回复Cover's impossibility result,我们进一步限制对抗环境的能力，下面有两种方法。

* 方法一：增加额外的假设

  * **Consistent 算法**

    * Consistent算法维持一个与![image-20191123204233399](Online Convex Optimization.assets/image-20191123204233399.png)一致的所有假设的集合![img](http://latex.codecogs.com/gif.latex?{V_t})。此集合通常称为version space。然后它从中选择任何假设并根据该假设进行预测。

    ![image-20191123205424772](Online Convex Optimization.assets/image-20191123205424772.png)

    * Conisstent算法的mistake bound

    ![image-20191123205437826](Online Convex Optimization.assets/image-20191123205437826.png)

    

  * **Halving算法**

    * 根据Vt中大部分的假设做预测，而不是根据Vt中的一些假设做出武断的预测。这样的话，就会保证无论何时都会移去verson space 中至少一半的假设。

    ![image-20191123205239540](Online Convex Optimization.assets/image-20191123205239540.png)

    * Halving算法的mistake bound

    ![image-20191123205256085](Online Convex Optimization.assets/image-20191123205256085.png)



* 方法二：随机化（randomization)
  * 凸性是推导在线学习算法的中心主题





------

------

### 二、online convex optimization

![image-20191123205143764](Online Convex Optimization.assets/image-20191123205143764.png)

凸集的定义：![img](https://images2018.cnblogs.com/blog/1473673/201809/1473673-20180901105532105-1429571368.png)

一个向量u的Regret定义为

![image-20191123205158466](Online Convex Optimization.assets/image-20191123205158466.png)

算法相对于竞争向量的集合U的Regret被定义为

![image-20191123205214549](Online Convex Optimization.assets/image-20191123205214549.png)



------

#### convexification by randomization

#### 两种凸化技术

* 通过随机凸化

考虑**专家建议的预测问题**：每个在线回合中，学习者必须从d位给定专家的建议中进行选择。![img](http://latex.codecogs.com/gif.latex?{p_t}%20\in%20\{%201,2,...,d\})表示选到的专家，然后学习机收到一个向量![img](http://latex.codecogs.com/gif.latex?{y_t}%20\in%20{[0,1]^d})，其中![img](http://latex.codecogs.com/gif.latex?{y_t}[i])表示听从第![img](http://latex.codecogs.com/gif.latex?i)个专家的建议所遭受的损失，学习机需要支付的损失为![img](http://latex.codecogs.com/gif.latex?{y_t}[{p_t}])。在这种情况下，decision space是离散的，因此非凸。

有限假设类（finite hypothesis class）的在线分类问题可以很容易地作为具有专家建议问题的预测的特例。 因此，Cover’s impossibility result意味着没有算法可以通过专家建议问题获得预测的low Regret。

然而，正如我们在下面所示，通过允许学习者随机化他的预测，我们可以将问题转化为在线凸优化框架，因此可以获得针对该问题的low Regret算法。令![img](http://latex.codecogs.com/gif.latex?S%20=%20\{%20w%20\in%20{R^d}:w%20\ge%200%20\wedge%20{\left\|%20w%20\right\|_1}%20=%201\})是probability simplex，S是一个凸集。

在第 t回合，学习者选择![image-20191123204613430](Online Convex Optimization.assets/image-20191123204613430.png)，并且基于![image-20191123204627255](Online Convex Optimization.assets/image-20191123204627255.png)根据![image-20191123204651015](Online Convex Optimization.assets/image-20191123204651015.png)随机抽取一个专家，学习机支付期望损失：<img src="Online Convex Optimization.assets/image-20191123204704902.png" alt="image-20191123204704902" style="zoom:80%;" />

* Convexification by Surrogate Loss Functionsx'z

  通过替代损失函数凸化

从有限假设类的在线分类具体问题开始，回想一下，我们用来回避 Cover’s impossibility result的技术之一依赖于可实现性假设（realizability assumption）。我们假设存在![img](http://latex.codecogs.com/gif.latex?{h^%20*%20}%20\in%20H)使得对于所有的t有![img](http://latex.codecogs.com/gif.latex?{y_t}%20=%20{h^%20*%20}({x_t}))。有了这个假设，我们描述了Halving算法并且表明它最多![img](http://latex.codecogs.com/gif.latex?{\log%20_2}(\left|%20H%20\right|))个预测错误。我们现在使用在线凸优化语言得出类似的保证:

![img](https://images2018.cnblogs.com/blog/1473673/201809/1473673-20180901160119316-575003312.png)

![img](https://images2018.cnblogs.com/blog/1473673/201809/1473673-20180901160322834-680294912.png)



S是一个凸集，![img](http://latex.codecogs.com/gif.latex?{f_t})对于所有t是一个凸函数，我们转化得到一个在线凸优化问题。

接下来的部分中，我们将推导出在线凸优化问题的算法。 特别是，这些算法之一具有如下的regret bound：

![image-20191123205003157](Online Convex Optimization.assets/image-20191123205003157.png)

其中，![img](http://latex.codecogs.com/gif.latex?\eta)是一个参数，在这里设置为1/4，![img](http://latex.codecogs.com/gif.latex?{L_t})是函数![img](http://latex.codecogs.com/gif.latex?{f_t})关于L1范数的Lipschitz参数。在我们的案例中，

![image-20191123205015749](Online Convex Optimization.assets/image-20191123205015749.png)

通过![img](http://latex.codecogs.com/gif.latex?{f_t})的 surrogate property，我们获得：

![image-20191123205029238](Online Convex Optimization.assets/image-20191123205029238.png)

这种类型的界限，其中错误的数量受到 competing hypothesis的convex surrogate loss的上限，通常被称为relative loss bound。

在realizable的情况下，我们可以进一步简化 relative loss bound如下。 由于bound适用于所有u∈S，因此它特别适用于向量u=(0，...，0,1,0，...，0)，其中1位于对应于 true hypothesis ![img](http://latex.codecogs.com/gif.latex?{h^%20*%20}%20\in%20H)的位置。

通过我们的构造，对于所有t，![image-20191123205053796](Online Convex Optimization.assets/image-20191123205053796.png)，产生![image-20191123205104100](Online Convex Optimization.assets/image-20191123205104100.png)



------

#### FTL算法

**follow-the-leader**

最自然的学习规则是使用任何在过去回合中损失最小的向量。这与Consistent算法的精神相同，它在在线凸优化中通常被称为Follow-The-Leader，**最小化累积损失**。

![image-20191123205630757](Online Convex Optimization.assets/image-20191123205630757.png)

![image-20191123205641235](Online Convex Optimization.assets/image-20191123205641235.png)

采用数学归纳法证明如下：

将上述不等式的两边同时减去![image-20191123205703109](Online Convex Optimization.assets/image-20191123205703109.png)，得到<img src="Online Convex Optimization.assets/image-20191123205724580.png" alt="image-20191123205724580" style="zoom:80%;" />

当T = 1时，由定义代入上述不等式成立。

假设不等式支持T-1，得<img src="Online Convex Optimization.assets/image-20191123205806726.png" alt="image-20191123205806726" style="zoom:80%;" />，对不等式两边同时加上![image-20191123205824900](Online Convex Optimization.assets/image-20191123205824900.png)，得

<img src="Online Convex Optimization.assets/image-20191123205838825.png" alt="image-20191123205838825" style="zoom:80%;" />，该不等式支持所有的u,特别是![image-20191123205857236](Online Convex Optimization.assets/image-20191123205857236.png)，得

<img src="Online Convex Optimization.assets/image-20191123205928996.png" alt="image-20191123205928996" style="zoom:80%;" />，根据定义，![image-20191123210006707](Online Convex Optimization.assets/image-20191123210006707.png),因此得到

<img src="Online Convex Optimization.assets/image-20191123210017284.png" alt="image-20191123210017284" style="zoom:80%;" />

* 例子1：Online Quadratic Optimization

![image-20191123210047347](Online Convex Optimization.assets/image-20191123210047347.png)

* failure of FTL

![](Online Convex Optimization.assets/image-20191124132709327.png)



------

#### FTRL算法

**follow-the-regularized-leader**

通过在FTL的基础上再加一个正则式来使他更稳定

![image-20191124132821897](Online Convex Optimization.assets/image-20191124132821897.png)

![image-20191124133029913](Online Convex Optimization.assets/image-20191124133029913.png)

![image-20191124133102971](Online Convex Optimization.assets/image-20191124133102971.png)



------

#### OGD在线梯度下降

线性化凸函数

带有欧几里得正则式的FTRL算法

![image-20191124133258137](Online Convex Optimization.assets/image-20191124133258137.png)

![image-20191124133336104](Online Convex Optimization.assets/image-20191124133336104.png)

* 在线梯度下降算法的来源

![image-20191124133414408](Online Convex Optimization.assets/image-20191124133414408.png)

![image-20191124133438667](Online Convex Optimization.assets/image-20191124133438667.png)

* **凸函数**以及**次梯度**的概念

  满足一下式子的函数为凸函数，其中，z若不唯一，为此凸函数的次梯度；z若唯一，为此凸函数的梯度。

  ![image-20191124133552633](Online Convex Optimization.assets/image-20191124133552633.png)

  ![image-20191124133758281](Online Convex Optimization.assets/image-20191124133758281.png)

  ![image-20191124133942232](Online Convex Optimization.assets/image-20191124133942232.png)

![image-20191124133905560](Online Convex Optimization.assets/image-20191124133905560.png)



------

#### 强凸正则化

* 强凸的概念

![image-20191124134053624](Online Convex Optimization.assets/image-20191124134053624.png)

![image-20191124134122298](Online Convex Optimization.assets/image-20191124134122298.png)

* 欧几里得正则化

![image-20191124134141768](Online Convex Optimization.assets/image-20191124134141768.png)

* Entropic 正则化

![image-20191124134156808](Online Convex Optimization.assets/image-20191124134156808.png)



------

#### OMD在线镜像下降

![image-20191124134334520](Online Convex Optimization.assets/image-20191124134334520.png)

* 算法由来：凸函数为线性函数的FRTL算法

<img src="Online Convex Optimization.assets/image-20191124134355320.png" alt="image-20191124134355320" style="zoom: 67%;" />

* 当<img src="Online Convex Optimization.assets/image-20191124134546873.png" alt="image-20191124134546873" style="zoom:80%;" />时，在线镜像下降为在线梯度下降。在线梯度下降为在线镜像下降的最简形式。
* 悔界

![image-20191124134742267](Online Convex Optimization.assets/image-20191124134742267.png)





------

------

### 三、online calssification

#### Weighted Majority √

![image-20191124135002599](Online Convex Optimization.assets/image-20191124135002599.png)

------

#### SOA标准最优化算法

##### H shattered tree

<img src="Online Convex Optimization.assets/image-20191124135116939.png" alt="image-20191124135116939" style="zoom: 50%;" />

* 概念

![image-20191124135239367](Online Convex Optimization.assets/image-20191124135239367.png)

<img src="Online Convex Optimization.assets/image-20191124135304424.png" alt="image-20191124135304424" style="zoom:80%;" />

##### Ldim

* 概念

![image-20191124135426743](Online Convex Optimization.assets/image-20191124135426743.png)

* 重要性质

<img src="Online Convex Optimization.assets/image-20191124135458730.png" alt="image-20191124135458730" style="zoom:80%;" />

##### SOA算法

<img src="Online Convex Optimization.assets/image-20191124135602128.png" alt="image-20191124135602128" style="zoom:80%;" />

* 重要定理

![image-20191124135745670](Online Convex Optimization.assets/image-20191124135745670.png)

<img src="Online Convex Optimization.assets/image-20191124135639296.png" alt="image-20191124135639296" style="zoom:67%;" />



------

#### Percepron and Winnnow √√









------

------

### 四、Limited feedback (bandits)

在这个在线学习问题中，学习机知道它预测的向量的损失函数，但它不知道在其他点的损失函数。

**MAB：这里和专家建议预测问题相似，但是在每一轮，学习机只能观察到它选择的专家的代价而不能观察到其他专家的代价。**

#### 估计梯度的在线镜像下降

这里![image-20191125091846806](Online Convex Optimization.assets/image-20191125091846806.png)不再表示损失函数的次梯度，而是一个随机向量，<img src="Online Convex Optimization.assets/image-20191125091905733.png" alt="image-20191125091905733" style="zoom:80%;" />。

![image-20191125091950107](Online Convex Optimization.assets/image-20191125091950107.png)

<img src="Online Convex Optimization.assets/image-20191125092213932.png" alt="image-20191125092213932" style="zoom: 80%;" />

​         

####    The multi-armed bandit problem

有d个手臂，在每个在线回合学习机应该选择其中一个手臂，用pt表示，被选择的手臂可以是一个随机变量。然后他收到一个选择这个手臂的代价，![image-20191125094132666](Online Convex Optimization.assets/image-20191125094132666.png) ，向量![image-20191125094225467](Online Convex Optimization.assets/image-20191125094225467.png) 和每一个手臂的代价相联系，但是学习机只能看到他推动的那个手臂的代价。              

这个问题就和专家建议预测问题相似。唯一的不同就是学习机不知道他没选择的那些专家的代价。学习机的目标就是产生尽可能的后悔。                                                      

这个问题可以看作是**利用-开发的权衡**(exploit-explore  EE问题）。一方面，我们可能更愿意选择那些依靠以前回合的经验有最低的代价的手臂（利用我们所知道的最好的选择）；另一方面，也许最好应该探索其他的有更小代价的手臂（冒一些风险去收集未知的信息），最好的长期战略可能包括短期的牺牲。例如，一次探索试验可能完全失败，但他警告我们将来不要频繁地采取这种行动。

![image-20191125101313816](Online Convex Optimization.assets/image-20191125101313816.png)

<img src="Online Convex Optimization.assets/image-20191125101256409.png" alt="image-20191125101256409" style="zoom:80%;" />



* What is MBA?

  假设你在一个赌场里，面对着多台老虎机，每台老虎机都配置有一个未知的概率，即你在一次游戏中获得奖励的可能性有多大。问题是:实现最高长期回报的最佳策略是什么? 

  ![image-20191125141022147](Online Convex Optimization.assets/image-20191125141022147.png)



#### Gradient descent without a gradient

我们只有一个访问损失函数的黑盒，不能直接计算出次梯度（上一章的多臂老虎机问题是一个特例，他的损失函数是线性的）。

![image-20191125105440695](Online Convex Optimization.assets/image-20191125105440695.png)





------

------

### 五、online-to-batch conversions

在线到批处理的转化

