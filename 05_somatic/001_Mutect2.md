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
