# DF-Early-warning-of-the-wind-power-system Rank2
# DF风机叶片开裂预警单赛道第二名，总决赛二等奖方案分享
注意：本方案包含了
+ 1> 代码审核时提交的部分（能够直接运行）
+ 2> 做线下训练时的代码（包含各种模型）

其中，线下训练时的代码内容较为丰富，由于本人今年要考研的原因，没有过多地去整理，望见谅

# 运行方法：修改config中的数据路径之后，直接运行main.py
1.环境配置和依赖库：
+ python3
+ multiprocessing
+ lightgbm
+ tqdm

2.特征说明：
+ 基本统计特征：每个column对应的的mean, max, min, var, ptp, median
+ 特征总数：75 * 6 = 450
+ 筛选后特征总数：40

3.数据预处理：
+ 1> 把全0行数据替换为均值（当然也可以直接去掉）
+ 2> 先把所有数据除以均值，然后再做相关统计（有利于产生更多有意义的特征组合）

4.训练模型：
+ lightgbm
+ KNN
+ SVM

# 赛题分析
1.任务描述
+ 利用SCADA采集的风机工况数据，对于风机未来一周内是否会发生故障进行预测

2.赛题理解
+ 由于风机开裂故障多发在盛丰期，由此推断风机故障的主要原因是谐振。谐振与风机的机械特性密切相关，每个风机的共振频率都不大一致。
+ 除此之外，谐振的特点是，能够在短时间内造成巨大的破坏力。因此给出的data与label并不是完全准确的对应关系（脏数据）。很有可能的情况是，事故前6天的数据都是正常的，只有最后那一段时间存在异常

3.数据简介
+ train一共有25类风机共4w个样本，test没有风机编号，共8w样本

4.可视化挖掘
![fig1](https://github.com/SY575/DF-Early-warning-of-the-wind-power-system/blob/master/figure/fig1.png)
+ 可以看出数据是明显聚类的（学过大物的朋友应该知道，振动其实是一个机械器件的固有属性）
+ 由此可以对test的数据进行准确率较高的聚类

5.阈值的确定
+ 我们借鉴了OSTU算法的思想。OSTU是用作图像二值化处理的一种算法。
+ 参考OSTU算法的结果，可以更科学地确定划分阈值

关于为什么要按类确定阈值：不同的类别阈值差异较大，使用统一的阈值效果会很差

#### 文末福利
+ 在utils文件夹下的CV.py文件是我用的比较舒服的一个轮子，曾经2天打下了南京赛社保欺诈的第6名。这个轮子的最大优点就是用起来非常简单
+ 提供了并行提取特征的轮子，并行之后大概7分钟能提完所有特征（单核大概要提45分钟的样子）
+ 这几天有时间的话，我会把DeepFFM的轮子也整理一下放上来。DeepFFM与lgb模型差异性很大，传统的数据挖掘比赛，baseline用lgb跑一次，DeepFFM跑一次，融合一下，前20名基本上就没问题了
