---
title: SAM/BAM格式说明
categories: 
  - 生信
tags:
  - 生信理论基础
  - 文件格式
date: 2018-02-13
---

**SAM**是一种序列比对格式标准，由sanger制定，是以TAB为分割符的文本格式。 主要应用于测序序列mapping到基因组上的结果表示，当然也可以表示任意的多重比对结果。 SAM的全称是sequence alignment/map format

##### 定义和示例

SAM分为两部分，注释信息（header section ）和比对结果部分 （alignment section）。 通常是把FASTQ文件格式的测序数据比对到对应的参考基因组版本得到的。 注释信息并不是SAM文件的重点，是该SAM文件产生以及被处理过程的一个记录，规定以@开头，用不同的tag表示不同的信息，主要有：

- @HD，说明符合标准的版本、对比序列的排列顺序；
- @SQ，参考序列说明；
- @RG，比对上的序列（read）说明；
- @PG，使用的程序说明；
- @CO，任意的说明信息。
一个简单的SAM文件例子如下：
```
ST-E00317:118:HNHK2CCXX:7:1101:5071:1309	97	chr1	196695666	60	147M	chr18	54806715	0	AAGAATATGGACACAGTGAAGTGGTGGAATATTATTGCAATCCTAGATTTCTAATGAAGGGACCTAATAAAATTCAGTGTGTTGATGGAGAGTGGACAACTTTACCAGTGTGTATTTGTAATGTATAAAACATTAATATTGAAACTT	FFAKKKKKKFKKKKKK,FKKKKKKK<KKKFKK,FKKKKKKKKKFFKKKKKFFKKFAKFK,FKKKAKKKKKKKKKKKAF,,,FFAFA,FKKAKAA7FKKFKFFFKKKK7FFKK7F7K,7FAFKAFFKKKKK,7AFKFKFA,,,AAK,A	MC:Z:13S134M	MD:Z:76A39G13T9T6	RG:Z:sampleID	NM:i:4	AS:i:127	XS:i:21
```
说明如下：
```
QNAME   ST-E00317:118:HNHK2CCXX:7:1101:5071:1309      
## read名称
FLAG    97  
##  FLAG, 概括出一个合适的标记，各个数字分别代表
##1     序列是一对序列中的一个
##2     比对结果是一个pair-end比对的末端
##4     没有找到位点
##8     这个序列是pair中的一个但是没有找到位点
##16   在这个比对上的位点，序列与参考序列反向互补
##32   这个序列在pair-end中的的mate序列与参考序列反响互补
##64   序列是 mate 1
##128 序列是 mate 2
##假如说标记为以上列举出的数目，就可以直接推断出匹配的情况。假如说标记不是以上列举出的数字，比如说83=（64+16+2+1），就是这几种情况值和。
##　其他情况可参见 http://broadinstitute.github.io/picard/explain-flags.html
RNAME  chr1 
## 染色体名称
POS 196695666   
## 染色体位置
MAQ 60  
## mapping质量 越高说明位点越独特
CIGAR   147M 
## M”表示 match或 mismatch；
## “I”表示 insert；
## “D”表示 deletion；
## “N”表示 skipped（跳过这段区域）；
## “S”表示 soft clipping（被剪切的序列存在于序列中）；
##“H”表示 hard clipping（被剪切的序列不存在于序列中）；
##“P”表示 padding；
##“=”表示 match；
##“X”表示 mismatch（错配，位置是一一对应的）；
MRNM    chr18
## read2 在参考序列上的位置，如果没有就用 "*", 相同"="
MPOS    0 
##read2 的位置，若不可用则用0
ISIZE   0
## emplate的长度，最左边得为正，最右边的为负，中间的不用定义正负，不分区段（single-segment)的比对上，或者不可用时，此处为0
SEQ AAGAATATGGACACAGTGAAGTGGTGGAATATTATTGCAATCCTAGATTTCTAATGAAGGGACCTAATAAAATTCAGTGTGTTGATGGAGAGTGGACAACTTTACCAGTGTGTATTTGTAATGTATAAAACATTAATATTGAAACTT
##　序列片段的序列信息
QUAL    FFAKKKKKKFKKKKKK,FKKKKKKK<KKKFKK,FKKKKKKKKKFFKKKKKFFKKFAKFK,FKKKAKKKKKKKKKKKAF,,,FFAFA,FKKAKAA7FKKFKFFFKKKK7FFKK7F7K,7FAFKAFFKKKKK,7AFKFKFA,,,AAK,A
## 序列的质量信息，格式同FASTQ一样。read质量的ASCII编码
MC:Z:13S134M    MD:Z:76A39G13T9T6       RG:Z:sampleID   NM:i:4  AS:i:127        XS:i:21
## 可选字段（optional fields)，格式如：TAG:TYPE:VALUE，其中TAG有两个大写字母组成，每个TAG代表一类信息，每一行一个TAG只能出现一次，TYPE表示TAG对应值的类型，可以是字符串、整数、字节、数组等。
## AS:i 匹配的得分
## XS:i 第二好的匹配的得分
## YS:i mate 序列匹配的得分
## XN:i 在参考序列上模糊碱基的个数
## XM:i 错配的个数
## XO:i gap open的个数
## XG:i gap 延伸的个数
## NM:i 经过编辑的序列 
## YF:i 说明为什么这个序列被过滤的字符串
## YT:Z
## MD:Z 代表序列和参考序列错配的字符串
## 
```
##### SAM要处理好的问题
- 非常多序列（read)，mapping到多个参考基因组（reference）上
- 同一条序列，分多段（segment）比对到参考基因组上
- 无限量的，结构化信息表示，包括错配、删除、插入等比对信息

要注意的几个概念，以及与之对应的模型：

- reference
- read
- segment
- **++template（参考序列和比对上的序列共同组成的序列为template）++**
- alignment
- seq
- 


**参考链接**：
1. [SAM(file format)](https://en.wikipedia.org/wiki/SAM_(file_format))(可下载PDF文件)
2. https://genome.sph.umich.edu/wiki/SAM#What_are_TAGs.3F
3. https://mp.weixin.qq.com/s/yK1OyJHrePg6bWl41JCpvA
4. http://www.cnblogs.com/emanlee/p/5366610.html