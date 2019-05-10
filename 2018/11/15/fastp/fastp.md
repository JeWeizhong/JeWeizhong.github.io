---
title: (转)fastp： 一款超快速全功能的FASTQ文件自动化质控+过滤+校正+预处理软件
categories: 
  - 生信
tags:
  - 生信理论基础
  - 软件
date: 2018-11-15
---


软件作者简介：陈实富，海普洛斯 联合创始人 / CTO

文章转载自：https://zhuanlan.zhihu.com/p/33601691


## 1引言

各位做生信的小伙伴都知道，对于下机的FASTQ数据需要进行质控和预处理，以保证下游分析输入的数据都是干净可靠的。通常我们都是使用FASTQC等软件进行质控，使用cutadapt软件去除接头，使用Trimmomatic等软件进行剪裁，然后使用一些自已开发的脚本进行过滤。这一过程可能需要使用多个软件，相当繁琐，而且速度较慢，这些软件大多又不支持多线程，遇到较大的FASTQ文件，处理起来可真是让人等得心急如焚。

所以今天给大家介绍一款新的软件，名字叫fastp，它可以仅仅扫描FASTQ文件一次，就完成比FASTQC+ cutadapt + Trimmomatic 这三个软件加起来的功能还多很多的功能，而且速度上比仅仅使用Trimmomatic一个软件还要快3倍左右，因为它使用C++开发，处处使用了高效算法，而且完美支持多线程！
github地址请戳：https://github.com/OpenGene/fastp

##2功能特点

该软件的功能列表：
-    对数据自动进行全方位质控，生成人性化的报告。
-    过滤功能（低质量，太短，太多N……）。
-    对每一个序列的头部或尾部，计算滑动窗内的质量均值，并将均值较低的子序列进行切除（类似Trimmomatic的做法，但是快非常多）。
-    全局剪裁 （在头/尾部，不影响去重），对于Illumina下机数据往往最后一到两个cycle需要这样处理。
-    去除接头污染。厉害的是，你不用输入接头序列，因为算法会自动识别接头序列并进行剪裁。
-    对于双端测序（PE）的数据，软件会自动查找每一对read的重叠区域，并对该重叠区域中不匹配的碱基对进行校正。
-    去除尾部的polyG。对于Illumina NextSeq/NovaSeq的测序数据，因为是两色法发光，polyG是常有的事，所以该特性对该两类测序平台默认打开。
-    对于PE数据中的overlap区间中不一致的碱基对，依据质量值进行校正
-    可以对带分子标签（UMI）的数据进行预处理，不管UMI在插入片段还是在index上，都可以轻松处理。
-    可以将输出进行分拆，而且支持两种模式，分别是指定分拆的个数，或者分拆后每个文件的行数。

##3轻松上手

SE:

```
fastp -i in.fq -o out.fq
```

PE:

```
fastp -i in.R1.fq -o out.R1.fq -I in.R2.fq -O out.R2.fq
fastp-i in.R1.fq.gz -I in.R2.fq.gz -o out.R1.fq.gz -O out.R2.fq.gz
```

##4安装fastp

```
git clone https://github.com/OpenGene/fastp.git
cd fastp
make
sudo make install
```

##5功能介绍
###5.1过滤

fastp可以对低质量序列，较多N的序列，该功能默认是启用的，但可以使用`-Q`参数关闭。使用`-q`参数来指定合格的phred质量值，比如`-q 15`表示质量值大于等于Q15的即为合格，然后使用`-u`参数来指定最多可以有多少百分比的质量不合格碱基。比如`-q 15 -u 40`表示一个read最多只能有40%的碱基的质量值低于Q15，否则会被扔掉。使用`-n`可以限定一个read中最多能有多少个N。

fastp还默认启用了read长度过滤，但可以使用`-L`参数关闭。使用`-l`参数指定最低要求一个read有多长，比如`-l 30`表示低于30个碱基的read会被扔掉。这个功能可以用于实现常用的discard模式，以保证所有输出的序列都一样长。
在fastp的HTML报告中，最头上的Summary表格很清楚地显示了过滤的统计信息，如下图所示：
![5.1](https://pic2.zhimg.com/80/v2-6920178b4b210ff1e37089ff8e1b289d_hd.jpg)

###5.2接头处理
接头（adapter）污染的处理是FASTQ文件预处理中很重要的一步。fastp默认启用了接头处理，但是可以使用`-A`命令来关掉。fastp可以自动化地查找接头序列并进行剪裁，也就是说你可以不输入任何的接头序列，fastp全自动搞定了！对于SE数据，你还是可以`-a`参数来输入你的接头，而对于PE数据则完全没有必要，fastp基于PE数据的overlap分析可以更准确地查找接头，去得更干净，而且对于一些接头本身就有碱基不匹配情况处理得更好。fastp对于接头去除会有一个汇总的报告，如下图所示：
![5.2](https://pic1.zhimg.com/80/v2-202f214ad93c99a305f6b3510ddf3f78_hd.jpg)

###5.3滑窗质量剪裁
很多时候，一个read的低质量序列都是集中在read的末端，也有少部分是在read的开头。fastp支持像Trimmomatic那样对滑动窗口中的碱基计算平均质量值，然后将不符合的滑窗直接剪裁掉。使用`-5`参数开启在5’端，也就是read的开头的剪裁，使用`-3`参数开启在3’端，也就是read的末尾的剪裁。使用`-W`参数指定滑动窗大小，默认是4，使用`-M`参数指定要求的平均质量值，默认是20，也就是Q20。

###5.4 PE数据的碱基校正
fastp支持对PE数据的每一对read进行分析，查找它们的overlap区间，然后对于overlap区间中不一致的碱基，如果发现其中一个质量非常高，而另一个非常低，则可以将非常低质量的碱基改为相应的非常高质量值的碱基值，如下图所示：
![5.4](https://pic4.zhimg.com/80/v2-78e31fe48bbdc53f5c1a1427b6170a53_hd.jpg)

上图中所示的标红的T碱基是低质量序列，和高质量的A不匹配，它会被校正为A。该校正功能默认没有开启使用`-c`参数可以启用，对于一些对噪声容忍度低的应用，比如液体活检，建议开启。

###5.5全局剪裁
fastp可以对所有read在头部和尾部进行统一剪裁，该功能在去除一些测序质量不好的cycle比较有用，比如151*2的PE测序中，最后一个cycle通常质量是非常低的，需要剪裁掉。使用`-f`和`-t`分别指定read1的头部和尾部的剪裁，使用`-F`和`-T`分别指定read2的头部和尾部的剪裁。

###5.6 polyG剪裁
对于两色发光法的Illumina设备（NextSeq /NovaSeq），因为在没有光信号情况下base calling的结果会返回G，所以在序列的尾端可能会出现较多的polyG，需要被去除。fastp会自动化地识别NextSeq / NovaSeq的数据，然后进行polyG识别和剪裁。如果你想强制开启该功能，可以指定`-g`参数，如果想强制关闭该功能，则可以指定`-G`参数。

###5.7分子标签UMI处理
UMI在处理ctDNA类似的超低频突变检测应用中是十分有用的，为了更好地对带UMI的FASTQ文件进行预处理，fastp也很好地支持了UMI预处理功能。该功能默认没有启用，需要使用`-U`参数开启，另外需要使用`--umi_loc`来指定UMI所在的位置，它可以是（index1、 index2、 read1、 read2、 per_index、 per_read ）中的一种，分别表示UMI是在index位置上，还是在插入片段中。如果指定了是在插入序列中，还需要使用 `--umi_len` 参数来指定UMI所占的碱基长度。

### 5.8输出文件切分
很多时候我们需要对输出的FASTQ进行切分，分成大小均匀的多个文件，这样可以使用比对软件并行地比对，提高并行处理的速度。fastp软件也提供了相应的功能，并且支持了两种模式，分别是使用参数`-s`指定切分后文件的个数，或者使用`-S`参数指定每个切分后文件的行数。

##6质控报告解读
接下来，我们再看一下如何理解fastp生成的质控报告。fastp的报告在单一文件中同时包含了过滤前和过滤后的统计结果，如果是PE数据，则同时包含了read1和read2的统计结果。之前我们已经说过了，fastp会生成HTML的报告和JSON格式的报告。HTML报告的默认文件名是fastp.html，但是可以通过-h参数修改，JSON报告的默认文件名是fastp.json，但是可以通过-j参数修改。而且fastp报告还有一个标题，默认是fastp report，这个也可以通过-R参数修改为你想要的标题。JSON格式的报告是优化过的，人机皆可读，适合进阶的用户使用程序解析，而这里我们重点关注HTML格式的报告。

###6.1质量分布曲线图
我们第一关注的当然是质量，所以fastp提供了质量分布曲线，即每一个cycle的平均质量值，而且fastp同时提供了A/T/C/G四种不同碱基的平均质量，以及总的平均质量，如下图所示：
![6.1](https://pic3.zhimg.com/80/v2-ee5358c3a2a93af49171459278aa751e_hd.jpg)
![6.1_1](https://pic3.zhimg.com/80/v2-ee5358c3a2a93af49171459278aa751e_hd.jpg)

从上图我们可以看到，一共有5条曲线，分别是A/T/C/G和mean。而且HTML报告中的每一个项目和分项目都是可以点击进行隐藏和展开的。

###6.2碱基含量分布曲线
和质量分布曲线类似，碱基含量分布曲线也是按照每一个cycle来的，显示了每一个位置的碱基含量。如下图所示：
![6.2](https://pic2.zhimg.com/80/v2-c2ca5f1b36bf7c9010d43773dffaf66d_hd.jpg)
![6.2_1](https://pic2.zhimg.com/80/v2-4d30aaf1dfe395d89d36d9ae5c3e135d_hd.jpg)

从图中可以看到，fastp同时显示了A/T/C/G/N/GC的每一个位置的比例和总的比例。而且如果你觉得头部那里比较乱看不清的话，可以用鼠标拉一个框，它就放大了

###6.3 KMER统计表格
fastp对5个碱基长度的所有组合的出现次数进行了统计，然后把它放在了一张表格中，表格的每一个元素为深背景白字，背景越深，则表示重复次数越多。这样，一眼望去，就可以发现有哪一些异常的信息。
![6.3](https://pic3.zhimg.com/80/v2-a69e014cd516d44ac2bb0d3cb8d6d67e_hd.jpg)

从上面的KMER表格中，我们可以发现，GGGGG的颜色特别深，从鼠标移上去之后显示的信息中我们可以发现它的出现次数是平均次数的12.8倍，这是不正常的，因为GGGGG的正常倍数应该在1倍左右。幸好我们有fastp，它可以过滤掉这种polyG，让数值较多地回归正常。

###6.4过表达序列(overrepresented sequence)
fastp的最新版本（v0.12）提供了overrepresetned
sequence的分析，而且不但提供了这些overrepresented sequence的序列个数和占比，还提供了他们在测序cycles中的分布情况，这有利于分析各种问题

##7 完整参数

使用示例：
```
fastp -i R1.fastq.gz -I R2.clean.fastq.gz -o R1.clean.fastq.gz -O R2.clean.fastq.gz -f 5 -F 5 -3 -W 8 -M 25 -q 20 -u 25 -n 3 -l 50 -c -g --poly_g_min_len 6 -x -y -Y 20 -j out.json -h out.html --adapter_sequence GATCGGAAGAGCACACGTCTGAACTCCAGTCAC --adapter_sequence_r2 AGATCGGAAGAGCGTCGTGTAGGGAAAGAGTGT > fastp.log
```
```
$fastp
fastp: an ultra-fast all-in-one FASTQ preprocessor
version 0.19.3
usage: fastp --in1=string [options] ...
options:
  -i, --in1                            read1 input file name (string)
  -o, --out1                           read1 output file name (string [=])
  -I, --in2                            read2 input file name (string [=])
  -O, --out2                           read2 output file name (string [=])
  -6, --phred64                        indicate the input is using phred64 scoring (it'll be converted to phred33, so the output will still be phred33)
  -z, --compression                    compression level for gzip output (1 ~ 9). 1 is fastest, 9 is smallest, default is 4. (int [=4])
      --stdout                         stream passing-filters reads to STDOUT. This option will result in interleaved FASTQ output for paired-end input. Disabled by defaut.
      --interleaved_in                 indicate that <in1> is an interleaved FASTQ which contains both read1 and read2. Disabled by defaut.
      --reads_to_process               specify how many reads/pairs to be processed. Default 0 means process all reads. (int [=0])
      --dont_overwrite                 don't overwrite existing files. Overwritting is allowed by default.
  -V, --verbose                        output verbose log information (i.e. when every 1M reads are processed).
  -A, --disable_adapter_trimming       adapter trimming is enabled by default. If this option is specified, adapter trimming is disabled
  -a, --adapter_sequence               the adapter for read1. For SE data, if not specified, the adapter will be auto-detected. For PE data, this is used if R1/R2 are found not overlapped. (string [=auto])
      --adapter_sequence_r2            the adapter for read2 (PE data only). This is used if R1/R2 are found not overlapped. If not specified, it will be the same as <adapter_sequence> (string [=])
  -f, --trim_front1                    trimming how many bases in front for read1, default is 0 (int [=0])
  -t, --trim_tail1                     trimming how many bases in tail for read1, default is 0 (int [=0])
  -F, --trim_front2                    trimming how many bases in front for read2. If it's not specified, it will follow read1's settings (int [=0])
  -T, --trim_tail2                     trimming how many bases in tail for read2. If it's not specified, it will follow read1's settings (int [=0])
  -g, --trim_poly_g                    force polyG tail trimming, by default trimming is automatically enabled for Illumina NextSeq/NovaSeq data
      --poly_g_min_len                 the minimum length to detect polyG in the read tail. 10 by default. (int [=10])
  -G, --disable_trim_poly_g            disable polyG tail trimming, by default trimming is automatically enabled for Illumina NextSeq/NovaSeq data
  -x, --trim_poly_x                    enable polyX trimming in 3' ends.
      --poly_x_min_len                 the minimum length to detect polyX in the read tail. 10 by default. (int [=10])
  -5, --cut_by_quality5                enable per read cutting by quality in front (5'), default is disabled (WARNING: this will interfere deduplication for both PE/SE data)
  -3, --cut_by_quality3                enable per read cutting by quality in tail (3'), default is disabled (WARNING: this will interfere deduplication for SE data)
  -W, --cut_window_size                the size of the sliding window for sliding window trimming, default is 4 (int [=4])
  -M, --cut_mean_quality               the bases in the sliding window with mean quality below cutting_quality will be cut, default is Q20 (int [=20])
  -Q, --disable_quality_filtering      quality filtering is enabled by default. If this option is specified, quality filtering is disabled
  -q, --qualified_quality_phred        the quality value that a base is qualified. Default 15 means phred quality >=Q15 is qualified. (int [=15])
  -u, --unqualified_percent_limit      how many percents of bases are allowed to be unqualified (0~100). Default 40 means 40% (int [=40])
  -n, --n_base_limit                   if one read's number of N base is >n_base_limit, then this read/pair is discarded. Default is 5 (int [=5])
  -L, --disable_length_filtering       length filtering is enabled by default. If this option is specified, length filtering is disabled
  -l, --length_required                reads shorter than length_required will be discarded, default is 15. (int [=15])
      --length_limit                   reads longer than length_limit will be discarded, default 0 means no limitation. (int [=0])
  -y, --low_complexity_filter          enable low complexity filter. The complexity is defined as the percentage of base that is different from its next base (base[i] != base[i+1]).
  -Y, --complexity_threshold           the threshold for low complexity filter (0~100). Default is 30, which means 30% complexity is required. (int [=30])
      --filter_by_index1               specify a file contains a list of barcodes of index1 to be filtered out, one barcode per line (string [=])
      --filter_by_index2               specify a file contains a list of barcodes of index2 to be filtered out, one barcode per line (string [=])
      --filter_by_index_threshold      the allowed difference of index barcode for index filtering, default 0 means completely identical. (int [=0])
  -c, --correction                     enable base correction in overlapped regions (only for PE data), default is disabled
      --overlap_len_require            the minimum length of the overlapped region for overlap analysis based adapter trimming and correction. 30 by default. (int [=30])
      --overlap_diff_limit             the maximum difference of the overlapped region for overlap analysis based adapter trimming and correction. 5 by default. (int [=5])
  -U, --umi                            enable unique molecular identifer (UMI) preprocessing
      --umi_loc                        specify the location of UMI, can be (index1/index2/read1/read2/per_index/per_read, default is none (string [=])
      --umi_len                        if the UMI is in read1/read2, its length should be provided (int [=0])
      --umi_prefix                     if specified, an underline will be used to connect prefix and UMI (i.e. prefix=UMI, UMI=AATTCG, final=UMI_AATTCG). No prefix by default (string [=])
      --umi_skip                       if the UMI is in read1/read2, fastp can skip several bases following UMI, default is 0 (int [=0])
  -p, --overrepresentation_analysis    enable overrepresented sequence analysis.
  -P, --overrepresentation_sampling    one in (--overrepresentation_sampling) reads will be computed for overrepresentation analysis (1~10000), smaller is slower, default is 20. (int [=20])
  -j, --json                           the json format report file name (string [=fastp.json])
  -h, --html                           the html format report file name (string [=fastp.html])
  -R, --report_title                   should be quoted with ' or ", default is "fastp report" (string [=fastp report])
  -w, --thread                         worker thread number, default is 2 (int [=2])
  -s, --split                          split output by limiting total split file number with this option (2~999), a sequential number prefix will be added to output name ( 0001.out.fq, 0002.out.fq...), disabled by default (int [=0])
  -S, --split_by_lines                 split output by limiting lines of each file with this option(>=1000), a sequential number prefix will be added to output name ( 0001.out.fq, 0002.out.fq...), disabled by default (long [=0])
  -d, --split_prefix_digits            the digits for the sequential number padding (1~10), default is 4, so the filename will be padded as 0001.xxx, 0 to disable padding (int[=4])
  -?, --help                           print this message

```