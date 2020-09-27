## 
宏转录组测序是对某一特定时期、特定环境样品中的全部微生物的RNA进行高通量测序，直接获得该环境中所有微生物转录组信息的一种测序技术的新应用。宏转录组中不仅包含有微生物的物种信息，还有微生物的基因表达信息。如果说宏基因组能告诉我们微生物群，那么宏转录组则能告诉我们这些微生物，这有助于挖掘微生物功能基因和探索微生物与环境、疾病、动植物等关系的机制。

##
1. Remove adapter sequences, which are added during library preparation and sequencing steps, and trim low quality bases and sequencing reads.
2. Remove duplicate reads to reduce processing time for following steps.
3. Remove vector contamination (reads derived from cloning vectors, spike-ins, and primers).
4. Remove host reads (if exploring a microbiome in which the host is an issue).
5. Remove abundant rRNA sequences which typically dominate metatranscriptomic datasets despite the use of rRNA removal kits.
6. Add duplicated reads, removed in step 2, back to the data set to improve quality of assemblies.
7. Classify reads to known taxonomic groups and visualize the taxonomic composition of your dataset.
8. Assemble the reads into contigs to improve annotation quality.
9. Annotate reads to known genes.
10. Map identified genes to the swiss-prot database to identify enzyme function
11. Generate normalized expression values associated with each gene.
12. Visualize the results using KEGG metabolic pathways as scaffolds in Cytoscape.


We are analyzing our RNA reads as we would do for DNA reads. This approach has one main caveat. In MetaPhlAn2, the species are quantified based on the recruitment of reads to species-specific marker genes. In metagenomic data, each genome copy is assumed to donate ~1 copy of each marker. But the same assumption cannot be made for RNA data: markers may be transcribed more or less within a given species in this sample compared to the average transcription rate. A species will still be detected in the metatranscriptomic data as long as a non-trivial fraction of the species’ markers is expressed.

We should then carefully interpret the species relative abundance. These values reflect species’ relative contributions to the pool of species-specific transcripts and not the overall transcript pool.


```
质控低质量，接头 --> trimmomatic
去宿主 --> bowtie2/bwa ???
rRNA --> BLAT/Inferal
组装拼接 --> trinity  嵌合体
功能注释 --> DIAMOND, BLAST
Gist 物种注释
```

![](./pics/20200916.jpg)

首先要对原始数据进行预处理，得到有效数据（Clean Data）。然后基于有效数据进行物种分类分析和复杂度分析
以及基因的表达丰度分析；再进行拼接与组装，进行代谢通路（KEGG），同源基因簇（eggNOG），碳水化合物酶（CAZy）
等功能注释，全面了解样品中的微生物组成结构和功能注释信息。最后，基于以上分析结果，可以进行多样品比较分析，
如聚类分析，PCoA 分析等，挖掘出样品之间的物种和功能差异。

![](./pics/202009161.jpg)

使用 DIAMOND 软件将拼接后的 Unigenes 与从 NCBI 的 NR 数据库中抽提出的细菌（Bacteria）、真菌（Fungi）、
古菌（Archaea）和病毒（Viruses）序列进行比对，选取出在各样品中的最大相对丰度排名前 10 的物种，通过物种丰度
统计明确主要占比的微生物种类。

为获得全面的基因功能信息，进行了四大数据库的基因功能注释，包括：GO、KEGG、CAZy 和 eggNOG。统计不同
功能层级的相对丰度，确定微生物在特定环境中发挥的主要功能。

### 挑战
缺少polyA信号，很难纯化细菌mRNA，导致大量rRNA污染

[1] Shi M, Lin X, Tian J, et al. Redefining the invertebrate RNA virosphere[J]. Nature, 2016, 540(7634): 539-543.
[2] Li C, Shi M, Tian J, et al. Unprecedented genomic diversity of RNA viruses in arthropods reveals the ancestry of
negative-sense RNA viruses[J]. eLife, 2015.
[3] Shi M, Neville P J, Nicholson J, et al. High-Resolution Metatranscriptomics Reveals the Ecological Dynamics of
Mosquito-Associated RNA Viruses in Western Australia[J]. Journal of Virology, 2017, 91(17).

https://blog.csdn.net/woodcorpse/article/details/82053830