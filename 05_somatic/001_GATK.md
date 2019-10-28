### strand bias

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