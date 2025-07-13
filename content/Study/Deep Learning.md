---
title: '"Deep Learning"'
draft: false
tags:
  - "#AI"
---
 

环境：
`python3 -m pipenv shell`
\^D退出shell

## 反向传播
- **本质**:链式法则的运用
- 靠近最终loss函数的部分层数低,远离的部分层数高
- +:低层->高层不变(偏导都为1) \*:低层->高层再乘另外一个元
	- 将所有叶子节点按照步长更新
- 计算图:DAG,用拓扑序实现反向传播
## 常用工具
**conda:**
`conda create -n env_name`创建新环境
`conda env list`查看已有的环境
`conda activate env_name`激活环境
`conda deactivate`退出环境

**pip**:
激活了conda环境时,使用pip3会自动重定向到conda,可用`which pip3`检测是否重定向至anaconda目录下的pip3
`pip3 install -r requirements.txt`根据requirement.txt内容安装所需的包
`pip3 install package_name`安装包''

## Pytorch
### 初始化
`torch.zeros((3,4))`返回一个3\*4大小的矩阵(张量)
`torch.randn()`根据正态分布抽取随机数($\sigma = 1,\mu=0$),即大部分在(-3,3)内
`torch.tensor(array)`把一个元组/list转换成张量返回,用小写tensor(会自动推断并对齐类型)

### 取样
`torch.Generator().manual_seed(12345678)`返回一个generator对象
`torch.rand(3,generator=g)`返回一个大小为(3,)的张量,即一维张量,随机生成三个数
`torch.multinomial(p,num_samples=100,replacement=True,generator=g)`根据tensor p取样100个数,数的大小对应下标
`torch.randint(0,5,(32,))`生成32个0~5间的随机数(tensor)
### broadcast

`torch.sum(input,dim,keepdims)`
- dim=0:对列求和
- dim=1:对行求和
- keepdims:是否保留被压缩的那一维度属性
广播操作要求:有一个对齐,另一个有一个是1/0
非矩阵张量自动看成行向量(自动右对齐)
做除法

### encode
- `torch.nn.functional.one_hot(indice,num_classes)`one hot编码,每行只有原tensor对应的那个数索引是1,其他都是0 接受`longtensor`(64位整数)
- 使用索引

### 计算
- pytorch的tensor以一维形式存储,使用`x.view(-1,2)`可以原地改变张量的形状,-1可以自动推测某维度大小
- 交叉熵`torch.nn.functional.cross_entropy`可以方便地算loss,对比手动归一化后计算loss有以下好处
	- 无中间过程,前向传播和反向传播效率高
	- 减去对数最大值,不容易越界(float类型对小数容忍度大)
`x.max(0/1,keepdim=True)`有属性.values(每列/行最大值)还有.indices,即最大值所在的下标
矩阵求导:数乘(\*)与加法(+)与标量求导相同,特殊情况:broadast需要把被广播的维度求和
矩阵乘法:$C=W@H+b,\frac {\delta C} {\delta W}=与H^T有关$,涉及到叉乘,实际运用时先看维数匹配就行
## activation
训练前的准备：
- target：对于任何一个y，分配均匀的概率 负对数概率$-log\frac{1}{m}$
- logits 的值越接近0,经过softmax得到的probs更均匀.但权重W的初始值最好不要直接设成0

激活函数:
- 作用:
	- 没有激活函数,所有变换都是线性变换,整个多层神经元会坍缩成一个线性函数层
	- 防止梯度过大,常用在多层神经元的传递过程中
	- 一般非线性,增强模型泛化能力
- 副作用:
	- 压缩了导数,容易对梯度不敏感,导致神经元"死亡"

保持标准差:
	普通:乘$\frac{gain}{\sqrt{fan in}}$,默认gain是1
	在该层后经过了激活函数:
		tanh:$gain=\frac 5 3$
		ReLu:$gain=\sqrt 2$

**批量归一化batch normalization**
- 保持标准差得手动算,而且只保证开始的时候标准差归一了
- 在深层网络中,每层的输入分布随着上一层参数更新的变化,也在不断变化,导致layer需要持续适应新分布,训练效率降低
- BN通过对每层输入进行归一化,将其强行拉回平均值为0,方差为1的正态分布,从而稳定下一层输入的分布,让后续层的参数训练更高效,一般放在layer和激活函数中间
- 训练阶段使用批次的标准差和均值.验证时使用全局的标准差和均值,计算公式$X^{*}=\frac{X-\mu}{\sigma+\epsilon},y^{*}=\gamma X^{*}+\beta$,其中$\epsilon$是很小的数,为了防止分母是0;$\gamma,\beta$都是可以自己学习的量.
	- $\gamma$表示缩放量,让层决定是否恢复方差.如$\gamma=\sigma$,则代表取消方差归一化;
	- $\beta$表示偏移量,对归一化的分布进行偏移,调整均值的位置.若$\beta=\mu$则代表取消均值归一化
	- 初始时设置$\mu=0,\gamma=1$代表单纯的归一化
- BN由于使用本身有噪声的批次,反而产生了正则化的副作用,让模型泛化能力更强了