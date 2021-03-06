# One-Class Training for Masquerade Detection

<!-- TOC -->

- [背景知识与启发](#背景知识与启发)
- [Schonlau 数据集](#schonlau-数据集)
- [机器学习方法](#机器学习方法)
    - [朴素贝叶斯分类器](#朴素贝叶斯分类器)
    - [单分类 SVM](#单分类-svm)
- [局限性](#局限性)
- [参考资料](#参考资料)

<!-- /TOC -->

## 背景知识与启发

* 用户行为建模
* 检测异常行为
* “伪装者”问题是一个具有挑战性的问题
    * 引发假警报
* 多分类
    * 每个用户的样本当作一个类别组成数据来源
    * 自身与他人
    * 用户加入/离开机构时需要重新训练
    * 伪装的非自身用户样本会偏离模型的审查
* 单分类
    * 仅用该用户的数据，为本用户构建模型
    * 需要的数据少
    * 分布式实施

## Schonlau 数据集

* 来自70名用户的 Unix shell 命令
    * 使用 Unix `acct` 收集数据
    * 随机选择50名用户作为入侵目标
    * 20名伪装用户
* 每个用户15000个命令 
    * 持续数天或数月
    * 前5000个命令是正常的
    * 接下来的10000个命令，随机注入100个入侵块命令
    * 正常和入侵块的大小：100
* 问题
    * 不同用户的时段差异很大
    * 每个用户的登录会话次数不同
    * 每个用户的入侵块数量不同（0-24）
    * 用户的工作职能未知
    * `acct` 按照命令完成的次序记录日志

## 机器学习方法

* 学习任务
    * 明确的指认伪装者，而不是特定用户

### 朴素贝叶斯分类器

* 贝叶斯定理
* 不同的命令是独立的
* 多变量伯努利模型
    * Unix 下856个唯一的命令
    * 每个块作为一个二进制的N维向量
    * 用伯努利建模每一个维度
    * 在小词汇量下表现更好
* 多项式模型
    * 每个块作为N维向量
    * 每个命令出现的次数作为特征
    * 在大词汇量下表现更好
* 单分类朴素贝叶斯
    * 仅使用用户自身的模型来计算$$p(c_i|u)$$
    * 对于伪装者，假定每个命令的概率都是$$1/N$$（完全随机）
        * 对伪装者不做假设
    * 给定一个块 $$d$$，计算： $$p(d|self)/p(d|non-self)$$
    * 设定阈值控制误报与检测率

### 单分类 SVM

* 将数据映射到高维特征空间
* 最大化分离边界
* 允许异常值，通过参数设置边缘出错的概率

## 局限性

* 使用二值特征的单分类 SVM 表现的比单分类贝叶斯和使用数值特征的单分类 SVM 要好
* 问题依旧困难，准确性有待提高
* 2-gram 特征表现更差
* 1-gram 和 2-grams 同时使用可能提高性能
* 这个系统不应被用作唯一的检测器
* 需要包含命令参数而不仅仅是命令本身

## 参考资料

* One-class Training for Masquerade Detection (2003)
* CS 259D Lecture 3
