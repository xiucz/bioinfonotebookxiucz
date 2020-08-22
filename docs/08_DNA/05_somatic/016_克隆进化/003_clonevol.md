Therefore, the clonal marker variants of a clone will show similar cellular prevalence across samples and will likely be clustered together.

The assumption for the clustering algorithm to work is that V AF provides a good estimate of the cellular fraction of a variant, ie. the fraction of cells carrying the variant. In the case with diploid heterozygous variants (copy number neutral), the cancer cell fraction (CCF) of a variant can be calculated as twice of its VAF. 

When evaluating the clusters, look for 
+ potential outlier clusters (eg. ones with small number of variants), 
+ potential merged clusters (eg. ones that have variants with V AF stretching from zero to non-zero values in multiple samples, that can be further splitted into multiple clusters),
+ noise clusters (eg. ones that show very similar and low V AF across samples, indicating false variant calls)

or calculate the equivalent copy number corrected VAF as half of the CCF.