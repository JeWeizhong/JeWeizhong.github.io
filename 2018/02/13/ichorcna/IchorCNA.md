---
title: ichorCNA
categories: 
  - 生信
tags:
  - 生信软件
date: 2018-02-13
---
#### 1. 简介

ichorCNA是从超低通全基因组测序（ULP-WGS，0.1x覆盖率）估计无细胞DNA中肿瘤分数的工具。

**描述**

ichorCNA使用概率模型，作为隐马尔可夫模型（HMM）实现，以同时分割基因组，预测大规模拷贝数改变，并估计超低通全基因组测序样本（ULP-WGS ）。ichorCNA针对样本的低覆盖率（〜0.1x）测序进行了优化，并使用患者和健康供体cfDNA样本进行了基准测试。

**用途**

ichorCNA可用于通知肿瘤来源DNA的存在或缺失，并指导决定进行整个外显子或更深的全基因组测序。此外，我们可以使用肿瘤分数的定量估计来校准期望的测序深度，以达到用于鉴定无细胞DNA中的突变的统计功效。最后，ichorCNA可以利用超低通量测序的成本效益方法来检测来自大群体的大规模拷贝数变化。

**文献**

Adalsteinsson, Ha, Freeman, et al. Scalable whole-exome sequencing of cell-free DNA reveals high concordance with metastatic tumors

#### 2. 安装
```
#R控制台中
install.packages("plyr")
source("https://bioconductor.org/biocLite.R")
biocLite("HMMcopy")
biocLite("GenomeInfoDb")
install.packages("devtools")
library(devtools)
install_github("broadinstitute/ichorCNA", "--no-docs")
#依赖的包有很多，安装过程报错信息可以看出需要依赖的其他包
```

#### 3. 使用
```
#以样本DYDFA-842-31为例
$bwa mem -t 4 -B 3 -O 11 -E 4 -k 32 -Y hg19_reference_with_NC.fasta 
DYDFA-842-31/DYDFA-842-31_R1.fq.gz DYDFA-842-31_R2.fq.gz > ichorCNA/test31/31.sam

$samtools view -bS ichorCNA/test31/31.sam > ichorCNA/test31/31.bam

$samtools sort 31.bam > 31.sort.bam &

$samtools  index 31.sort.bam &

$java -jar picard-2.8.2.jar MarkDuplicates I=./31.sort.bam O=./31.rmdup.bam M=rmdup_stat.txt REMOVE_DUPLICATES=true &

$samtools index 31.rmdup.bam  &

hmmcopy_utils-master/bin/readCounter --window 1000000 --quality 20 --chromosome chr1,chr2,chr3,chr4,chr5,chr6,chr7,chr8,chr9,chr10,chr11,chr12,chr13,chr14,chr15,chr16,chr17,chr18,chr19,chr20,chr21,chr22,chrX,chrY 31.rmdup.bam > 31.wig &

$Rscript ichorCNA-master/scripts/runIchorCNA.R --id test31 --WIG test31.wig \
    --gcWig ichorCNA-master/inst/extdata/gc_hg19_1000kb.wig \
    --mapWig ichorCNA-master/inst/extdata/map_hg19_1000kb.wig \
    --centromere ichorCNA-master/inst/extdata/GRCh37.p13_centromere_UCSC-gapTable.txt \
    --normalPanel ichorCNA-master/inst/extdata/HD_ULP_PoN_1Mb_median_normAutosome_mapScoreFiltered_median.rds \
    --normal "c(0.5,0.6,0.7,0.8,0.9)"  --scStates "c(1,3)" \
    --txnE 0.9999 --txnStrength 10000 --outDir ./

```
#### 4. 输出
```
test31
Gender: male
Tumor Fraction: 0
Ploidy: 2.001
Subclone Fraction:      NA
```
![31](/myphoto/clipboard31.png)
```
test32
Gender: male
Tumor Fraction: 0
Ploidy: 2
Subclone Fraction:      NA
```
![32](/myphoto/clipboard32.png)
```
test33
Gender: female
Tumor Fraction: 0
Ploidy: 2
Subclone Fraction:      NA
```
![33](/myphoto/clipboard33.png)
```
test34
Gender: female
Tumor Fraction: 0
Ploidy: 1.999
Subclone Fraction:      NA
```
![34](/myphoto/clipboard34.png)
```
test35
Gender: female
Tumor Fraction: 0.404
Ploidy: 2.98
Subclone Fraction:      0.5017
```
![35](/myphoto/clipboard35.png)
#### 5. 解释结果
#### 6. 帮助文档
```
Rscript runIchorCNA.R --help
Usage: runIchorCNA.R [options]

Options:
--WIG=WIG
	WIG 文件(必须)

--NORMWIG=NORMWIG
	标准 WIG 文件 Default: [NULL]

--gcWig=GCWIG
	GC-content WIG 文件; (必须);软件给出的是hg19的GC WIG文件

--mapWig=MAPWIG
	 WIG 文件  Default: [NULL]

--normalPanel=NORMALPANEL
	深度矫正文件 Default: [NULL]

--exons.bed=EXONS.BED
	外显子bed文件 Default: [NULL]

--id=ID
	样本名 Default: [test]

--centromere=CENTROMERE
	着丝粒位置文件;ichorCNA提供的是hg19版本的. Default: [NULL]

--rmCentromereFlankLength=RMCENTROMEREFLANKLENGTH
    去除着丝粒侧翼的长度. Default: [1e+05]

--normal=NORMAL
	Initial normal contamination; can be more than one value if additional normal initializations are desired. Default: [0.5]
    #初始正常污染; 如果需要额外的正常初始化，可以是多于一个值
--scStates=SCSTATES
	亚克隆状态. Default: [NULL]

--coverage=COVERAGE
	PICARD 软件计算出的覆盖度文件. Default: [NULL]

--lambda=LAMBDA
	Initial Student's t precision; must contain 4 values (e.g. c(1500,1500,1500,1500)); if not provided then will automatically use based on variance of data. Default: [NULL]
    # 初始T-test精确度; 必须包含4个值（例如c（1500,1500,1500,1500））;如果没有提供，则会根据数据的方差自动使用
--lambdaScaleHyperParam=LAMBDASCALEHYPERPARAM
	Hyperparameter (scale) for Gamma prior on Student's-t precision. Default: [3]
    #Gamma之前的超参数（规模）的t精度
--ploidy=PLOIDY
	Initial tumour ploidy; can be more than one value if additional ploidy initializations are desired. Default: [2]
    # 最初的肿瘤倍性; 默认二倍体；
--maxCN=MAXCN
	Total clonal CN states. Default: [7]

--estimateNormal=ESTIMATENORMAL
	Estimate normal. Default: [TRUE]

--estimateScPrevalence=ESTIMATESCPREVALENCE
	Estimate subclonal prevalence. Default: [TRUE]

--estimatePloidy=ESTIMATEPLOIDY
	Estimate tumour ploidy. Default: [TRUE]

--maxFracCNASubclone=MAXFRACCNASUBCLONE
	Exclude solutions with fraction of subclonal events greater than this value. Default: [0.7]
    # 排除大于此值的部分亚克隆事件的解决方案
--maxFracGenomeSubclone=MAXFRACGENOMESUBCLONE
	Exclude solutions with subclonal genome fraction greater than this value. Default: [0.5]
    # 排除亚克隆基因组分数大于此值的解决方案
--minSegmentBins=MINSEGMENTBINS
	Minimum number of bins for largest segment threshold required to estimate tumor fraction; if below this threshold, then will be assigned zero tumor fraction.

--altFracThreshold=ALTFRACTHRESHOLD
	Minimum proportion of bins altered required to estimate tumor fraction; if below this threshold, then will be assigned zero tumor fraction. Default: [0.05]
    # 评估肿瘤分数所需的最大分割阈值的最小分箱数量; 如果低于这个阈值，则将被分配零肿瘤分数
--chrNormalize=CHRNORMALIZE
	Specify chromosomes to normalize GC/mappability biases. Default: [c(1:22)]
    #指定染色体
--chrTrain=CHRTRAIN
	Specify chromosomes to estimate params. Default: [c(1:22)]
    #指定染色体
--chrs=CHRS
	Specify chromosomes to analyze. Default: [c(1:22,"X")]
    #指定染色体
--normalizeMaleX=NORMALIZEMALEX
	If male, then normalize chrX by median. Default: [TRUE]
    # 男性按照X染色体分析
--fracReadsInChrYForMale=FRACREADSINCHRYFORMALE
	Threshold for fraction of reads in chrY to assign as male. Default: [0.001]
    #将chrY中的读数部分的阈值分配为男性
--includeHOMD=INCLUDEHOMD
	If FALSE, then exclude HOMD state. Useful when using large bins (e.g. 1Mb). Default: [FALSE]
    # 如果FALSE，则排除HOMD状态。 使用大容量箱（如1Mb）时很有用
--txnE=TXNE
	Self-transition probability. Increase to decrease number of segments. Default: [0.9999999]
    #　自我转换概率。 增加以减少段数
--txnStrength=TXNSTRENGTH
	Transition pseudo-counts. Exponent should be the same as the number of decimal places of --txnE. Default: [1e+07]
    # 转换伪计数。 指数应与--txnE的小数位数相同
--plotFileType=PLOTFILETYPE
	File format for output plots. Default: [pdf]
    #图片输出格式
--plotYLim=PLOTYLIM
	ylim to use for chromosome plots. Default: [c(-2,2)]
    y轴范围
--outDir=OUTDIR
	Output Directory. Default: [./]

--libdir=LIBDIR
	Script library path. Usually exclude this argument unless custom modifications have been made to the ichorCNA R package code and the user would like to source those R files. Default: [NULL]

```