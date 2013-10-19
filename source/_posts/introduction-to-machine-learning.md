title: Introduction to Machine Learning
date: 2013-10-19 20:25:13
categories: Machine Learning
tags: Machine Learning
---

## 机器学习简介

### 一、定义

> Arthur Samuel (1959): Machine Learning: Field of study that gives computers the ability to learn without being explicitly programmed.
> Tom Mitchell (1998) : Well-posed Learning Problem: A computer program is said to learn from experience E with respect to some task T and some performance measure P, if its performance on T, as measured by P, improves with experience E.

<!-- more -->

个人理解，一句话就是不通过明确的编程让机器具有学习能力，以垃圾邮件为例：
1. 邮件分为垃圾邮件和非垃圾邮件为任务T；
2. 查看哪些邮件被标记为垃圾邮件，以及哪些标记为非垃圾邮件是经验E；
3. 正确识别垃圾邮件或非垃圾邮件比例（或数量）为P。

### 二、机器学习算法的类型

(1) 监督学习(Supervised learning)

1. 概念： 通过生成一个函数将输入映射为一个合适的输出（通常也称为标记，多数情况下训练集都是有人工专家标注生成的）。
2. 理解： 通过分好类的输入，模拟出一个函数，以预测相应的结果产生的概率。
3. 问题： 分类问题（Classification）和回归问题（Regression）
    * 分类问题： 离散型（如预测乳腺癌良性、恶性还有预测明天下午5点会不会下雨），预测的离散输出值(0, 1)。
    * 回归问题： 连续型（如预测房屋价格），预测结果为连续的输出值。
4. 案例：
    * Given genetic (DNA) data from a person, predict the odds of him/her developing diabetes over the next 10 years.
    * Given 50 articles written by male authors, and 50 articles written by female authors, learn to predict the gender of a new manuscript's author (when the identity of this author is unknown).

(2) 无监督学习(Unsupervised learning)

1. 概念： 与有监督学习相比，训练集没有人为标注的结果。
2. 理解： 对输入的训练集没有进行标注，即不告诉你分类情况，让机器自己进行相应的分类，即无监督学习常见算法聚类。
3. 案例： Google News， 基因序列分析，社会网络分析，市场切分等。
4. 特别的例子： 鸡尾酒宴会。
    * 鸡尾酒问题算法： [W,s,v] = svd((repmat(sum(x.*x,1),size(x,1),1).*x)*x');

(3) 半监督学习

介于监督学习与无监督学习之间。

(4) 强化学习(Reinforcement learning)

通过观察来学习如何做出动作，每个动作都会对环境有所影响，而环境的反馈又可以引导该学习算法。

(5) 其他

还有其他包括推荐系统，Transduction，Learning to learn等类型。
