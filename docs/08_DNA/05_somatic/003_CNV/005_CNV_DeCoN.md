[TOC]
# exomeCopy
http://www.bioconductor.org/packages/2.9/bioc/html/exomeCopy.html


## Installation

https://bmcgenomics.biomedcentral.com/articles/10.1186/1471-2164-15-661

```
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")
BiocManager::install("exomeCopy", version = "3.8")
```
background:

```
> exome.samples <- grep("HG.+", colnames(mcols(example.counts)),
+     value = TRUE)
> example.counts$bg <- generateBackground(exome.samples,
+     example.counts, median)
> example.counts$log.bg <- log(example.counts$bg + 0.1)
> example.counts$bg.var <- generateBackground(exome.samples,
+     example.counts, var)
```

# ExomeDepth
https://github.com/vplagnol/ExomeDepth

- it in fact performs best on smaller panels
- ExomeDepth is primarily designed for inherited changes. 
- 
https://cran.r-project.org/web/packages/ExomeDepth/vignettes/ExomeDepth-vignette.pdf

---
# DECoN
https://wellcomeopenresearch.org/articles/1-20/v1
```
Rscript ReadInBams.R --bams bams.file --bed bed.file   --fasta fasta.file --out output.prefix

Rscript IdentifyFailures.R --Rdata summary.file --mincorr .98   --mincov 100  --exons customNumbers.file --custom FALSE --out output.prefix

Rscript makeCNVcalls.R --Rdata summary.file --transProb transition.probability   --exons customNumbers.file --custom FALSE --out output.prefix –-plot All  --plotFolder DECoNPlots

```

## INPUT
bed file:
+  the targeted 1-based BED file to be used for analysis
+  

---

# ExCopyDepth
https://static-content.springer.com/esm/art%3A10.1186%2F1471-2164-15-661/MediaObjects/12864_2014_6348_MOESM1_ESM.pdf
