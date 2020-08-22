https://bioconductor.org/packages/release/bioc/vignettes/timescape/inst/doc/timescape_vignette.html

https://bitbucket.org/aroth85/pyclone/wiki/browse/

http://suprahex.r-forge.r-project.org/demo-PyClone.html
 
 the cellular prevlance of the mutation.

 the proportion of cancer cells which contain a mutation as the cellular frequency of the mutation. 

## Input
Positions in which only one of the cases has a variant genotype (AB or BB) are included in this dataset. Conceptually this is equivalent to a sample with four populations of diploid clones, which share no mutations. Because we excluded sex chromosomes, the total copy number of all positions is 2. For this dataset we still need to get the parental copy number for mutation. This can be done since we have the predicted genotype of the variants (AB or BB). In the case the mutation is AB the major and minor copy numbers would both be 1. In the case the genotype was BB the major copy number would be 2 and the minor copy number would be one.

 maternal or paternal