### 20191213
shell 获取反向互补序列
```
[zhangbo@mu01 heyueqing_PW59-1]$ echo 'GTACATGTCA' |rev|tr "ATCG" "TAGC"
```

### 20191206
F1-Measure:
$$F_{A} = $$

其中是参数，P是精确率(Precision)，R是召回率(Recall)。 [1] 
当参数=1时，就是最常见的F1-Measure了：
F1=2*RP/(R+P)


```
http://genome.ucsc.edu/cgi-bin/das/hg38/dna?segment=chr1:186318317,186318337

[zhangbo@mu01 fusioncatcher]$ $bedtools getfasta -s -fi $hsa_ref -bed tmp.bedplus  -fo tmp.faplus
[zhangbo@mu01 fusioncatcher]$ cat tmp.faplus
>chr1:186318317-186318337(+)
ACTCTGTCTCAAAAAAAAAA
```


### 20191201
Targeted method:
In the targeted fusion detection method, panel primers are designed to target specific exon-exon junctions of fusions where the driver gene, the partner gene, and the breakpoint between the driver and the partner gene are known. The sequencing reads are mapped to a reference file that contains only the known gene fusions.

Non‑targeted method:
In the non-targeted fusion detection method, the panel primers are used to detect fusions between novel combinations of known diver and partner genes. The sequencing reads are mapped to a broader reference, such as the whole‑exome. Mapping the reads to a broader reference allows for the detection of multiple configurations of driver and partner genes as well as detection of novel breakpoints between the known partner and driver genes.

https://ionreporter.thermofisher.com/ionreporter/help/GUID-F6403251-CB0F-4955-9C2F-637E7C535387.html
https://ionreporter.thermofisher.com/ionreporter/help/GUID-759AEE0F-3542-4774-AC5E-E630E2A4B1FB.html


高级分析 http://www.feiaituan.com/?p=1289

### 20191128
## cosmic fusion 数据库介绍
官网地址： 
+   https://cancer.sanger.ac.uk/cosmic/fusion
内容描述：
+ https://cancer.sanger.ac.uk/cosmic/help/fusion/summary#summary_tab_content
+ https://cancer.sanger.ac.uk/cosmic/help#types_tab_content       
        

```
Gene name 1 {Accession number 1 }: r. _ Gene name 2{ Accession number 2 } : r.
Gene name： 基因的HUGO symbol
Accession number：Genebank对应的转录本编号
:：冒号用于区分基因ID和位置信息
r.：基于RNA水平的位置描述
_：区分前后两个断点
```

### 20191122
鉴别基因组版本
```
Identifying reference genome, if not stated, isn't always straightforward. In DanteLabs's case, though, looking at the BAM file headers we have:
@SQ SN:chrM LN:16571

The 16571 basepair chrM is NC_001807 which UCSC used, but is used by practically nobody nowadays (It's not the same as rCRS or RSRS). Since in mapping reads will align to a location they best match with, changing the reference, even something as simple as the mitochondrial DNA, can cause reads to map differently elsewhere, and labs like to keep things unchanged so the results remain comparable. This one probably should be changed though, but YFull interpreted full mtDNA from the sequence, which is probably easiest option.

```

---

STAR-Fusion --genome_lib_dir $starfusion_lib \
            --samples_file $sampleId.samples \
            --output_dir ./STAR-Fusion/ \
            --FusionInspector validate \
            --denovo_reconstruct \
            --examine_coding_effect \
            --CPU $ncpus \
            --min_FFPM 1



https://github.com/ShixiangWang/DoAbsolute

dna rna 整合 https://github.com/griffithlab/regtools


线粒体 : https://link.springer.com/article/10.1007%2Fs00414-019-02205-y

## bgi数据处理
本地集群上：
/lustre/Work/project/3d_genome/BGI_data_segmentation/20191109




https://github.com/zpeng1989/Cancer_Study

https://bioconductor.org/packages/devel/workflows/vignettes/SingscoreAMLMutations/inst/doc/workflow_transcriptional_mut_sig_chinese.html

## 肿瘤异质性
1.肿瘤间异质性(intertumour heterogeneity)

2.肿瘤内异质性(intratumour heterogeneity)

3.细胞间异质性(intercellular heterogeneity)

##

https://github.com/bio-ontology-research-group

https://github.com/ChristofferFlensburg/superFreq


aneuploidy 非整倍行
https://github.com/SilenWang/fastaAnno

## 2019113
ExtractUmisFromBam 
    --molecular-index-tags ZA ZB
align reads
    ORIENTATIONS=FR
GroupReadsByUmi
    -s Paired

 CallDuplexConsensusReads CallMolecularConsensusReads

https://stldevs.com/developers/zlskidmore   


9. Extracting UMIs within sample index reads during demultiplexing 9

https://www.sciencedirect.com/science/article/pii/S2001037017300946



