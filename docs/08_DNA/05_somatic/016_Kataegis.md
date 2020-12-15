Kataegis 在希腊语中为“暴风雨”的意思。Kataegis用以描述基因组一个小范围的区域内，短时间内发生大量的基因突变模式（“microclusters”），以区别于传统的“循序渐进”的突变模式。

Kataegis具有以下的基因组特征：

+ (1)    主要发生在TpCpX 区域，倾向于C->T/C->G突变。
+ (2)    在同一个Kataegis事件中，突变类型基本上比较一致。
+ (3)    通常这些突变是发生在同一条染色体上。
+ (4)    在一个区域有多个Kataegis事件发生。
+ (5)    由于端粒危机，通常与SV（染色体碎裂）相关。

根据Kataegis的基因组特征，研究人员认为其与AID/APOBEC脱氨酶相关，在酵母中确实验证了这一关系，AID/APOBEC可以导致Kataegis事件的发生，这种脱氨机制可以诱导SSDNA/RNA在DNA复制的过程中使胞嘧啶脱去氨基变成尿嘧啶，让DNA降解或者发生“kataegis”突变(图2，ref2)。AID/APOBEC脱氨机制是导致kategesis事件的主要来源。

根据突变特征解析，目前认为Kataegis事件与Signatures 2 /13是正相关的，其中 Signature 2 is composed predominantly of C>T transitions with fewer C>G transversions at a TpCpX sequence context. In contrast, Signature13 is dominated by C>G transversions at a TpCpX context（图3）。同时双链断裂诱导的修复复制（BIR）也是另一个强有力的“kataegis”的来源（ref5）。

## 
研究组在21个乳腺癌基因组中发现了将近20万个突变，通过分析每个癌症中的突变，研究人员区分出了不同形式的突变，这些突变似乎代表了这个病人一生中，个体细胞基因组中过去出现的突变机制的痕迹。这些痕迹就像是留在癌细胞DNA中的考古学印记，癌细胞中的突变过程在癌症引起注意之前很多年，就已经被激活了。

研究人员创建了这21个癌症基因组中所有突变的目录，并分析了导致乳腺癌的突变途径。他们发现这些突变多年来逐渐积累在乳腺癌中，刚开始还很慢，随着遗传损伤出现，这些突变就开始发起猛烈攻击。到乳腺癌发展增大，能被检测出来后，这些肿瘤就会由一些遗传相关家族突变细胞组成，这一家族就会占据癌症主体。

其中一个新突变机制就是在小区域基因组中出现大量突变的异乎寻常的过程，研究人员将其称为“kataegis”（希腊语中“雷雨”的意思）——虽然之前从未描述过，但Kataegis在乳腺癌中十分常见，在21个乳腺癌基因组中，13个乳腺癌就出现了kataegis。这种密集出现的突变可能出现在某个时间点，而不是随着癌症进展，分步积累出现的。

“它们具有不同的突变位点，并且常常与大型重排同时出现——这是一种独一无二的突变模式。”

“目前癌症治疗并不考虑亚克隆多样性，常常只靶向主导亚克隆”，Stratton教授解释道，“这就有可能其它小的亚克隆重新复制，变成主导克隆，从而引起癌症复发。”

Kataegis is a localized hypermutation occurring when a region is enriched in somatic SNVs (Nik-Zainal S. etal 2012 ).

Kataegis can result from multiple cytosine deaminations catalyzed by the AID/APOBEC family of proteins (Lada AG et al 2012 ).


为了能了解乳腺癌背后的奥妙，研究组成员将21个乳腺癌基因组中出现的突变排列了出来。其中他们发现了一个途径出现了大量基因组突变，并且这种突然出现的“倾盆大雨”突变在乳腺癌中很常见。研究人员将这种现象——之前从未发现过，称为kataegis（希腊语中“雷雨”的意思）。

https://mp.weixin.qq.com/s/_nTV5lbtRBEABW8DJg-YiA

## SeqKat
Kataegis is a localized hypermutation occurring when a region is enriched in somatic SNVs. Kataegis can result from multiple cytosine deaminations catalyzed by the AID/APOBEC family of proteins. This package contains functions to detect kataegis from SNVs in BED format. This package reports two scores per kataegic event, a hypermutation score and an APOBEC mediated kataegic score. Yousif, F. et al.; The Origins and Consequences of Localized and Global Somatic Hypermutation; Biorxiv 2018 .


```
.fa文件，碱基全部大写，只能包含[ATCGN],且去掉">chr*"。

```

## Mutalisk
Mutalisk consists of four major functional modules: (i) localized hypermutation analysis, (ii) mutational signature decomposition, (iii) transcriptional strand bias analysis and (iv) epigenome association analysis. 


## KataegisPortal

https://github.com/MeichunCai/KataegisPortal

```
> library(devtools)
> options(devtools.install.args = c("--no-multiarch", "--no-test-load"))
> devtools::install_url('https://github.com/MeichunCai/KataegisPortal/files/5060224/KataegisPortal_1.0.3.tar.gz', INSTALL_opts = c("--no-multiarch", "--no-test-load"))
```