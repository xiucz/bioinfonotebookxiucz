<!-- TOC -->

- [Install](#install)
- [Concept](#concept)
  - [.](#)
    - [target](#target)
      - [Bin size and resolution](#bin-size-and-resolution)
      - [Labeling target regions](#labeling-target-regions)
    - [access](#access)
    - [bed](#bed)
  - [output](#output)
    - [](#-1)
  - [cnvkit](#cnvkit)
  - [targeted amplicon sequencing](#targeted-amplicon-sequencing)
  - [In practice we see good results with an average of 200–300 reads per bin。](#in-practice-we-see-good-results-with-an-average-of-200300-reads-per-bin)
  - [http://born2run.cn/2018/05/17/cnvkit-information/](#httpborn2runcn20180517cnvkit-information)
    - [weight值在cns文件中意思](#weight值在cns文件中意思)
    - [](#-2)
    - [sex chromosome](#sex-chromosome)
  - [过滤方法](#过滤方法)

<!-- /TOC -->

Github地址：https://github.com/etal/cnvkit

官方教程地址：https://cnvkit.readthedocs.io/en/stable/index.html

cnvkit-0.9.4a0


#
# Install
github:
https://github.com/etal/cnvkit

```
conda install -c bioconda cnvkit
```

```
git clone https://github.com/etal/cnvkit.git
python setup.py build
python setup.py install --prefix=$HOME/local
```
```
ImportError: /local_data1/work/zhangbo/software/Anaconda3/lib/python3.6/site-packages/pandas/_libs/../../../../libstdc++.so.6: version `GLIBCXX_3.4.21' not found (required by /local_data1/work/zhangbo/software/Anaconda3/lib/python3.6/site-packages/pandas/_libs/window.cpython-36m-x86_64-linux-gnu.so)
```
方法一：失败了
```
[zhangbo@mu01 cnvkit]$ conda install libgcc
Solving environment: done

# All requested packages already installed.
```

基因注释文件，如果是wgs，则用refFlat.txt；如果是wes之类的则用带注释形式的bed文件

# Concept


## .



coverage—>fix—>segment—>segment


```
 cnvkit.py batch *Tumor.bam --normal *Normal.bam \

--targets my_baits.bed --annotate refFlat.txt \

 --fasta hg19.fasta --access data/access-5kb-mappable.hg19.bed \

--output-referencemy_reference.cnn --output-dir results/ \

--diagram –scatter
```
–targets: Target file corresponding to the exome probes
:target后接的是`SeqCap_EZ_Exome_v3_hg38_primary_targets.v2.bed`

```
cnvkit.py access hg19.fa -o access.hg19.bed
cnvkit.py autobin *.bam -t baits.bed -g access.hg19.bed [--annotate refFlat.txt --short-names]

# For each sample...
cnvkit.py coverage Sample.bam baits.target.bed -o Sample.targetcoverage.cnn
cnvkit.py coverage Sample.bam baits.antitarget.bed -o Sample.antitargetcoverage.cnn

# With all normal samples...
cnvkit.py reference *Normal.{,anti}targetcoverage.cnn --fasta hg19.fa -o my_reference.cnn

# For each tumor sample...
cnvkit.py fix Sample.targetcoverage.cnn Sample.antitargetcoverage.cnn my_reference.cnn -o Sample.cnr
cnvkit.py segment Sample.cnr -o Sample.cns

# Optionally, with --scatter and --diagram
cnvkit.py scatter Sample.cnr -s Sample.cns -o Sample-scatter.pdf
cnvkit.py diagram Sample.cnr -s Sample.cns -o Sample-diagram.pdf
```

单独绘图
```
/home/anaconda2/envs/cnvkit/bin/cnvkit.py scatter -s 700_bwa.sam.cn{s,r} -c chr7 -o scatter-chr7.pn
```
```
/home/anaconda2/envs/cnvkit/bin/cnvkit.py diagram 700_bwa.sam.cnr
```

```
/home/anaconda2/envs/cnvkit/bin/cnvkit.py batch /data1/data-sample/human-WGS/bwa-sam-bam/704_bwa.sam.bam -r my_flat_reference.cnn -d 704 -p 10
```

### target

```
cnvkit.py target my_baits.bed --annotate refFlat.txt --split -o my_targets.bed
```
Since these regions (usually exons) may be of unequal size, the --split option divides the larger regions so that the average bin size after dividing is close to the size specified by --average-size. If any of these three (--split, --annotate, or --short-names) flags are used, a new target BED file will be created; otherwise, the provided target BED file will be used as-is.

#### Bin size and resolution
人类基因组外显子的平均长度在200bp左右。默认的窗口大小为267bp， 以便拆分较大的外显子将产生最小值为200的窗口。

例如，将窗口的平均大小设置为100bp，将产生大约两倍的窗口，这可能会导致更高分辨率的分割。但是，每个窗口中reads的读取次数将减少大约一半，从而增加窗口覆盖度的差异或“噪音”。过多的带噪窗口可能会使可视化变得困难，并且由于噪音可能无法均匀分布，尤其是在存在许多覆盖度零的窗口的情况下，分割算法可能会在低覆盖率样本上产生不太准确的结果。实际上，我们看到了很好的结果，每个窗口内平均有200–300条reads；因此，我们建议总的靶标测序覆盖深度至少为200x到300x，读取长度为100bp，以证明将平均靶标窗口大小降低到100bp是合理的。

对于杂交捕获，如果靶不是均匀的tiled with uniform density
#### Labeling target regions
```
--short-names
--annotate
consecutive targeted regions
```

### access
The access command computes the locations of the accessible sequence regions for a given reference genome based on these masked-out sequences, treating long spans of ‘N’ characters as the inaccessible regions and outputting the coordinates of the regions between them.access命令基于这些被屏蔽的序列来计算给定参考基因组的可访问序列区域的位置，将“N”个字符的长跨度视为不可访问区域并输出它们之间的区域的坐标。

```
cnvkit.py access hg19.fa -x excludes.bed -o access-excludes.hg19.bed
```

Other known unmappable, variable, or poorly sequenced regions can be excluded with the -x/--exclude option.可以使用-x / - exclude选项排除其他已知的不可映射，可变或测序不好的区域。
+ ftp://hgdownload.soe.ucsc.edu/goldenPath/
+ ftp://hgdownload.soe.ucsc.edu/goldenPath/hg19/encodeDCC/wgEncodeMapability/
+ /local_data1/work/zhangbo/software/cnvkit/data/access-5k-mappable.hg19.bed
+ 

### bed
+ SeqCap_EZ_Exome_v3_hg19_primary_targets.bed:The ONE corresponding to the exome regions that we are interested in.(exome_regions.bed)
+ SeqCap_EZ_Exome_v3_hg19_capture_targets.bed: The ONE corresponding to the regions that probes were able to be designed on.(probe_regions.bed)
```bash
# download the chain file
wget -c http://hgdownload.cse.ucsc.edu/goldenPath/hg19/liftOver/hg19ToHg38.over.chain.gz

# run liftover
liftOver SeqCapEZ_Exome_v3.0_Design_Annotation_files/SeqCap_EZ_Exome_v3_hg19_primary_targets.bed  hg19ToHg38.over.chain.gz SeqCap_EZ_Exome_v3_hg38_primary_targets.bed unMapped.bed

# use bedtools to determine the size of the capture space represented by this bed file
cut -f 1-3 SeqCap_EZ_Exome_v3_hg38_primary_targets.bed > SeqCap_EZ_Exome_v3_hg38_primary_targets.v2.bed
# first sort the bed files and store the sorted versions
bedtools sort -i SeqCap_EZ_Exome_v3_hg38_primary_targets.v2.bed > SeqCap_EZ_Exome_v3_hg38_primary_targets.v2.sort.bed
# now merge the bed files to collapse any overlapping regions so they are not double counted.
bedtools merge -i SeqCap_EZ_Exome_v3_hg38_primary_targets.v2.sort.bed > SeqCap_EZ_Exome_v3_hg38_primary_targets.v2.sort.merge.bed
# finally use a Perl one liner to determine the size of each non-overlapping region and determine the cumulative sum
perl -ne 'chomp; @l=split("\t",$_); $size += $l[2]-$l[1]; print "$size\n"' SeqCap_EZ_Exome_v3_hg38_primary_targets.v2.sort.merge.bed
```

## output
+ **reference.cnn** is the copy number control used for normalization of test samples.
+ **.cnr** means normalized copy ratios. These are processed test samples, already normalized to the reference.
+ **.cns** means copy number segments. These are the result of segmenting .cnr files, essentially where bins with similar estimated copy number are joined together.

###  
 --method wgs 选项
 
 cnvkit
--------------------------------------------------------------------------------
hybrid capture
whole-genome sequencing
targeted amplicon sequencing
--------------------------------------------------------------------------------
**target BED** vs **bait BED**: 
CNVkit uses the bait BED file (provided by the vendor of your capture kit)
For hybrid capture, **the targeted regions (or “primary targets”)** are the genomic regions your capture kit attempts to ensure are well covered, e.g. exons of genes of interest.

**The baited regions (or “capture targets”)** are the genomic regions your kit actually captures, usually including about 50bp flanking either side of each target. Give CNVkit the bait/capture BED file, not the primary targets.Give CNVkit the bait/capture BED file, not the primary targets.
|bed1|bed2|
|---|---|
|target BED|  bait BED|
|primary targets| capture targets|
｜-｜probes.bed｜
|位点设计的时候考虑的|探针设计的时候考虑的（实际捕获到的）|
Sequencing protocol: 
+ hybridization capture ('hybrid'),
+ targeted amplicon sequencing ('amplicon'),
+ whole genome sequencing ('wgs')

In practice we see good results with an average of 200–300 reads per bin。
--------------------------------------------------------------------------------
access
Calculate the sequence-accessible coordinates in chromosomes from the given reference genome, output as a BED file.计算来自给定参考基因组的染色体中序列可访问的坐标，输出为BED文件。

Many fully sequenced genomes, including the human genome, contain large regions of DNA that are inaccessable to sequencing. (These are mainly the centromeres, telomeres, and highly repetitive regions.) In the reference genome sequence these regions are filled in with large stretches of “N” characters. These regions cannot be mapped by rese- quencing, so CNVkit avoids them when calculating the antitarget bin locations.许多完全测序的基因组，包括人类基因组，都含有大量DNA序列，这些DNA区域无法进行测序。（这些主要是着丝粒，端粒和高度重复的区域。）在参考基因组序列中，这些区域用大片“N”字符填充。 这些区域无法通过重新排序进行映射，因此CNVkit在计算antitarget bin位置时会避免它们。

http://born2run.cn/2018/05/17/cnvkit-information/
--------------------------------------------------------------------------------
https://cnvkit.readthedocs.io/en/stable/germline.html

https://cnvkit.readthedocs.io/en/stable/nonhybrid.html
The batch --method wgs option uses the given reference genome’s sequencing-accessible regions (“access” BED) as the “targets” 
No “antitarget” regions are used.
Since the input does not contain useful per-target gene labels, a gene annotation database is required and used to label genes in the outputs
cnvkit.py batch Sample1.bam Sample2.bam -n Control1.bam Control2.bam \
        -m wgs -f hg19.fasta --annotate refFlat.txt


The batch -m amplicon option uses the given targets to infer coverage, ignoring off-target regions:
This approach does not collect any copy number information between targeted regions, so it should only be used if you have in fact prepared your samples with a targeted amplicon sequencing protocol.


### weight值在cns文件中意思
weigt值并不直接和p-value有关。在老版本中表示segment覆盖的窗口的权重的平均值，而在新版或者近期版本中则是求和。

The weight value doesn't correspond directly to a p-value. It's either the sum (current & recent CNVkit versions) or mean (some earlier versions) of the weights of the bins that the segment covers. Those weights indicate the estimated standard deviation of bin log2 values in a (possibly hypothetical) pool of normal samples used as the reference -- in the reference .cnn file this is the "spread" column. Reference bins with spread > 1.0 are dropped automatically under the assumption that they're unreliable.

(If your segment weights are all below 1, you might be using an older version of CNVkit. Consider updating to take advantage of recent improvements.)

If you want to quantify the reliability of a segment, try the segmetrics command, in particular the --ci (confidence interval) or --sem (standard error) options.
https://www.biostars.org/p/266688/

###  
+ segmented log2 ratios 
+ copy-neutral
+  single-copy losses (1) and gains (3), a few multi-copy gains (4) and homozygous deletions (0
+  it merges the consecutive exons that have similar logratios.
+  logratio is LOG[(tumor copynumber)/(normal copynumber),2] 

1.
2. I recommend "export bed" for custom analysis. If you are analyzing tumor samples with some known amount of normal-cell contamination, use "call" or "rescale" first to adjust the log2 ratios for that contamination.
3. The ["export vcf"] output conforms to the VCF spec, which is a little unintuitive for describing CNVs. The CN field means absolute copy number, but it's only specified for copy number gains, while hemizygous or homozygous deletions are specified differently, as you're seeing.
4. If the log2 value close to 0 it could instead just be noise or imperfect centering. But it is true that the neutral value, i.e. cutoff between loss and gain, is zero. In array CGH analysis (which CNVkit mimics) it's common to treat log2 values between +/- 0.2 as effectively neutral copy number, and focus on greater deviations from zero.
5. The original copy number is the ploidy of your organism, e.g. humans are diploid, 2 copies of each autosome, and the sex chromosomes are XX or XY normally. If you use a flat reference for both tumor and normal, then you can interpret the log2 values as they are. If you used a single normal reference, then you should first check that the normal sample is copy-number-neutral at the location of interest (it probably is) before interpreting the tumor log2 ratio.
6. If the log2 value close to 0 it could instead just be noise or imperfect centering. But it is true that the neutral value, i.e. cutoff between loss and gain, is zero. In array CGH analysis (which CNVkit mimics) it's common to treat log2 values between +/- 0.2 as effectively neutral copy number, and focus on greater deviations from zero.

For a certain region in the .cns file, the log2 value is 0.382191. In the .vcf file, SVLEN is 6812878 and the CN value is 3. What is the copy number then?

In your example, a log2 value of .38 corresponds to 2^(.38) = 1.3 times the reference ploidy. For a diploid genome, the absolute copy number would be 2 * 2^(.38) = 2.6, which you can probably round up to 3 assuming some reasonable level of normal-cell contamination. (See the documentation on tumor heterogeneity for more guidance on this topic.) ---- https://www.biostars.org/p/178685/

log2 of -9.96578 is equivalent to a tumour-to-normal ratio of:

2^(-9.96578) = 0.00100000296

To find the copy number status of the normal sample, just run the same pipeline on it. If the normal sample was included in the CNV reference (cnv_reference.cnn), you can alternatively run the pipeline on the normal sample using a "flat" reference instead.

### sex chromosome
Running the "batch" command without the -y flag assumes a female reference, with expected/neutral ploidy of 2 for autosomes, 2 for X, and 1 for Y (for the sake of having a baseline level for comparing male samples). Using this reference, male samples without sex-chromosome abnormalities will have 1 X, 1 Y, so the log2 ratios you'll see are log2(1/2) = -1 for X, log2(1/1) = 0 for Y. A female sample will have log2(2/2) = 0 for X, log2(0/1) = -infinity (in practice, just some noisy deep negative values) for Y.

Rerunning "batch" with the -y flag, the expected ploidies of the sex chromosomes are 1 for X, 1 for Y. With a male reference a normal male sample with have log2(1/1) = 0 for both X and Y; a normal female sample will have log2(2/1) = +1 for X, log2(0/1) = -infinity (very low numbers) for Y. ---- https://www.biostars.org/p/190825/


## 过滤方法
1) Set region size
2) Filter regions according the coverage
3) Use Bayesian model
4) Set Rate for filtering CNV