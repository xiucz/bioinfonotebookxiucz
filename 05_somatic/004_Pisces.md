[toc]

## 简介
+ 适用于扩增子测序/富集panel
+ 适用于somatic/germline 突变检测

![xx](https://github.com/xiucz/pics/blob/master/20190926.jpeg?raw=true)

下载地址：https://github.com/Illumina/Pisces/releases

Stitcher - Stitches two paired reads together into a single read

Pisces - Calls small variants

Scylla - Detects multiple nucleotide variants (MNVs) in a given sample and phases the variants in complex regions into sub populations

VariantQualityRecalibration - Recalibrates the variant quality scores (Q scores) if the particular variants are over represented


## 安装
1. 下载 **.net core 2.0** 二进制的文件
```
#https://download.visualstudio.microsoft.com/download/pr/886b4a4c-30af-454b-8bec-81c72b7b4e1f/d1a0c8de9abb36d8535363ede4a15de6/dotnet-sdk-3.0.100-linux-x64.tar.gz

#https://dotnet.microsoft.com/download/thank-you/dotnet-sdk-2.2.402-linux-x64-binaries

https://download.visualstudio.microsoft.com/download/pr/46411df1-f625-45c8-b5e7-08ab736d3daa/0fbc446088b471b0a483f42eb3cbf7a2/dotnet-sdk-2.2.402-linux-x64.tar.gz
```

```
mkdir -p $HOME/dotnet && tar zxf dotnet-sdk-3.0.100-linux-x64.tar.gz -C $HOME/dotnet
export DOTNET_ROOT=$HOME/dotnet
export PATH=$PATH:$HOME/dotnet
```
需要注意的是的， .net core 3.0似乎不兼容：
```
$ dotnet Pisces_5.2.10.49/Pisces.dll
It was not possible to find any compatible framework version
The specified framework 'Microsoft.NETCore.App', version '2.0.0' was not found.
```

## 软件版本
+ net core 2.0
+ Pisces_5.2.10.49
+ linux centos7

## 检测逻辑

Pisces本质上是一个管道程序。在原始BAM中处理时，Pisces在每个CIGAR字符串中扫描indel和参考进行不匹配的读取序列，从而建立候选变异列表。跳过不被认为有价值的对齐方式，如secondary or duplicate alignments or low mapping quality。同时，Pisces会建立覆盖矩阵（按碱基类型计算的覆盖连续块）。只有那些超过最低碱基检测质量的碱基才算作observations。当每个块完成时（最后一个相关的reads已从bam中提取），通过查询完整的块中的信息来检测出该块中的所有候选变异。此时，将计算出真实的覆盖深度，频率，最终质量分数，链偏好性等，并执行最终过滤。然后将通过最小报告阈值的变异写入VCF，并处理下一个块。缓冲块处理以实现最佳读写。

默认情况下，Pisces要求用于变异检测的所有碱基的碱基质量分数至少为Q20，对应于0.01的错误率或更少。碱基检测过滤器是可配置的过滤器，可以使其更严格以提高精度。base calls的质量驱动内部噪声模型。例如，当变异检测过程中仅包括Q20以上的base calls时，Pisces将有信心将突变从噪声降至1％的突变频率，这反映在写入VCF的突变质量中。如果将碱基检测质量得分过滤阈值设置为Q30，则Pisces将进行调整，并在检测低频突变（现在降至0.1％）上有更高的可信度。仅当大多数碱基满足质量阈值时，才应碱基质量分数设置为更高。 

默认情况下，我们考虑频率为1％或更高的任何非参考等位基因。根据参考和突变的reads计数，我们基于泊松模型计算SNV的q值，如果SNV的q值低于20，则对SNV进行过滤。如果q值在20到30之间，我们报告该突变在.vcf的过滤器列中带有“ q30”。如果q值大于或等于30，则我们在FILTER列中为突变提供一个清晰的“ PASS”。

插入缺失的处理方式类似：我们查看覆盖给定位置的比对中有多少个包含特定插入缺失（即我们的变异数）与该位置的总体覆盖度。请注意，我们不执行的某些indel重新排列或更复杂的检测变异程序中包含“indel清洗”步骤。

当在体细胞环境中进行基因分型时，Pisces使用“ 0/1”表示在最小检测阈值（默认值为1％）处发现了参考等位基因和其他等位基因。请注意，这不是在二倍体上下文中的同一个“ 0/1”基因型，在该基因型中参考和等位基因分别占大约50％。例如，如果发现突变的频率是80％，而现参考的频率是5%，并且Q分数都可以接受，则这被认为是杂合体细胞突变，**因为两者都存在**。即使在直觉上，用户在二倍体环境中也可以认为这是纯合突变。

## Q-score 计算

Q得分给出了存在变异的可确定程度。较高的q分数代表更大的确定性。我们的q得分计算考虑了噪，覆盖深度和突变计数。更大的突变数和覆盖深度会增加q得分。较大的噪声水平会降低q得分。 （默认情况下，我们使用一个非常简单的噪声模型：基于碱基检测质量阈值，噪声模型始终为1％。用户也可以选择使用“窗口”噪声模型。在这种情况下，q得分计算将基于相邻碱基q得分的平均值。）从技术上讲，我们的q得分范围可以从0到无穷大，但默认情况下，我们将其上限设置为100。
```math
Q = -10*log10(P)
```

## P-value 计算
P值是通过将突变观察数建模为Poisson过程来计算的。“P”对应于突变被错误检测的概率，即假阳性。

## 频率计算
简而言之，突变频率是以突变位置突变总数除以覆盖深度。（在数学上，泊松分布的lambda的最大似然估计是观察计数本身。即我们观察到的潜在变异频率的最佳估计是我们观察到的值。http://en.wikipedia.org/wiki/Poisson_distribution#Parameter_estimation）。

### 突变计数 覆盖率计数：

某个特定位置的覆盖度是通过过滤条件（比对质量，碱基质量）的所有reads总和，作为vcf文件中的DP值给出。从3.5.5版本后，未通过过滤条件的reads也被算在覆盖度里面。SNV计数的过滤方式和覆盖度计数的过滤方式一样。而Indel/MNV计数是所有通过过滤条件的reads的总和。估计的参考覆盖度是总覆盖度减去突变覆盖度。

因为mapping quality（默认阈值为1）， base quality（默认阈值为2）带来的过滤，使得bam 文件里的原始深度大于或等于vcf中报道的AD值，这也就是gatk中所说的informative reads。


### 过滤

## 基因组索引
以gatk bundle里的参考基因组为例，在Pisces_hg19文件夹里生成GenomeSize.xml/ucsc.hg19.dict/ucsc.hg19.fasta.fai：
> 建立参考基因组索引时，最好新建一个空文件夹，然后进入并执行以下

```
$ ls
ucsc.hg19.fasta

$ dotnet CreateGenomeSizeFile_5.2.10.49/CreateGenomeSizeFile.dll -g ./ -s "Homo sapiens (UCSC xiucz)" -o ./
```
## 变异检测
```
dotnet Pisces_5.2.10.49/Pisces.dll \
-g ~/hg19/gatk_bundle/Pisces/ \
--callmnvs true --maxgapbetweenmnv 1 \
--outputsbfiles true  --maxthreads 4 \
-gvcf false \
-i ~/example.bed \
-bam sample.recalibrated.bam \
-outFolder ./output   

#需要注释的是，bam文件索引需要sample.recalibrated.bam.bai, 而不能sample.recalibrated.bai.
```

## QaA
### base call背景噪音
默认情况下，所有位点的噪音阈值为Q=20 或者1%，这是因为在我们对计算中的reads设置了碱基质量分数的过滤阈值为20。在突变质量分数模型中，使用了噪音等级和突变深度来确定突变的可信度。

### strand bias计算
To compute the strand bias, we start by evaluating evidence for the variant using only the reads from one strand at a time, applying the same Poisson probability distributions that are used to compute the Q-score of the variant (using pooled data from both strands). This data is used to compute a test statistic based on likelihood ratios. The directional bias for the strand 1 is computed using this formula:
CHINESE (SIMPLIFIED)
为了计算链偏好性，首先，一次只使用来自一条链的reads数目来评估突变的证据，并应用计算突变Q-score的相同泊松概率分布模型（使用来自两个链的合并数据）。该数据基于似然比来计算检验统计量。使用以下公式计算1向链的方向偏差：
```math
Direction1Bias = (Direction1.ChanceVarFreqGreaterThanZero * Direction2.ChanceFalsePos) / OverallStats.ChanceVarFreqGreaterThanZero
```

## Ref_Info
https://dotnet.microsoft.com/download/thank-you/dotnet-sdk-3.0.100-linux-x64-binaries

https://github.com/Illumina/Pisces

https://github.com/Illumina/Pisces/wiki

Pisces publication: Pisces: An Accurate and Versatile Variant Caller for Somatic and Germline Next-Generation Sequencing Data