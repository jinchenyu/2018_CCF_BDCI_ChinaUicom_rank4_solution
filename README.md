# 2018 CCF BDCI 中国联通研究院 面向电信行业存量用户的智能套餐个性化匹配模型
第四名解决方案
赛题链接：[面向电信行业存量用户的智能套餐个性化匹配模型](https://www.datafountain.cn/competitions/311/details)

## Contributor
- [clown9804](https://github.com/hclown9804)
- [NeuronBlack_](https://github.com/neuronblack)
- [木有服务器咋挖矿呢](https://github.com/oh-it-is)
- [F](https://github.com/37Feng)

## 特征工程与模型
特征方面较为基本，没什么较大亮点，不再多说

模型方面，选用lightgbm，最终使用复赛模型，和全量模型进行加权融合，最后进行二分类后处理

## 主要开源几个较大上分点
1. 二分类后处理
2. 初赛数据的应用
3. 连续数据的离散处理

## 1. 二分类后处理
**为什么要进行后处理？**
首先从本题的评估指标说起

![官网给出评测指标](https://github.com/jinchenyu/2018_CCF_BDCI_ChinaUicom_rank4_solution/blob/master/images-folde/评分方式.png)

本题使用的评估指标其实就是macro-F1的变种，既先计算macro-F1，再对macro-F1平方，通过平方以拉开选手间差

而macro-F1最主要的，还是对每个类别计算F1，再平均，也就是说，如果某些类别得分较低，这会对整体F1有较大的影响

![各套餐micro_F1.jpg](https://github.com/jinchenyu/2018_CCF_BDCI_ChinaUicom_rank4_solution/blob/master/images-folde/各套餐micro_F1.jpg)

比如本题中，尾数为830的类别（以下简称830）F1最低，只有可怜的不到0.3，严重拉低了macro-F1的后腿，所以，有必要对830进行后处理，增加830的F1，以达到分数最大化

但如果本题使用其他指标，如micro-F1，计算整体正确错误个数，那么后处理的作用微乎其微

**为什么用二分类？**
![confusion_matrix.jpg](https://github.com/jinchenyu/2018_CCF_BDCI_ChinaUicom_rank4_solution/blob/master/images-folde/confusion_matrix.jpg)

根据混淆矩阵可以看到，在830只有10k多个样本时，有接近一半，被错误的分类到尾数为166的套餐（以下简称166）中，而166对830分错较少

![套餐830与166对比](https://github.com/jinchenyu/2018_CCF_BDCI_ChinaUicom_rank4_solution/blob/master/images-folde/830_166对比.png)

再对830和166两类进行EDA，可以看到无论是流量，还是话费，两套餐都呈现出高度一致

所以选择单独对这两类套餐进行二分类，既将830数据视为正样本，166视为负样本对模型进行训练，预测最终模型结果中830套餐为166套餐的概率，并对概率0.5以下进行修改

## 2. 初赛数据在复赛阶段的使用
复赛数据和初赛对比发现套餐出现的比例出现了较大的变化

![初赛复赛分布比例](https://github.com/jinchenyu/2018_CCF_BDCI_ChinaUicom_rank4_solution/blob/master/images-folde/初赛复赛比例.jpg)

对于数据分布差异较大，最好的办法就是嫁接，然而嫁接姿势不对，全员尝试，均无法上分，迫于无奈，只能选择拼接。

但是由于分布不同，如果贸然直接拼接，势必会影响统计特征分布，所以在此根据复赛数据来做统计特征，再将统计特征merge到全量数据中

## 3. 连续数据的离散处理
以用户消费金额为例

![本月用户消费](https://github.com/jinchenyu/2018_CCF_BDCI_ChinaUicom_rank4_solution/blob/master/images-folde/本月用户消费value_counts.jpg)

对total_fee统计发现大量消费集中在56、76、106、136等，而对于正常的数值型数据，数据分布通常比较均匀，不会出现在集中在这几个消费段

![本月消费金额76](https://github.com/jinchenyu/2018_CCF_BDCI_ChinaUicom_rank4_solution/blob/master/images-folde/本月消费金额76value_counts.jpg)

![本月消费金额106](https://github.com/jinchenyu/2018_CCF_BDCI_ChinaUicom_rank4_solution/blob/master/images-folde/本月消费金额106value_counts.jpg)

而在这些消费段，一个值基本区分出89950166,89950167这些套餐，推断每月出账金额是该用户套餐消费的关键。

由此也可以将消费金额看作离散变量处理。

联想到1_total_fee至4_total_fee上，将4个月消费金额拼接，使用CountVectorizer做词频统计。为防止重复数据导致过拟合训练集，过滤词频低于20
