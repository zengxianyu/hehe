---
layout: post
title: 阅读统计学习方法
---
## 1
模型：函数或者条件分布
策略：根据什么准则选择模型
算法：优化方法
损失函数：度量一次的好坏$L(y, f(x))$
期望风险：$\mathbb{E}_{P(x,y)}[L(y, f(x))]$
$P(x,y)$未知，最小化的目标又和它有关
经验风险：$\frac{1}{N}\sum_{i=1}^NL(y_i, f(x_i))$
结构风险：表示模型的复杂度

## 2感知机
模型：$f(x)=wx+b$
学习策略：误分类的点到分类平面的总距离
平面$wx+b=0$是所有在$w$方向的投影为$-\frac{b}{||w||}$的点。任意一点到平面的距离是$|\frac{wx}{||w||}+\frac{b}{||w||}|=|\frac{f(x)}{||w||}|$
误分类的点$x_i$满足$y_if(x_i)<0$，到平面的距离是$-y_i\frac{f(x_i)}{||w||}$
$\min_w\sum_{误分类}-y_i(wx_i+b)/||w||$

## 3最近邻
kd树：快速找到N个最近邻，通过不考虑分割轴对面不可能更近的点减少计算量
给定一个构建于一个样本集的 kd 树，下面的算法可以寻找距离某个点 p 最近的 k 个样本。
```
https://www.joinquant.com/post/2627
https://www.joinquant.com/post/2843
零、设 L 为一个有 k 个空位的列表，用于保存已搜寻到的最近点。  
一、根据 p 的坐标值和每个节点的切分向下搜索（也就是说，如果树的节点是照x_r=a进行切分，并且 p 的 r 坐标小于 a，则向左枝进行搜索；反之则走右枝）。  
二、当达到一个底部节点时，将其标记为访问过。如果 L 里不足 k 个点，则将当前节点的特征坐标加入 L ；如果 L 不为空并且当前节点的特征与 p 的距离小于 L 里最长的距离，则用当前特征替换掉 L 中离 p 最远的点。  
三、如果当前节点不是整棵树最顶端节点，执行 (a)；反之，输出 L，算法完成。  
a. 向上爬一个节点。如果当前（向上爬之后的）节点未曾被访问过，将其标记为被访问过，然后执行 (1) 和 (2)；如果当前节点被访问过，再次执行 (a)。  
1. 如果此时 L 里不足 kk 个点，则将节点特征加入 L；如果 L 中已满 k 个点，且当前节点与 p 的距离小于 L 里最长的距离，则用节点特征替换掉 L 中离最远的点。  
2. 计算 p 和当前节点切分线的距离。如果该距离大于等于 L 中距离 p 最远的距离并且 L 中已有 k 个点，则在切分线另一边不会有更近的点，执行 (三)；如果该距离小于 L 中最远的距离或者 L 中不足 k 个点，则切分线另一边可能有更近的点，因此在当前节点的另一个枝从 (一) 开始执行。
```

## 4 naive bayes
$x=(x^{(1)},...,x^{(n)})$
生成模型：
$P(X=x|Y=c_k)=P(X^{(1)}=x^{(1)},...,X^{(n)}=x^{(n)}|Y=c_k)$
假设特征之间条件独立：
$=\prod_jP(X^{(j)}=x^{(j)}|Y=c_k)$
分类时：
$P(Y=c_k|X=x)=\frac{P(x|c_k)P(c_k)}{\sum_k P(x|c_k)P(c_k)}$
输入$x$，输出使上式取最大值的类（最大化后验概率->最小化期望风险）：
$y=\arg\max_{c_k}\prod_jP(x^{(j)}|c_k)P(c_k)$
参数估计，参数$P(x^{(j)}|c_k), P(c_k)$

## 5 决策树
熵：
$H(X)=-\sum_iP(X=x_i)\log P(X=x_i)$记为$-\sum_ip_i\log p_i$
$H(Y|X)=-\sum_ip_iH(Y|X=x_i)$
$p_i$由数据估计得到时：经验熵
互信息：$g(D,A)=H(D)-H(D|A)$
建立决策树：从互信息最大的特征作为根节点开始，从上到下的节点是互信息越来越小的特征。
剪枝：损失函数$\sum_{t=1}^{|T|}N_tH_t(T)+\alpha|T|$，其中$|T|$是叶子节点的个数，$t$是叶子节点，$N_t$是叶子节点的样本个数，其中第$k$类节点的个数为$N_{tk}$。从每个叶子节点开始，如果去掉这个叶子节点所在的子树使得损失函数变小了就去掉。

## 6 logistic回归、最大熵模型
事件的几率、对数几率（logit）：发生的概率和不发生的概率的比值
用线性函数模型表示logit就是logistic回归模型
$\log\frac{ P(Y=1|x)}{1-P(Y=1|x)}=wx+b$
$P(Y=1|x)=\frac{\exp(wx+b)}{\exp(wx+b)+1}$

最大熵原理：满足约束条件的模型里选熵最大的
模型$P(y|x)$，经验分布$\tilde{P}(y,x), \tilde{P}(x)$，特征函数$f(x,y)\in{0,1}$
约束：特征函数用经验分布求的期望值和用模型得到的期望值相等，
$\mathbb{E}_{P}(f)=\mathbb{E}_{\tilde{P}}(F)$，$\sum_yP(y|x)=1$
$\sum_{x,y}f(x,y)P(y|x)\tilde{P}(x)=\sum_{x,y}f(x,y)\tilde{P}(x,y)$
最大化熵$H(P)=-\sum_{x,y}\tilde{P}(x)P(y|x)\log P(y|x)$

设有n个特征函数$f_i(x,y)$，用拉格朗日乘子法，引入乘子$w_0, w_1, .., w_n$:
$L(P, w)=-H(p)\\
+\sum_{i=1}^nw_i\sum_{x,y}f_i[P(y|x)\tilde{P}(x)-\tilde{P}(x,y)]\\
+w_0(\sum_{x,y}P(y|x)-1)$
得到无约束的优化问题$\min_P\max_wL(P, w)$
因为$L(P,w)$是关于$P$的凸函数，可以通过求解对偶问题$\max_w\min_PL(P,w)$求上式

$\min_PL(P,w)$的解是$P_w(y|x)=\exp(\sum_{i=1}^nw_if_i(x,y))/Z_w(x)$
把$\max_wL(P_w, w)$记为$\Psi(w)$，最大熵模型就化成$\max_w\Psi(w)$了。这等价于对$P_w(y|x)$的最大似然估计。从最大熵原理、用经验分布和模型求得的期望相等的约束出发，得到了类似于logistic回归的模型。。。



