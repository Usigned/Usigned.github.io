---
title: what the fuck is inductive bias
tags: [Deep-Learning]
---

毕设调研之闲扯篇：什么是他🐎的归纳偏置(inductive bias)

## 参考资料

[ 深度学习的归纳偏置是什么？ - 知乎 (zhihu.com)](https://www.zhihu.com/question/41404496/answer/627673667)

[关于Vision Transformer的一些思考 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/276145805)

## 正文

第一次接触这个词的时候是在看ViT(Vision Transformer)的时候，也就是参考文献中的第二篇文章。ViT作者在解释为什么要将Transformer用于CV领域的时候说的是，传统的卷积神经网络中卷积操作的设计有太多的归纳偏置(inductive bias)，而Transformer中的self-attention结构的归纳偏置会少一点。而文章作者就对这个观点进行了批驳，说如果不要归纳偏置，那不如都用MLP(Multi Layer Perception 个人理解就是全连接神经网络)完事了。谁知道今年google又发了一篇论文[MLP-Mixer](https://www.zhihu.com/question/457926000/answer/1870019880)，仅用MLP就实现了在ImageNet上SOTA模型的精度，其观点就是卷积操作, self-attention等结构中的归纳偏置太多了，其实它们都是不必要的。其一经发表就引发热议，文章作者也喜提预言家。

其实当初看就图一乐，看看神经网络是怎么发展的，怎么由简入繁，现在又是怎么产生删繁就简的趋势的，对归纳偏置是什么也没怎么理解。今天心血来潮又搜了搜关于inductive bias的相关内容，发现这个东西好像早在机器学习中就有了，于是上知乎又了解了一波。下面总结一下个人理解的什么是归纳偏置(what the fuck is inductive bias)。

归纳偏置就是人们通过大量实验结果、历史经验总结来的解决方案偏好。举几个例子：比如有论文说在条件受限的情况下，使用进化算法比强化学习效果好，这就是一种归纳偏置。再比如在深度学习领域中，CV领域使用卷积神经网络比较多，因为研究普遍认为卷积操作能更好处理空间局部性；而在NLP中则更偏好时序网络，因为其能处理序列信息。而其实卷积操作或是自注意力操作其实都是可以用全连接操作表达的，人为规定其结构相当于对设计空间进行了人为的剪枝，比如在CV中，哪些不是卷积神经网络的全连接网络就被去掉了。归纳偏置的好处就是可以提高样本的利用率、减少计算代价，缺点则是牺牲了模型的泛化能力。按照ViT作者的说法，transformer之所以能用于CV，而卷积网络却难以用于seq2seq任务，原因就是attention比卷积有更少的归纳偏置（换句话说，其实卷积操作可以用attention操作表达？）。最后总结一下，归纳偏置越强，模型的效果应该越好（假定归纳偏置是合适的），而模型的泛化性也会越差，即其只能适用于单一任务，难以迁移至别的任务。