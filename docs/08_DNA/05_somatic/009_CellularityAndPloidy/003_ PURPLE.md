## Algorithm
### 1. Sex determination
Klinefelter syndrome: a chromosomal disorder resulting in 2 or more X chromosome in a male and which we have found to affect 0.2% of the male samples in our cohort.

+ 通过杂合位点数来判断

we examine the number of heterozygous loci in our provided BED file of 1.3 million common germline SNPs on the X chromosome, outside the pseudoautosomal region. Anything less than 1k heterozygous loci is considered male. A typical female has 12-13k heterozygous loci.

+ 通过拷贝数来判断
we first use the reference ratio to determine the number of copies of the X chromosome. A median X ratio greater than 0.65 is interpreted as 2 copies (note that nearly all female samples are very close to a ratio of 1, but a handful are significantly lower with mosaic X loss). If there is only one copy of the X chromosome the sample is male. Otherwise, we check for the presence of the Y chromosome as determined by at least 1000 data points with a median ratio > 0.05. If the Y chromosome is present (in addition to the 2 copies of the X chromosome), then the sample is male with Klinefelter syndrome. In the absence of the Y chromosome the sample is female.

### 2. Segmentation
using the Bioconductor copynumber package which uses a piecewise constant fit (PCF) algorithm (with custom settings: gamma = 100, k =1). 

### 3. Sample Purity and Ploidy
