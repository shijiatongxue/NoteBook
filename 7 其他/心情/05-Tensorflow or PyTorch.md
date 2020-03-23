# Tensorflow or PyTorch

最近要做毕业设计了，用哪个深度学习的框架是我考虑的一个问题。

## 观点 #1

Tensorflow和PyTorch都是开源的，它们由不同的团队开发。

- Tensorflow基于Theano，由Google开发；
- PyTorch基于Torch，由Facebook开发。

## 观点 #2

两个框架的最大不同之处在于定义计算图的方式。

- Tensorflow创建一个静态图；
- PyTorch创建动态图。

为什么要这样设计？在Tensorflow中，我们需要预先定于模型的整个计算图，然后返回你的机器学习模型。但是在PyTorch中，你可以在运行中定义和操作图。对于使用变长输入大小的RNN模型来说，这一点颇有好处。

## 观点 #3

Tensorflow比PyTorch有更加陡峭的学习曲线。

- PyTorch更加*pythonic*，创建机器模型的时候更加符合直觉；
- 使用Tensorflow，你需要学习一些它的工作机制（session，placeholder等）。

## 观点 #4

Tensorflow有更大的社区。（此篇文章发表于2018年4月，现在是2019年11月）

> 我：现在网络上也有很多课程是用PyTorch讲授的，例如cs224n

## 观点 #5

TensorBoard是可视化的好工具，它可以直接将模型显示在浏览器。目前PyTorch没有这样一个工具，虽然你可以使用Matplotlib。

最后，Tensorflow更加适用于产品级模型并且可以拓展。PyTorch相比之下，便于学习并且轻量，是创建快速原型的好工具。

---

参考：[Tensorflow or PyTorch : The force is strong with which one?]( https://medium.com/@UdacityINDIA/tensorflow-or-pytorch-the-force-is-strong-with-which-one-68226bb7dab4 )