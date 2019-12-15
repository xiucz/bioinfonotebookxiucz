---
marp: true
size: 4:3
theme: default
---

# MuTect

---

Overview
In a nutshell, the MuTect analysis consists of three steps:
1. Pre-processing the aligned reads in the tumor and normal sequencing data
2. Statistical analysis to identify sites that are likely to carry somatic mutations with high confidence
3. Post-processing of candidate somatic mutations

---

![xx](https://github.com/xiucz/pics/blob/master/20191206.jng?raw=true)




https://www.nature.com/articles/nbt.2514

---

## Preprocessing the aligned reads
In this step we ignore reads with too many mismatches or very low quality scores since these represent noisy reads that introduce more noise than signal.

---

## Statistical analysis

Overview
In a nutshell, the MuTect analysis consists of three steps:
1	Pre-processing the aligned reads in the tumor and normal sequencing data
2	Statistical analysis to identify sites that are likely to carry somatic mutations with high confidence
3     Post-processing of candidate somatic mutations

---

统计分析通过使用两个贝叶斯分类器来预测体细胞突变：
	第一个用于检测肿瘤样本在给定位点是非参考位点，对于那些被发现为非参考的位点，
	第二个分类器确保正常样本不携带变异等位基因。在实践中，通过计算LOD得分（log odds），然后与阈值进行比较来执行分类，阈值是由所考虑事件的先验概率的对数比来决定的。
![xx](https://github.com/xiucz/pics/blob/master/201912061.jng?raw=true)

---

在体细胞突变以每Mb约1的速度发生，这保证了由于肿瘤中的噪声导致的假阳性率低于体细胞突变率的一半。

在正常细胞中，如果位点不在dbSNP中，因为非dbSNP种系变异大致以100/Mb的速率发生。这种阈值值保证了由于缺少正常变异而导致的假阳性，也低于体细胞突变率的一半。
$$LOD_{T} > \log_{10}(0.5 \times 10^{-6}) \approx 6.3$$

$$LOD_{N} > \log_{10}(0.5 \times 10^{-2}) \approx 2.3$$

---

![xx](https://github.com/xiucz/pics/blob/master/201912062.jng?raw=true)

---

![xx](https://github.com/xiucz/pics/blob/master/201912063.jng?raw=true)

---

alt_allele_in_normal: the alternate allele was also found in the normal so this is probably either a shared artifact or a germline event

---

**clustered_events:**

several mutations that are close together (parameters are in the tool doc), which is often the sign of being an artifact

It filters variants with 2 mutation-events with the distance between each other larger than 2bp(start position), not the mutations close to each other.

ECNT is the number of potential (pre-filtering) somatic mutations (events) exhibited in an assembled haplotype.https://gatkforums.broadinstitute.org/gatk/discussion/23249/ecnt-value-in-mutect2

`FilterMutectCalls with --maxEventsInHaplotype`

---

**t_lod_fstar:**

log-odds of an event in the tumor, which is measure of confidence of the mutation being real




---

str_contraction: related to short tandem repeats

---

Filters used in high-confidence mode

1. Proximal Gap

 MuTect will reject a candidate site if there are more than a given number of reads with insertions/deletions in an 11 base pair window centered on the candidate. The threshold value is controlled by the --gap_events_threshold.

In the CALLSTATS output file, the relevant columns are labeled t_ins_count and t_del_count.

---

Filters used in high-confidence mode

2. Poor Mapping

This filter removes FPs caused by reads that are poorly mapped (typically due to sequence similarities between different portions of the genome). The filter uses two tests:

Reject candidate if it does not meet a given threshold for the fraction of reads that have a mapping quality of 0 in tumor and normal samples. The threshold value is controlled by --fraction_mapq_threshold.

Reject candidate if it does not have at least one observation of the mutant allele with a mapping quality that satisfies a given threshold. The threshold value is controlled by `--required_maximum_alt_allele_mapping_quality_score`.

In the CALLSTATS output file, the relevant columns are labeled total_reads and map_Q0_reads for the first test, and t_alt_max_mapq for the second test.

---

Filters used in high-confidence mode

3. Strand Bias

This filter rejects FPs caused by context-specific sequencing where the vast majority of alternate alleles are seen in a single direction of reads. Candidates are rejected if strand-specific LOD is below a given threshold in a direction where the sensitivity to have passed that threshold is above a certain percentage. The LOD threshold value is controlled by --strand_artifact_lod and the percentage is controlled by --strand_artifact_power_threshold.

In the CALLSTATS output file, the relevant columns are labeled `power_to_detect_negative_strand_artifact` and `t_lod_fstar_forward`. There are also complementary columns labeled `power_to_detect_positive_strand_artifact` and `t_lod_fstar_reverse`.

---

Filters used in high-confidence mode

4. Clustered Position

This filter rejects FPs caused by misalignments evidenced by the alternate alleles being clustered at a consistent distance from the start or end of the read alignment. Candidates are rejected if their median distance from the start/end of the read and median absolute deviation are lower or equal to given thresholds. The position from end of read threshold value is controlled by --pir_median_threshold and the deviation value is controlled by --pir_mad_threshold.

In the CALLSTATS output file, the relevant columns are labeled tumor_alt_fpir_median and tumor_alt_fpir_mad for the forward strand, and complementary columns are labeled tumor_alt_rpir_median and tumor_alt_rpir_mad for the reverse (note the name difference is fpir vs. rpir, for forward vs. reverse position in read).

---
Filters used in high-confidence mode

5. Observed in Control

This filter rejects FPs in tumor data by looking at control data (typically from a matched normal) for evidence of the alternate allele that is above random sequencing error. Candidates are rejected if both the following conditions are met:

The number of observations of the alternate allele or the proportion of reads carrying the alternate allele is above a given threshold, controlled by --max_alt_alleles_in_normal_count and --max_alt_allele_in_normal_fraction.

The sum of quality scores is above a given threshold value, controlled by --max_alt_alleles_in_normal_qscore_sum.

In the CALLSTATS output file, the relevant columns are labeled n_alt_count, normal_f, and n_alt_sum.

---

Filters applied in all MuTect modes
1. Tumor and normal LOD scores
This filter rejects candidates with a tumor LOD score below a given threshold value, controlled by --tumor_lod, and similarly for a normal LOD score threshold controlled by --normal_lod_threshold.

In the CALLSTATS output file, the relevant columns are labeled t_lod_fstar and init_n_lod, respectively.

2. Possible contamination
This filter rejects candidates with potential cross-patient contamination, controlled by --fraction_contamination.

In the CALLSTATS output file, the relevant columns are labeled t_lod_fstar and contaminant_lod.

3. Normal LOD score and dbsnp status
If a candidate mutation is in dbsnp but is not in COSMIC, it may be a germline variant. In that case, the normal LOD threshold that the candidate must clear is raised to a value controlled by --dbsnp_normal_lod.

In the CALLSTATS output file, the relevant column is labeled init_n_lod.

4. Triallelic Site Filter

When the program is evaluating a site, it considers all possible alternate alleles as mutation candidates, and puts them through all the filters detailed above. If more than one candidate allele passes all filters, resulting in a proposed triallelic site, the site is rejected with the reason triallelic_site because it is extremely unlikely that this would really happen in a tumor sample.

---

# gatk4

---

### bad_haplotype
Mutect2 emits phasing information for calls in the same assembly region. We assign a “bad haplotype” probability equal to the greatest technical artifact probability of any in-phas call within a certain distance, by default 100 bases.
---