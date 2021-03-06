title: 隐马尔科夫模型介绍
date: 2013-10-15 17:26:30
categories: 机器学习
tags:
---
这篇博客是根据我 NLP课程 的一个小作业整理而来的，介绍一下 NLP 领域中非常经典的一个模型，叫做隐马尔可夫模型，简称 HMM。看过吴军老师的《数学之美》的同学应该对这个模型不感到陌生。这次的作业，老师给出一本书*Foundations of statistical natural language processing*，让我们自己挑选任意一部分的内容，不限字数，写篇文章。前面部分多是介绍语言学的基础知识，我选择了比较偏算法的 HMM。由于 NLP 的课程刚刚开始，远没有讲到这里，老师提供的参考书又是电子版的英文原版，看起来比较费劲，我更多的参考了一些其他资料，文末会列出。

分割线后是正文。

---HMM（隐马尔可夫模型）是 NLP（自然语言处理）领域中一个基本且常用的模型，可以用在诸如词性标注、中文分词、语音识别等细分的研究方向，在工业中的应用也已经十分广泛。本文将首先介绍 MM（马尔可夫模型），然后着重介绍 HMM 的原理、涉及到的基本问题，并给出比较形象的例子。
<!--more-->###马尔可夫模型提出：1913年由Andrei A. Markov提出，也因此得名。模型描述：这里引用网上的一段数学表示，稍后做出解释。![](http://ww3.sinaimg.cn/large/5e8cb366jw1e9m07iqbigj20bi03l0t4.jpg) 通俗地解释，随机序列 X 在不同时刻有不同的状态，在 t 时刻的状态受 t 之前所有时刻（t-1,t-2…0）的状态的影响；而在马尔可夫模型中，我们规定，t 时刻的状态只受 t-1时刻状态的影响，也就是不考虑更加之前的状态，这无疑会给我们解决问题带来方便。另外，我们用 P 来表示状态转移概率，亦即从 t-1时刻到 t时刻两个状态转换的可能性大小。之所以说「一阶」，是因为某状态只受前一个状态的影响，这也可以被称为2-gram；之所以说「链」，是因为 ai 受到 ai-1的影响，用 i 迭代，就像一个链式的反应。这个模型可以更加凝练地表示成一个二元组，形式为（S，A）。其中，S 表示所有可能的状态的集合；A 是一个 n 行 n 列的状态转移概率矩阵，每个元素 aij 表示的是从状态 i 转移到状态 j的概率。举个例子：假设我们观察天气，通过对历史数据的了解，我们知道只有三种天气状态：{阴，雨，晴}；同时，某天的天气只受前一天天气的影响，我们也从历史数据中得到了一个转移概率组成的矩阵（3*3）。有了这些已知条件，我们就可以预测某天的天气了。这里需要注意的是，我们所关心的状态（天气情况）和我们观察到的序列（天气情况）是一致的，给定了后者，前者便得到了。之所以讲这些，是为了引出 HMM，这一点正是 MM 和 HMM 的关键区别所在。###隐马尔可夫模型上面给出了马尔可夫模型，我们可以用它来做简单的预测问题，但是还有一些复杂的问题，用 MM 就束手无策了，下面通过一个简单的例子给出。假设有 N 个坛子，每个坛子表示一个状态；有 M 种颜色的小球，每个坛子中不确定会拿出哪种颜色的小球；现在我们依次随记从某个坛子中拿出小球，并记录小球的颜色，得到一个颜色序列；现在把这个观察得来的序列给另外一个人，它只知道颜色的产生顺序，不了解每个颜色分别是从哪个坛子里取出的。从这个例子看出，给出了观察序列，我们无法直接得知状态的序列；而上面天气的例子中，给出观察序列（天气），我们直接得到状态序列（天气）。本例中，状态的转移过程对于观察者是隐藏起来的，所以这个过程叫做「隐马尔可夫过程」。模型描述：HMM可以表示为一个五元组，我们用λ=（S，V，A，B，π）表示。
S：状态集合，对于上例，就是坛子的集合，共 n 个元素。
V：观察集合，对于上例，就是小球颜色的集合，共 m个元素。
A：状态转移矩阵，涵义同 MM 中的 A。B：观察值的概率分布，对于上例，就是每个坛子里，取出每种颜色的小球的概率，可以看成一个 n*m 的矩阵。π：初始状态概率分布，可以理解为每个状态的先验概率。通过马尔可夫过程，可以生成一个符号序列（观察序列），我们用如下方式表示：  O = （o1,o2,…ot）
该过程就是通过上述的五元组产生该序列的，可以简单地表示成下面过程：  按照π选择一个初始状态—> 按照 B 选择一个输出值 o—> 按照 A 选择下一个状态…  如此迭代进行，便可得出序列。HMM 中有三个基本问题，本文将通过一个直观的例子，提出这三个问题。问题描述：有三枚硬币（1，2，3），随机抛掷，记录结果。这里，S 为{1，2，3}；V 为{H，T}（Head or Tail，硬币的正反）；A、B、π也给出，如下：（为了方便，这里引用资料中的截图）![](http://ww1.sinaimg.cn/large/5e8cb366jw1e9m0920ytpj20bi04cwep.jpg) 通过这个直观的例子，我们也能更加清晰地理解 HMM 的五元组代表的意思。接下来，我们提出三个问题：1.	给定上述模型，观察到的结果（硬币的正反面）为O={H，T，H}的概率是多少？2.	若给定上述的观察结果，那么最可能的状态序列（硬币序号）是什么？3.	A、B、π未知的情况下，如何根据 O 得到它们？问题一是个估算问题，P（O|λ），可用向前算法和向后算法解决；  问题二是根据观察序列反向找出最可能的状态序列，是个解码问题，P（O,q|λ），q 是某状态，最常用的是 Viterbi算法；  问题三实际上是个参数估计或求解的问题，或者说是个学习问题，通过训练一步步优化参数，找到使得P（O|λ）最大的那个参数，最常用的是 Baum-Welch算法。当然，具体的算法描述和实现也是一大块内容，这里不另辟篇幅叙述了。###总结
回顾一下，我们首先介绍了 HMM 的基础—MM；然后通过例子给出了为什么MM 是不够的，为什么需要 HMM；稍后给出了 HMM 的模型描述，并通过硬币的例子，一方面形象化了 HMM，另一方面引出了 HMM的三个基本问题；至于每个问题的解答，需要研究一下上述的几个算法，在此基础上就可以用来解决中文分词、词性标注等领域的一些问题了。尤为重要的是，我最近在研究的一个开源中文分词系统「结巴分词」，在新词预测时就是使用的 Viterbi 算法，最后的效果还是很好的，这个算法应该认真学习。---
参考资料：1. Foundations of statistical natural language processing. Christopher D.Manning, Hinrich Schütze.2. 《统计自然语言处理基础》，参考资料条目1的中文版。3.  北京大学《计算语言学》第五章课件：http://www.icl.pku.edu.cn/member/chbb/lecture/cl/Computational_Linguistics_05.pdf4.	http://www.52nlp.cn/hmm-concrete-example-on-wiki5.	「结巴分词」：https://github.com/fxsjy/jieba