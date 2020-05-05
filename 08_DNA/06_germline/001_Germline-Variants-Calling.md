#  VCF
```
ID: some ID for the variant, if known (e.g., dbSNP)
REF, ALT: reference and alternative alleles (on forward strand of reference)
QUAL = -10*log(1-p), where p is the probability of variant being present given the read data
FILTER: whether the variant failed a filter (filters defined by the user or program processing the file)
GT (genotype):
  0/0 reference homozygote
  0/1 reference-alternative heterozygote
  1/1 alternative homozygote
  0/2, 1/2, 2/2, etc. - possible if more than one alternative allele present
  ./. missing data
AD: allele depths
DP: total depth (may be different from sum of AD depths, a the latter include only reads significantly supporting alleles)
PL: genotype likelihoods (phred-scaled), normalized to the best genotype, e.g., PL(0/1) = -10*log[ Prob(data|0/1) 
/ Prob(data|best_genotype) ]
GQ: genotype quality – this is just PL of the second-best genotype
```

QUAL：基于Phred格式的表示ALT的质量，也可以理解为可靠性；可以理解为所call出来的变异位点的质量值。Q=-10lgP，Q表示质量值；P表示这个位点发生错误的概率。因此，如果想把错误率从控制在90%以上，P的阈值就是1/10，那lg（1/10）=-1，Q=（-10）*（-1）=10。同理，当Q=20时，错误率就控制在了0.01。这个参数其实和mpileup的-Q是重复的。

MQ不同于MAPQ，是RMS Mapping Quality，公式定义如下：q指的是比对到这个参考基因组碱基上的比对质量，即MAPQ。参考之前说的，MAPQ设置为20，假设每个碱基的MAPQ都是20，则MQ为20。


$$RMS=\sqrt{\frac{1}{N}\sum_{n}^{i=1}q_{i}^{2}}$$

大多数的reads的MAPQ都在60。

## Local Realignment
Why?
+ Each read is independently aligned to the reference genome to cut down
on the computational cost.
+ But this means that many reads spanning indels are likely to be
misaligned.
+ We see this as regions containing indels as well as clusters of
mismatching bases.

How does it work?
+ Step 1: Identify regions likely in need of realignment.
  + 1 – At least one read contains an indel.
  + 2 – A cluster of mismatching bases exists.
  + 3 – An already known indel segregates at the site.
+ Step 2: Perform a multiple sequence realignment in this region, such that the number of mismatching bases is minimized across all reads.

## Local reaglinment
![xx](https://github.com/xiucz/pics/blob/master/20191027.jpg?raw=true)


http://www.cbs.dtu.dk/courses/27626/Lectures/post_alignment_variantcalling_SR.pdf

### Ambiguity of alignment at indel sites

![xx](https://github.com/xiucz/pics/blob/master/201910272.jpg?raw=true)

Re-alignment no longer recommended if the genotyping method used downstream involves local haplotype 
assembly
FreeBayes，re-alignment implicit in the assembly algorithm；
HaplotypeCaller

Still needed if genotypes called from allelic depths at individual sites:
UnifiedGenotyper (GATK)
samtools


---
## Duplicate marking/removal

Why do we have duplicates in our data?
• The PCR amplification step included in the majority of NGS library construction techniques can introduce duplicates in the data.

Why do we need to remove these?
+ It will bias our variant calls.
+ PCR errors are propagated and sampled many times giving rise to FP variant calls.

Basic concepts of duplicate marking
http://picard.sourceforge.net/

algorithm:
1. Identify genomic position and strand for 5’-most bases.
2. Mark reads that are duplicatesof each other.
3. Within a group of duplicate reads the read with the highest sum of base quality scores is retained.

But it is not perfect…
+ Does not account for sequencing errors.
+ Does not account for natural duplicates.
+ Does not account for duplicate reads with different mapping locations


## Duplicate reads (fragments)
Optical duplicates: (Illumina) generated when a single cluster of reads is part of two adjacent tiles' on the same 
slide and used to compute two read calls separately
• Very similar in sequence (except sequencing errors).
• Identified where the first, say, 50 bases are identical between two reads and the read’s coordinates are close

Library duplicates (aka PCR duplicates): generated when the original sample is preamplified to such extent 
that initial unique targets are PCR replicated prior to library preparation and will lead to several independent 
spots on the Illumina slide.
• do not have to be adjacent on the slide
• share a very high level of sequence identity
• align to the same place on reference
• identified from alignment to reference 

![xx](https://github.com/xiucz/pics/blob/master/201910271.jpg?raw=true)

### Removing (marking) duplicates with PICARD
```
java -jar $PICARDDIR/picard.jar \
MarkDuplicates \
INPUT=sample1.sorted.bam \
OUTPUT=sample1.sorted.dedup.bam \
METRICS_FILE=sample1.sorted.dedup.metrics.txt
```

The metrics file will contain some stats about the de-duping
In the resulting BAM file, only one fragment from each duplicate group survives unchanged, other duplicate 
      fragments are given a flag 0x400 and will not be used downstream. 
Optimally, detection and marking of duplicate fragments should be done per library, i.e., over all read groups 
corresponding to a given library. • In practice, often sufficient to do it per lane (read group).


---
## Base quality score recalibration

How it works!
1. Collect information regarding the following features of the bases:
  + Reported quality score
  + Position within the read (machine cycle)
  + Dinucleotide context (current base plus previous base)
2. Count the number of times a site was a mismatch to the reference (excluding known polymorphic sites).
3. Estimate new quality score as:
```
$$Phred-scaled quality score =\frac{# of reference mismatches +1}{# of observed bases +2}$$
```
Example:
We observed A [AA, pos. 35, Q20] a 1,000,000 times.A in this context mismatches the reference a 1,000 times. This gives us: 
```
Q value = -10log10((1,000+1)/(1,000,000+2)) ~ Q30
```

## BQSR

Caveats: 
Local indel realignment should be done before recalibration

Known variation (SNPs and indels) have to be excluded (not a source of errors)


Variants found from read pileup; PL from allelic read depths and base qualities

Variants found from locally assembled haplotypes; PL from read alignment to haplotypes



## Variant calling & genotyping
The posterior probability of each genotype given the pileup of sequence reads is given by Bayes theorem:

![xx](https://github.com/xiucz/pics/blob/master/201910276.jpg?raw=true)

+ p(G|D): Prob. of genotype given data (posterior probability)
+ p(G): Prior prob. based on allele freqs. from HW
+ p(D|G): Prob. of data given genotype (genotype likelihood)
+ **p(D):** Prob. of data
+ p(b|G): Prob. of genotype given observed base at a site
+ e is the Phred scaled quality score of the base

![xx](https://github.com/xiucz/pics/blob/master/201910277.jpg?raw=true)
![xx](https://github.com/xiucz/pics/blob/master/201910278.jpg?raw=true)
![xx](https://github.com/xiucz/pics/blob/master/201910279.jpg?raw=true)

## Variant filtration (soft)
![xx](https://github.com/xiucz/pics/blob/master/2019102710.jpg?raw=true)

### Variant quality score recalibration
+ Variants as points in a cloud can be modeled using a Gaussian mixture model.
+ This model is used to estimate the probability that a variant is a true variant rather than a sequencer error or a data processing artifact.
+ The model is trained on known ‘true sites’.
+ Model parameters are applied to each variant in the input vcf-file and each variant is flagged by a VQSLOD score, which is the log odds ratio of the variant being a true variant versus being a false variant under the trained Gaussian mixture model. 
## Annotation
![xx](https://github.com/xiucz/pics/blob/master/2019102711.jpg?raw=true)


## Genotyping: 
### Maximum likelihood estimator

假设在某个总深度为90条reads的SNP位点观察到25条突变reads，该怎么估计该位点的基因型呢？

三种可能的基因型：
+ RR	
+ RA	
+ AA	

计算似然值，即某个指定基因型观察值的可能性
+ P(25AltReads,65RefReads|RR)	
+ P(25AltReads,65RefReads|RA)	
+ P(25AltReads,65RefReads|AA)

通常，我们观察到突变reads数目服从二项分布：

$$L(x|G) =\binom{90}{25}p^{25}(1-p)^{^{25}}$$

p的函数，假设该位点是二倍体，则p有3种可能的值：
+ RR	->	p=0	
+ RA	->	p=0.5	
+ AA	->	p=1	

由于要考虑到比对中的技术错误：
+ RR	->	p=0.05	
+ RA	->	p=0.5	
+ AA	->	p=0.95

### Incorporating additional information in our model
**Bayesian	Inference**


### Bayesian Statistics vs. Frequentist Statistics

+ The	main problem of	Statistcal Inference is	the	estimation of a	specific feature (parameter) of	a population	(for example, the genotype of a	given individual on	a given	locus)	
+ Classic (Frequentist)	Statistics assume that the feature is deterministic(fixed at the time of our observation)	
+ Bayesian	Statistics	assume	that	the	feature	is	random		
+ This	(apparently)	small	technical	difference	creates	a	huge	philosophical	dis;nc;on	
(and	a	lot	of	technicali;es	too)	
+ Sta;s;cians	tend	to	be	very	passionate	around	each	approach	(although	some	are	
pragma;c)




### Bayesian Statistics
• If	the	feature	of	the	popula;on	that	we	want	to	es;mate	is	a	random	variable,	it	
must	have	a	probability	distribu;on.	We	call	this	the	prior	distribu9on:	P(G)		
• We	take	a	sample	from	the	popula;on,	measure	our	observa;ons,	and	compute	
the	likelihood	or	our	data:	P(R|G)	
• We	can	then	update	our	knowledge	of	the	distribu;on	of	the	parameter	and	obtain	
its	posterior	distribu9on	using	the	Bayes	Theorem:	
P(G | R) =
P(G)P(R |G)
P(R)
• P(R)	is	difficult	to	compute	and	it	is	the	same	for	all	possible	genotypes,	so	in	most	
applica;ons	we	just	need	to	compute	the	numerator.	
• We	obtain	a	probability	distribu;on!	Not	a	single	value!	But	we	can	obtain	a	range	
of	values	with	high	probability	(credible	interval),	or	summarise	the	distribu;on	
with	the	most	likely	value	(maximum	a	posteriori)	or	the	mean	(posterior	mean)	



### Naïve Bayesian genotyping: Posterior probability

$$L(x|G) =\binom{90}{25}p^{25}(1-p)^{^{25}}$$


We	need	to	consider	Prior	probabili;es	for	each	genotype:	
+ $P(RR)=0.75$
+ $P(RA)=0.15$
+ $P(AA)=0.10$

We	can	compute	posterior	probabili;es:	
+ P(RR|x)=P(x|RR)P(RR)	
+ P(RA|x)=P(x|RA)P(RA)	
+ P(AA|x)=P(x|AA)P(AA)	

In situations where we	don’t have much	data (lower	depth),	or there is	more noise,	the	prior can have more	impact in the posterior. Let’s assume now 10 variant reads out of 50:


![xx](https://github.com/xiucz/pics/blob/master/201910273.jpg?raw=true)
![xx](https://github.com/xiucz/pics/blob/master/201910274.jpg?raw=true)
![xx](https://github.com/xiucz/pics/blob/master/201910274.jpg?raw=true)


### Haplotype likelihood function in HaplotypeCaller


1. Define active regions
The program determines which regions of the genome it needs to operate on, based on the presence of significant
evidence for variation.
2. Determine haplotypes by re-assembly of the active region
For each ActiveRegion, the program builds a De Bruijn-like graph to reassemble the ActiveRegion, and identifies what
are the possible haplotypes present in the data. The program then realigns each haplotype against the reference
haplotype using the Smith-Waterman algorithm in order to identify potentially variant sites.
3. Determine likelihoods of the haplotypes given the read data P{Dj|H}
For each ActiveRegion, the program performs a pairwise alignment of each read against each haplotype using the
PairHMM algorithm. This produces a matrix of likelihoods of haplotypes given the read data. These likelihoods are
then marginalized to obtain the likelihoods of alleles for each potentially variant site given the read data.
4. Assign sample genotypes
For each potentially variant site, the program applies Bayes’ rule, using the likelihoods of alleles given the read data
to calculate the likelihoods of each genotype per sample given the read data observed for that sample. The most
likely genotype is then assigned to the sample.

Haplotype assembly uses reads from all samples (rather than one at a time), and so…resulting VCF file will not
 be exactly equivalent to that obtained from gVCF mode runs followed by GenotypeGVCF

Broad recommends running HaplotypeCaller-based pipeline instead of UnifiedGenotyper
However, still recommended for
Pooled sample cases
High ploidy cases


-stand_call_conf [number]
Variants with quality score (QUAL) less than [number] will not be written to VCF file. Good to set this
low – better have too many raw variants than too few. Can always filter VCF file later. Default: 30.

-dcov [int]
Read depth at any locus will be capped at [number]; the goal is to provide even distribution of read
start positions while removing excess coverage. For truly unbiased down-sampling, use -dfrac
Defaults are usually high (250) – can be reduced to speed things up

## Haplotypes and Computational Phasing
A	haplotype	is	a	set	of	gene;c	features	that	tend	to	be	inherited	together	
In	our	context,	we	are	interested	in	a	set	of	SNPs	that	tend	to	be	inherited	together.		
Why?	Because	it	will	make	variant	calling	more	robust	