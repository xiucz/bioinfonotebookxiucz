

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



