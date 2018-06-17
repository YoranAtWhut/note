# Non-linear deep network
[课程链接](https://www.bilibili.com/video/av24015685/?p=6)
---
之前证明了，只要deep linear network满足一定的条件（如隐层规模大于等于输入输出的维度），那么该linear network就只有global minima，那么non-linear也是这样吗？
  
证明non-linear NN没有local minima是很难的，但是证明其有local minima很简单。我们只需要在某个NN模型中找到一组参数，通过该组参数计算Loss，该loss是localminima即可。

现在我们设计一个NN模型与5笔训练数据，并给出两套参数（参数一（1，-3，1，0），参数二（-7，-4，1，0））。模型如下图所示

![](https://github.com/YoranAtWhut/note/blob/master/MLDS_2018/non_linear_network/1.png)

两套参数下的模型图如下所示：

![](https://github.com/YoranAtWhut/note/blob/master/MLDS_2018/non_linear_network/2.png)


对于参数一，它的曲线是下图中红色那条，它无法fit（-1，3）和（1，-3），它的loss是18（（3-0）^2+(-3-0)^2），并且可以证明参数一是一个minima，因为参数一的任何轻微变动都会使loss增大。对于参数二，可以计算出其loss为14（（-3-0）^2+(1-0)^2+(2-0)^2）。由于参数一是minima，又因为有另一组参数使得loss更小，所以参数一是local minima。因此，Relu的NN 是有local minima的。

![](https://github.com/YoranAtWhut/note/blob/master/MLDS_2018/non_linear_network/3.png)

generally，对于更一般的relu nn，也可以找到其盲点（local minima）。比如说该NN所有neuron的输出都为0，所以算出的y也为0，而且gradient也为0（因为此时无论如何稍微的移动权重，所有的neuron的输出仍然为0，所以此处是一个critical point）。如下图所示：

![](https://github.com/YoranAtWhut/note/blob/master/MLDS_2018/non_linear_network/4.png)

在这样一个盲点的领域内修改参数，NN的输出仍然为零，满足local minima的定义，所以该点是一个local minima。而显然该点不是global minima，因为我们只需要找到某个参数点，该组参数使得NN的输出更加接近label即可。那么如何使得NN陷入盲点呢？一种方法就是使得NN的bias的值远远小于0即可。有人据此做了实验，实验结果如下：

![](https://github.com/YoranAtWhut/note/blob/master/MLDS_2018/non_linear_network/5.png)

上图中，上面那一行用的是正常的mnist数据集，下面那一行是不正常的mnist数据集（把x和y进行shuffle，比如把2的数据标记为8），而在上面的每个小图中，图中每个点都代表了一个NN，横轴不同代表了隐层单元数目不同。在第一行前两幅图中，使用正常的参数初始化方式，从图中可以看出，最终收敛后的NN都能准确地预测，而后三幅图中将NN的参数初始化到了Relu NN的盲点（local minima，并且应该是那种周围都比较平的local minima），那么在训练过程中它将再也爬不出来了（gradient为0），第二行也表达了同样的意思。并且，从每一行的第三幅图中可以猜测出训练时的x大多为正值，因为只有是正值才会使neuron在relu操作之前，其求和得到的为负值。

除了参数的初始化可能会影响NN是否陷入local minima，训练数据本身的分布也会决定NN是否陷入local minima。现在假设下图中上面这样一个用于预测的NN，其activation为relu。而训练数据也是由一个NN生成的，但是该NN的weight是fix住的，并且该NN的neuron数量为k，不同于上面NN中的n。产生训练数据的过程如下，首先产生向量x~N（0，1），然后将x送入generator产生$\hat y$，而上面的NN的任务就是使得自己的参数W和下图中的V越接近越好。

![](https://github.com/YoranAtWhut/note/blob/master/MLDS_2018/non_linear_network/6.png)

若n大于等于k，且$n_i=k_i$，则上面的NN就找到了global minima。但即使这样实际中并不保证一定找到global minima，有人据此做了如下实验，实验结果如下图：

![](https://github.com/YoranAtWhut/note/blob/master/MLDS_2018/non_linear_network/7.png)

在左边的图中，n=k，其中的百分比表示训练最终陷入local minima次数的占总训练次数的比例，而average minimal eigenvalue就是H矩阵eigenvalue的均值，为正则表示其为local minima。而在右图中n=k+1，此时陷入local minima的几率大大减小。而在n>=k+2时，NN陷入local minima的几率为0（并不表示此时没有local minima）。

上述理论可以说明，如果我们的参数或者训练数据服从某个分布，我们就可以使Non-linear的NN找到其global minima，即使其由 local minima