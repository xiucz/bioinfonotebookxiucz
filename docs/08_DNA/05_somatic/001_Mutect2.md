## 方法

### 定义活跃区域
Mutect2 triages sites for possible somatic variation and determines intervals over which to assemble reads by assigning each site’s read pileup a log odds for somatic activity via a simplified version of the somatic genotyping model, below. A site is considered “active” if its log odds exceed some threshold. Like HaplotypeCaller, Mutect2 chooses for assembly intervals that surround each active site with some margin. The details of this are discussed in the supplemental material.
### Assembling Haplotypes
### Somatic Genotyping

### 过滤
FilterMutectCalls首先估计每个候选突变是体细胞变异的可能性，而不是胚系变异或测序错误等。然后，它选择使估计的F分数最大化的概率阈值（recall和precision的 harmonic mean），并进行相应过滤。错误概率是从几种不同的错误模型中得出的，其中最简单的是硬过滤器，当注释超过某个阈值时，它们会将错误概率分配为1。
+ Hardfilter:
Error probabilities are derived from several different error models, the simplest of which are hard filters that assign an error probability of 1 when an annotation exceeds some threshold. These include filters for variants with an excess of low-quality supporting bases, poor mapping quality, support coming exclusively near the end of reads, a large mismatch in lengths between variant- and reference-supporting fragments, and local haplotypes with too many variants.

+ PON:
+ probabilistic models:
  + germline model
  + The normal artifact model
  + The contamination model
  + short tandem repeat (STR) model
  + strand bias and orientation bias artifacts

## Results
### DREAM Challenge
### Normal-Normal Calls
A popular way to measure the false positive rate of somatic variant callers is to assign one normal sample as a “tumor” and a technical replicate of the same sample as the “matched normal.”
### Normal mixtures

### workflow
```
#GATK 3
java -jar GenomeAnalysisTK.jar \
-T MuTect2 \
-R <reference> \
-L <region> \
-I:tumor <tumor.bam> \
-I:normal <normal.bam> \
--normal_panel <pon.vcf> \                        
--cosmic <cosmic.vcf> \
--dbsnp <dbsnp.vcf> \
--contamination_fraction_to_filter 0.02 \                   
-o <mutect_variants.vcf> \
--output_mode EMIT_VARIANTS_ONLY \
--disable_auto_index_creation_and_locking_when_reading_rods
```
### 参数理解
GATK Mutect2 官网文档中有关于--cosmic和--dbsnp两个参数的描述：

 >“MuTect2 has the ability to use COSMIC data in conjunction with dbSNP to adjust the threshold for evidence of a variant in the normal. If a variant is present in dbSNP, but not in COSMIC, then more evidence is required from the normal sample to prove the variant is not present in germline.”

```
```
### 参数理解
`--disable-read-filter MateOnSameContigOrNoMappedMateReadFilter`, 禁用此过滤器，目的是为了将配对的read map到不同染色体上的那些reads也纳入考虑，从而使得可供分析的read更多。


### strand bias


FS is Phred-scaled p-value using Fisher’s Exact Test to detect strand bias (the variation being seen on only the forward or only the reverse strand) in the reads. More bias is indicative of false positive calls.


One nice explanation could be found in this samtools mailing list post

If you have only reads in one strand you can not "have" strand bias, or at least you can not measure it, since the other strand has 0 reads.

The strand bias measure if the proportion of alleles is statistically different between strands. As you don't have reads in the other strand, you can not measure it. You can only measure allele bias (departure of the 0.5 if you are expecting germline heterozygotes)

You can also read here that GATK keeps SNPs with strand bias p-value < 60 for filtering SNP (equivalent to p-value > 0.000001). But they also mention here that A higher score indicates a higher probability that a particular  decision is correct, while conversely, a lower score indicates a  higher probability that the decision is incorrect.
----https://www.biostars.org/p/244911/

```
chr10   3875262 .       A       G       32      .       DP=16;AF1=0.5001;CI95=0.5,0.5;DP4=5,0,9,0;MQ=20;FQ=9.52;PV4=1,1,1,0.097 GT:PL:GQ        0/1:62,0,36:39
```

```
sb <-
matrix(c(3, 1, 1, 3),
       nrow = 2,
       dimnames = list(c("ref_forward", "ref_reverse"),
                       c("alt_forward", "alt_reverse")))
fisher.test(sb, alternative = "greater")

#ref_forward = number of forward reads supporting the reference
#ref_reverse = number of reverse reads supporting the reference
#alt_forward = number of forward reads supporting the variant
#alt_reverse = number of reverse reads supporting the variant
```
https://sourceforge.net/p/samtools/mailman/samtools-help/thread/4E177430.7030802@bcgsc.ca/

## source-code
gatk3:https://github.com/broadgsa/gatk-protected/blob/master/protected/gatk-tools-protected/src/main/java/org/broadinstitute/gatk/tools/walkers/cancer/m2/MuTect2.java#L806

gatk3: https://github.com/broadinstitute/mutect/blob/69b7a37fcafe8a68f7470436f24e44df740f1c41/src/org/broadinstitute/cga/tools/gatk/walkers/cancer/mutect/MuTectArgumentCollection.java

gatk4:https://github.com/broadinstitute/gatk/blob/master/src/main/java/org/broadinstitute/hellbender/tools/walkers/mutect/filtering/M2FiltersArgumentCollection.java

