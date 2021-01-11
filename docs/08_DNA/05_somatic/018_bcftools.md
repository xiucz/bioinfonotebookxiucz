+ Does “multiallelic” denote “more than 2 alleles” rather than “not monomorphic”?

Yes. 2 or more alternate alleles at a site.

+ The number of SNPS+indels+others does not sum to the total number of records. Is this because an SNP can also be an indel or “other?”

A mutilallelic site may contain both a SNP and an indel for instance and hence will contribute a count to both tallies.
+ What types of variants are covered by “others” here?

complex substitutions, SVs, complex rearrangements. Basically anything not a SNP, insertion/deletion (unless represented by a symbolic allele) or MNP.
+ Why is the id[2] field blank for all sections of my output?

id field is relevant when comparing two files with bcftools stats. id will then be 0,1 or 2 and will represent sites unique to the first file, sites unique to the second file and sites common to both.

https://github.com/samtools/bcftools/issues/316

## 过滤
### bcftools filter
```
bcftools view -q 0.01 chr.all.vcf.gz 
bcftools view -q 0.01:minor chr.all.vcf.gz

```
```
bcftools view -e 'INFO/IMPUTED=1' or bcftools view -i 'INFO/IMPUTED=0'
```

## 注释
```
export BCFTOOLS_PLUGINS=/local_data1/MED/programs/zhangbo/bcftools/bcftools-1.9/plugins/
bcftools +fill-tags my.vcf > out.vcf
```