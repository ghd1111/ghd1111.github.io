## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/ghd1111/ghd1111.github.io/edit/main/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3


- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/ghd1111/ghd1111.github.io/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.


Online learning and online convex optimization一、Online learningoffLine learning 离线学习

online learning在线学习

一种模型的训练方法，能够根据线上反馈的数据，实时快速的进行模型调整，使得模型及时反映线上的变化，提高线上预测的准确率。Online Learning的流程包括：将模型的预测结果展现给用户，然后收集用户的反馈数据，再用来训练模型，形成闭环的系统。

根据线上预测的结果动态地调整模型。最终的目标是使整体的损失最小。（每次预测后，其结果用来更新修正预测模型，用于对以后数据进行更好的预测）

在线学习算法强调的是训练的实时性，面向流式数据，每次训练不使用全量数据，而是以之前训练好的参数为基础，每次利用一个样本更新一次模型，是属于增量学习的一部分，从而快速更新模型，提高模型的时效性。

introduction在线学习是在一系列连续的回合（round) 中进行的；

在回合,学习机（learner）被给一个question：(一个向量，即为特征向量),为从instance domain：采样得到的。学习机给出一个预测值：，然后得到正确的答案：，从target domain：采样得到，定义损失函数为。定义损失函数为。在大多数情况下，在中，但是，允许学习者从更大的集合中选择预测有时很方便，我们用D表示prediction domain。

学习框架：

学习机的最终目标就是随着运行最小化累计损失，也就是产生尽可能小的预测差错，努力去从原来的回合中提取信息然后去提高它对当下和未来问题的更好的预测能力。

two restrictions两个限制条件

限制一，适合于在线分类的情况

假设所有的answer都是由一些target mapping生成的:，取自固定集合，称为假设类，由H表示，这是学习者已知的。由于这种对序列的限制，我们称之为realizable case，学习者应该尽可能少犯错误，假设和问题的顺序可以由对手来选择。对于在线学习算法A，我们用表示A在一系列用标记的例子上可能犯的最大错误数。我们再次强调和问题的顺序可以由对手来选择。的上界称为mistake bound，我们将研究如何设计最小的算法。

限制二，relaxation of the realizable assumption

我们不再假设所有答案都是由产生，但是我们要求学习机与来自H最好的固定预测器竞争。这被算法的regret所捕获，regret度量了回顾过去，学习机有多“抱歉”没有遵循一些假设的预测。形式上，算法相对于的regret当在一系列T实例上运行时定义为：

算法相对于假设类H的regret是

学习机的目标是相对于H具有尽可能低的regret。我们有时会对“low regret”算法感到满意，我们认为与轮数T呈次线性增长，其意味着当T变为无穷大时，学习机的平均损失与H中最佳假设的平均损失之间的差异趋于零。

次线性增长：

examples在线回归（online regression)

专家建议预测（prediction with expert advice)

在线排名（online ranking)

online classification prblem在线分类问题

为了回复Cover's impossibility result,我们进一步限制对抗环境的能力，下面有两种方法。

方法一：增加额外的假设

Consistent 算法

Consistent算法维持一个与一致的所有假设的集合。此集合通常称为version space。然后它从中选择任何假设并根据该假设进行预测。

Conisstent算法的mistake bound

Halving算法

根据Vt中大部分的假设做预测，而不是根据Vt中的一些假设做出武断的预测。这样的话，就会保证无论何时都会移去verson space 中至少一半的假设。

Halving算法的mistake bound

方法二：随机化（randomization)

凸性是推导在线学习算法的中心主题

二、online convex optimization凸集的定义：

一个向量u的Regret定义为

算法相对于竞争向量的集合U的Regret被定义为

convexification by randomization两种凸化技术通过随机凸化

考虑专家建议的预测问题：每个在线回合中，学习者必须从d位给定专家的建议中进行选择。表示选到的专家，然后学习机收到一个向量，其中表示听从第个专家的建议所遭受的损失，学习机需要支付的损失为。在这种情况下，decision space是离散的，因此非凸。

有限假设类（finite hypothesis class）的在线分类问题可以很容易地作为具有专家建议问题的预测的特例。 因此，Cover’s impossibility result意味着没有算法可以通过专家建议问题获得预测的low Regret。

然而，正如我们在下面所示，通过允许学习者随机化他的预测，我们可以将问题转化为在线凸优化框架，因此可以获得针对该问题的low Regret算法。令是probability simplex，S是一个凸集。

在第 t回合，学习者选择，并且基于根据随机抽取一个专家，学习机支付期望损失：

Convexification by Surrogate Loss Functionsx'z

通过替代损失函数凸化

从有限假设类的在线分类具体问题开始，回想一下，我们用来回避 Cover’s impossibility result的技术之一依赖于可实现性假设（realizability assumption）。我们假设存在使得对于所有的t有。有了这个假设，我们描述了Halving算法并且表明它最多个预测错误。我们现在使用在线凸优化语言得出类似的保证:

S是一个凸集，对于所有t是一个凸函数，我们转化得到一个在线凸优化问题。

接下来的部分中，我们将推导出在线凸优化问题的算法。 特别是，这些算法之一具有如下的regret bound：

其中，是一个参数，在这里设置为1/4，是函数关于L1范数的Lipschitz参数。在我们的案例中，

通过的 surrogate property，我们获得：

这种类型的界限，其中错误的数量受到 competing hypothesis的convex surrogate loss的上限，通常被称为relative loss bound。

在realizable的情况下，我们可以进一步简化 relative loss bound如下。 由于bound适用于所有u∈S，因此它特别适用于向量u=(0，...，0,1,0，...，0)，其中1位于对应于 true hypothesis 的位置。

通过我们的构造，对于所有t，，产生

FTL算法follow-the-leader

最自然的学习规则是使用任何在过去回合中损失最小的向量。这与Consistent算法的精神相同，它在在线凸优化中通常被称为Follow-The-Leader，最小化累积损失。

采用数学归纳法证明如下：

将上述不等式的两边同时减去，得到

当T = 1时，由定义代入上述不等式成立。

假设不等式支持T-1，得，对不等式两边同时加上，得

，该不等式支持所有的u,特别是，得

，根据定义，,因此得到

例子1：Online Quadratic Optimization

failure of FTL

FTRL算法follow-the-regularized-leader

通过在FTL的基础上再加一个正则式来使他更稳定

OGD在线梯度下降线性化凸函数

带有欧几里得正则式的FTRL算法

在线梯度下降算法的来源

凸函数以及次梯度的概念

满足一下式子的函数为凸函数，其中，z若不唯一，为此凸函数的次梯度；z若唯一，为此凸函数的梯度。

强凸正则化强凸的概念

欧几里得正则化

Entropic 正则化

OMD在线镜像下降算法由来：凸函数为线性函数的FRTL算法

当时，在线镜像下降为在线梯度下降。在线梯度下降为在线镜像下降的最简形式。

悔界

三、online calssificationWeighted Majority √SOA标准最优化算法H shattered tree概念

Ldim概念

重要性质

SOA算法重要定理

Percepron and Winnnow √√四、Limited feedback (bandits)在这个在线学习问题中，学习机知道它预测的向量的损失函数，但它不知道在其他点的损失函数。

MAB：这里和专家建议预测问题相似，但是在每一轮，学习机只能观察到它选择的专家的代价而不能观察到其他专家的代价。

估计梯度的在线镜像下降这里不再表示损失函数的次梯度，而是一个随机向量，。

         

The multi-armed bandit problem有d个手臂，在每个在线回合学习机应该选择其中一个手臂，用pt表示，被选择的手臂可以是一个随机变量。然后他收到一个选择这个手臂的代价， ，向量 和每一个手臂的代价相联系，但是学习机只能看到他推动的那个手臂的代价。              

这个问题就和专家建议预测问题相似。唯一的不同就是学习机不知道他没选择的那些专家的代价。学习机的目标就是产生尽可能的后悔。                                                      

这个问题可以看作是利用-开发的权衡(exploit-explore  EE问题）。一方面，我们可能更愿意选择那些依靠以前回合的经验有最低的代价的手臂（利用我们所知道的最好的选择）；另一方面，也许最好应该探索其他的有更小代价的手臂（冒一些风险去收集未知的信息），最好的长期战略可能包括短期的牺牲。例如，一次探索试验可能完全失败，但他警告我们将来不要频繁地采取这种行动。

What is MBA?

假设你在一个赌场里，面对着多台老虎机，每台老虎机都配置有一个未知的概率，即你在一次游戏中获得奖励的可能性有多大。问题是:实现最高长期回报的最佳策略是什么? 

Gradient descent without a gradient我们只有一个访问损失函数的黑盒，不能直接计算出次梯度（上一章的多臂老虎机问题是一个特例，他的损失函数是线性的）。


Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
