
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
