## V90 Release Changes (5 September 2019)
在这个版本中，cosmic的突变定义已更新。在基因组水平描述的变异（亲本）和一个或多个转录本（一个或多个子代）上的注释描述的变异之间引入了亲子关系。这种新关系的引入，为亲代变异引入了新的基因组突变标识符，COSV。

1. 使用Ensembl release 93数据库
限制为gencode https://asia.ensembl.org/info/genome/genebuild/transcript_quality_tags.html#basic 
2. 使用Ensembl release 93软件VEP
> There is now a more accurate representation of mutations that lie within intronic and UTR regions of genes. Previously, the majority of intronic mutations that existed more than 10 bp outside of a splice-site and those lying within a UTR were represented as non-coding variants. These variants had a COSN identifier. Such variants have now been annotated in the coding domain by VEP and have HGVS-compliant syntaxes and a genomic mutation identifier (COSV). 

> Coding mutations
Mutations that lie within the gene boundary of a protein-coding transcript from Ensembl. These are identified with a genomic identifier (COSV), a legacy mutation identifier (COSM or COSN), and an alternative mutation identifier.
Non-coding mutations
Mutations that lie within an intergenic region of the genome. These are identified with a genomic identifier (COSV), a legacy mutation identifier (COSM or COSN).
3. 新的稳定基因组突变标识符（COSV）指示了变异在基因组上的确定位置。这些唯一的标识符使变异可以在GRCh37和GRCh38之间进行映射，并显示在选定的转录本上。

 https://cancer.sanger.ac.uk/cosmic/variant-updates