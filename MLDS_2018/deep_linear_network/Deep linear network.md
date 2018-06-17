# Deep linear network
[课程链接](https://www.bilibili.com/video/av24015685/?p=5)


---

先考虑这样一个问题，对于只有一个参数的network，即y=wx

我们现在假定$\hat y = x$,我们希望训练出一个netword，使得$y=w_1x$尽量接近$\hat y$，那么可以规定$L=(\hat y-w_1x)^2$，此时的L是convex的。

在考虑一个问题，假设一个network有两个参数（deeper），其结构如下图所示。

![](https://github.com/YoranAtWhut/note/blob/c39ddc2a66e7a8bcd4f508ffb38a62a57af3b95a/MLDS_2018/deep_linear_network/2.png)


同样规定$\hat y = x$，并且规定$L=(\hat y-w_1w_2x)^2$,但注意此时的L相对于W坐标系不是convex的，其error face如下图（途中颜色越蓝，表示error越小，图中里蓝色小箭头是gradient的方向）。

![](https://github.com/YoranAtWhut/note/blob/c39ddc2a66e7a8bcd4f508ffb38a62a57af3b95a/MLDS_2018/deep_linear_network/3.png)

现在我们假设输入训练数据为（1，1），计算偏导如下

![](https://github.com/YoranAtWhut/note/blob/c39ddc2a66e7a8bcd4f508ffb38a62a57af3b95a/MLDS_2018/deep_linear_network/4.png)

那么什么情况下是critical point呢？第一种情况：$w_1w_2=1$，第二种情况$(w_1,w_2)=(0,0)$，通过计算原点处的hessian矩阵，可以得到其矩阵为[[0,-2],[-2,0]]，计算其特征向量和特征值分别为$(1,1)^T <-> -2,(1,-1)^T <-> 2$，特征值有正有负，因此原点处是一个鞍点，注意这个鞍点是不平的，往（1，1）方向走可以是error减小，往（1，-1）方向走可以使error增大，这一点可以从图中看出来。

再考虑三个参数的情况，此时模型如下图所示：

![](https://github.com/YoranAtWhut/note/blob/c39ddc2a66e7a8bcd4f508ffb38a62a57af3b95a/MLDS_2018/deep_linear_network/5.png)

计算critical point以及hessian矩阵，计算如下：

![](https://github.com/YoranAtWhut/note/blob/c39ddc2a66e7a8bcd4f508ffb38a62a57af3b95a/MLDS_2018/deep_linear_network/6.png)

那么什么地方是critical point呢？有三种情况：
1. $w_1w_2w_3=1$
2. $w_1=w_2=w_3=0$
3. 三个参数中有两个是零。

对于这三种情况，分别分析：
1. 是global minima
2. hessian矩阵是全0，这就是说，如果不考虑更高次项，在原点不管往哪个方向走，error都不会有变化。
3. 此时hessian矩阵一正一负一零，是鞍点


如果有11个参数，在这个参数空间里，首先选取一个点$\theta^1$，然后选取其关于原点对称的点$\theta^2$，计算$L=(\hat y-w_1w_2...w_{11}x)^2$在$\theta^1$与$\theta^2$之间的插值，得到如下的error face图。

![](https://github.com/YoranAtWhut/note/blob/c39ddc2a66e7a8bcd4f508ffb38a62a57af3b95a/MLDS_2018/deep_linear_network/7.png)

左图中$\theta^1是$w_1w_2...w_{11}=-1$的点，右图中$\theta^1是$w_1w_2...w_{11}=1$的点,可以看到error face在原点附近非常的平。

在这个课中，做了一个实验来找到一个只有两个参数的network中$w_1,w_2$的最佳值，首先将$w_1,w_2$初始化为0，此时经过多轮迭代后$w_1,w_2$仍为0，因为原点处的gradient为0（H不为零）,若将$w_1,w_2$初始化为一个非常小的值，如$w_1,w_2=0.000000001$，再进行训练，会发现$w_1,w_2$不卡在原点了。

再使用上述方式初始化，但再加一个层，即$w_1,w_2,w_3=0.000000001$，会发现train不起来了，参数卡在了初始值，loss始终为1.因为通过上述分析我们知道，有三个参数的NN在原点处的hessian全为零，是一个非常平的error surface。

再使用11个参数，此时的NN的error surface在原点处平的范围更大了，即使用一个正常的初始化，如N(0,0.01)也无法逃脱初始值。因此我们需要把W初始化的离原点很远。

上述论述中是比较特殊的，现在假设输入输出都是向量。可以证明只要满足一些条件，所有deep linear network的local minima都是global minima。比如hidden layer size>=input layer size,output layer size。并且，如果一个线性NN有两个以上的隐层，就会产生那种“很差”的saddle point，该处的H矩阵没有负的特征值。