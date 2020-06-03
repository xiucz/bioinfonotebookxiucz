# A Comprehensive Workflow for Read Depth-Based Identification of Copy-Number Variation from Whole-Genome Sequence Data

## OverView
![xx](https://github.com/xiucz/pics/blob/master/20191111.jpg?raw=true)

**In stage 1 (“algorithm selection”)**
three WGS datasets and corresponding CNV benchmarks (HuRef,3, 4, 28 NA12878,34 and AK135) were used to assess the accuracy of six read depth-based CNV-detection algorithms—Canvas, cn.MOPS, CNVnator, ERDS, Genome STRiP, and RDXplorer. 
CNV算法检测的一致性
**In stage 2 (“workflow development”)**
other factors influencing CNV detection were evaluated in the context of the most accurate algorithms identified in stage 1. Based on results from the first two stages, we propose a comprehensive workflow for detecting CNVs from short-read WGS data. 

**In stage 3 (“workflow evaluation”)** 
we show that our workflow can accurately identify clinically relevant CNVs. Green parallelograms represent data, and gray rectangles represent actions. The blue shape represents the CNV detection workflow developed from the results of the first two stages.



While standard CGH microarrays typically detect CNVs ≥20 kb, the NimbleGen 42M and Agilent 24M have more probes and thus can identify CNVs as small as 500 bp.

Only CNVs ≥1 kb were retained, because three of the six CNV-detection algorithms tested did not report smaller CNVs (see below) and because smaller CNVs are more readily detected by strategies not involving read depth. 

Overlapping CNVs from the same technology were manually merged. 

CNVs based on mate-pair analysis of Sanger data were removed, as the breakpoints and CNV sizes could not be precisely determined.

BWA-MEM v.0.7.15 was used for GRCh38/hg38 because it fixed an error related to ALT-aware mapping that was present in v.0.7.12; the output from the two versions is identical in the absence of ALT contigs 



## Definition of Repetitive and Low-Complexity Regions of the Human Genome
Both array- and sequencing-based methods of CNV detection can be confounded by repetitive and low-complexity regions (RLCRs).24 To define a comprehensive set of RLCRs, we combined four datasets: (1) the set of assembly gaps defined by UCSC, which includes centromeres, telomeres, constitutive heterochromatin domains, gaps between or within clones and contigs, and the repeat-dominated short arms of chromosomes 13, 14, 15, 21, and 22; (2) the UCSC list of segmental duplications; (3) the pseudoautosomal regions of the sex chromosomes; and (4) repeat regions as defined by RepeatMasker version open-3-2-7 (see Web Resources) using the –s (sensitive) option with release 20090120 of library RepeatMaskerLib.embl. The number of regions present in each data source, as well as the total number of base pairs, is given in Table S1. Links to download the full RLCR definition, as well as a version that omits the RepeatMasker definitions, are provided in Web Resources.

## Filtering out CNVs with Substantial Repeat Content
For generating filtered sets of CNVs, a given CNV was removed if ≥70% of the CNV overlapped with RLCRs. This overlap could be comprised of just a single continuous region or multiple regions. To illustrate, if a CNV spans coordinates 100,000–105,000 of chromosome 1 and two RLCRs span coordinates 100,000–102,000 and 103,000–105,000 of chromosome 1, then 80% of the CNV overlaps with RLCRs.

## CNV-Detection Algorithms
+ The CNV-detection algorithms tested were Canvas v.1.3.5,
+ Copy Number estimation by a 
  + Mixture Of PoissonS (cn.MOPS) v.1.16.2
  + CNVnator v.0.3.2
  + ERDS v.1.1,20 Genome STRucture in Populations (Genome STRiP) v.2.00.1696
  + Read Depth eXplorer (RDXplorer) v.2.0 release 3.

For cn.MOPS, the authors recommend ≥6 genomes,38 while the authors of Genome STRiP recommend ≥20

## Concordance of CNV-Detection Algorithms
To determine sets of equivalent CNVs, CNVs were iteratively added to sets as follows. For each possible pair of CNVs (where each CNV in a pair was identified by a different algorithm or HuRef benchmark method), we determined whether they satisfied the 50% reciprocal overlap criterion. If so, and if neither CNV was yet in a set, then a new set was created including both CNVs. If only one of the CNVs was already in a set, then the other CNV was added to it. If both CNVs were already in different sets (a rare “conflict”), then they were left as such. When determining how many unique benchmark CNVs fell into each size bin, the size of a particular unique benchmark CNV was calculated as the median of the sizes of all the CNVs in the corresponding set. The overall similarity of two sets of CNVs A and B, where A and B were detected by two different methods, was measured using the Jaccard index J = (A ∩ B/A ∪ B). As not all algorithms reported absolute copy number, it was not taken into account when comparing CNVs.

## Accuracy of CNV-Detection Algorithms
The accuracies of the CNV-detection algorithms were evaluated according to their sensitivity and FDR when compared with a benchmark of known CNVs (HuRef, NA12878, or AK1 benchmarks). A CNV detected by an algorithm and a benchmark CNV were deemed to be the same if the first CNV overlapped with at least 50% of the second CNV and vice versa (50% reciprocal overlap).42 Sensitivity was defined as TP / (TP + FN), where TP (“true positives”) is the number of benchmark CNVs identified correctly and FN (“false negatives”) is the number of benchmark CNVs not detected by the algorithm. FDR was defined as FP / (FP + TP), where FP (“false positives”) is the number of identified CNVs not found in the benchmark. For the HuRef benchmark, which contains CNVs identified by multiple technologies and strategies, sensitivity was determined for CNVs having 50% reciprocal overlap with a benchmark CNV from at least one technology (denoted “1+”) or at least two technologies (“2+”).

## Read-Depth Uniformity
Read-depth uniformity was measured by the inter-quartile range (IQR). Let Ra denote the read depth for which a% of the bases in the reference genome have smaller depths. Then IQR = R75 − R25. The GitHub repository for this paper (Web Resources) provides scripts for calculating IQR from BAM files.
