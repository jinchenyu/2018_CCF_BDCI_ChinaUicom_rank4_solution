# 2018 CCF BDCI 中国联通研究院 面向电信行业存量用户的智能套餐个性化匹配模型
第四名解决方案
赛题链接：[面向电信行业存量用户的智能套餐个性化匹配模型](https://www.datafountain.cn/competitions/311/details)

## Contributor
- [clown9804](https://github.com/hclown9804)
- [NeuronBlack_](https://github.com/neuronblack)
- [木有服务器咋挖矿呢](https://github.com/oh-it-is)
- [F](https://github.com/37Feng)

## -1二分类后处理
**为什么要进行后处理？**
首先从本题的评估指标说起

本题使用的评估指标其实就是macro-F1的变种，既先计算macro-F1，再对macro-F1平方，通过平方以拉开选手间差距
而macro-F1最主要的，还是对每个类别计算F1，再平均，也就是说，如果某些类别得分较低，这会对整体F1有较大的影响
![各套餐micro_F1.jpg](https://github.com/jinchenyu/2018_CCF_BDCI_ChinaUicom_rank4_solution/blob/master/images-folde/各套餐micro_F1.jpg)
比如本题中，尾数为830的类别（以下简称830）F1最低，只有可怜的不到0.3，严重拉低了macro-F1的后腿，所以，有必要对830进行后处理，增加830的F1，以达到分数最大化
但如果本题使用其他指标，如micro-F1，计算整体正确错误个数，那么后处理的作用微乎其微

为什么用二分类？
![confusion_matrix.jpg](https://github.com/jinchenyu/2018_CCF_BDCI_ChinaUicom_rank4_solution/blob/master/images-folde/confusion_matrix.jpg)



