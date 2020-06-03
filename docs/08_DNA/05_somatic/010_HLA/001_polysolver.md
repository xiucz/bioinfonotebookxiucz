https://github.com/jason-weirather/hla-polysolver
https://software.broadinstitute.org/cancer/cga/polysolver

POLYSOLVER consists of the following steps:

(i) Improved retrieval and alignment of HLA reads Reads in the WES data that potentially originate from the HLA region are retrieved based on matches to a tag library derived from all known HLA alleles. These reads are then aligned to a full-length genomic library of all known HLA alleles using a precise alignment method, keeping all best-scoring alignments for use in subsequent steps.

(ii) Inference of the HLA alleles using a two-step Bayesian classification approach Inference of the two alleles for each HLA gene is based on a Bayesian calculation that takes into account the base qualities of aligned reads, observed insert sizes, as well as the ethnicity-dependent prior probabilities of each allele.


POLYSOLVER-based mutation detection consists of the following steps:

(i) Inference of HLA type by applying POLYSOLVER on the normal sample
(ii) Re-alignment of the HLA reads in both tumor and normal samples to the inferred HLA-alleles while filtering out likely erroneous alignments
(iii) Application of standard tools to detect somatic mutations (MuTect and Strelka) by comparing the re-aligned tumor and normal HLA reads
(iv). Annotation of the detected mutations with gene compartment and amino acid change information