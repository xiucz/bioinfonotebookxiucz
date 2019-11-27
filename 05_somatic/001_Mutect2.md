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

