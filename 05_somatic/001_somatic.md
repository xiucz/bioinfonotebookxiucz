---
marp: true
size: 4:3
theme: default
---

MuTect

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

# gatk4

---

### bad_haplotype
Mutect2 emits phasing information for calls in the same assembly region. We assign a “bad haplotype” probability equal to the greatest technical artifact probability of any in-phas call within a certain distance, by default 100 bases.
---