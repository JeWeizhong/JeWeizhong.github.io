---
title: 生新基础教程(一)：fastq格式
categories: 
  - 生信
tags:
  - 文件格式
date: 2018-07-07
---

>*转载请注明出处*


**FASTQ格式**是一种保存生物序列（通常为核酸序列）及其测序质量得分信息的文本格式。序列与质量得分皆由单个ASCII字符表示。

**格式**

FASTQ格式通常每个序列使用四行：

- 第一行以“@”字符开头，后面是序列标识符和其他描述。
- 第二行是序列
- 第三行以“+”也是序列标识符和描述（可选）
- 第四行是序列的质量得分信息，与第二行的碱基一一对应

```
@A00262:122:H5FW3DSXX:3:1101:1561:1031 1:N:0:CCGTGAGA
CNACCCCAAAAATGCTTTTGAAATCCTGAGATGTGATCAGTGAAATATGCAGCCAAGGCAAGGGGAAACTGTCCGCAAGTTAAAAAGATTTATTGCTATTCCAGGCTTCAAATGAGCCCAGAACTCAGGGCTGGTGTGTGTTTCAGAAGT
+
F#FFFFFFFFFFFFFFFFFFFFFFFFFFFFF,F:FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF:FFFFFFFFF:FFFFFFFFFFFFFFFFFFF:FFFFFFFFFF:FFFFFFFFFFF:FF,FFFFF:FFFFFFF
```

**Illumina 测序仪标识符**

| A00262    | 测序仪编号 | 
| :--------: | :--------:|
| 122 		|   运行id |
| H5FW3DSXX | flowcell id|
| 3 | lane 编号 |
| 1101 | tile编号 |
| 1561 | tile x坐标 |
| 1031 | tile y坐标 |
|1|单端测序为1，双端为2|
|N|Y过滤reads(reads质量较差),否则为N|

>**ps**: 每个flowcell有8个泳道，一个泳道称为一个Lane，每条Lane上有两列，每列有多个有小格子，叫一个tile。(见下图)

>![flowcell](/myphoto/flowcell.jpg)

**质量评分**

第四行表示序列的质量值,用[ACSII码](https://en.wikipedia.org/wiki/ASCII)表示。
测序仪一般是按照荧光信号来判断所测序的碱基是哪一种的，例如红黄蓝绿分别对应ATCG，因此对每个结果的判断都是一个概率的问题：

| Phred Quality Score(Q值) |错误率 |碱基准确率 |
| :--------:| :--------:| :------: |
| 10 | 0.1  |  90%  |
| 20 | 0.01 | 99% |
| 30 | 0.001 | 99.9% |
| 40 | 0.0001 | 99.99% |
| 50 | 0.00001 | 99.999% |

一般都是以Q值来衡量read碱基质量，Sanger中心用的换算公式如下，其中P为错误率，
$$Q=-10logP $$
Solexa系列测序仪使用不同的公示来计算质量值：$Q=-10log(P/1-P)$
不同的测序平台Q值所能表示的范围不一样，因此要想用对应的ACSII编码，必须加上一个数值(33或者64)
以上面的那条Illumina测序仪产生的read为例，`F`对应的十进制数是70，Q值就是36，也就是说这个碱基的准确率在99.99%以上


**参考链接:**
- http://boyun.sh.cn/bio/?p=1901
- https://en.wikipedia.org/wiki/FASTQ_format
- https://blog.csdn.net/godsunshine/article/details/51946314
