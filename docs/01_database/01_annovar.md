## 版本更新
**2019Oct24：**
+ 利用annotate_variation.pl中的-downdb参数可以允许refGeneWithVer作为一个有效的基因注释
+ add -intronhgvs argument to print out HGVS notations for intronic variants;
+ add startloss and startgain as functional consequences that affects the first ATG codon; add -nofirstcodondel to table_ by default to enable calculation of amino acid changes for certain variants previously annotated as 'wholegene'; minor adjustment on nonframeshift vs startloss vs stopgain for certain variants with multiple valid notations; changed p. notation for block substitution that does not cause protein change; changed table_ so that ExAC and gnomAD are treated as float fields in VCF annotation; allow genericdb for region annotation; allow chromosome name to contain . or - for certain species; the -polish argument is ON by default in table_.pl; table_.pl can generate column headers such as Otherinfo, Otherinfo2, Otherinfo3, etc; fixed a bug of cdot notation for block substitutions that cover 5UTR and start codon


## cosmic数据库准备
```
#!/bin/bash
# ./cosmic2annovar.sh mail:passwd

for i in "CosmicCodingMuts.vcf.gz" "CosmicNonCodingVariants.vcf.gz"
do
    curl $(curl -H "Authorization: Basic $(echo "$1" | base64)" https://cancer.sanger.ac.uk/cosmic/file_download/GRCh37/cosmic/v90/VCF/$i | awk -F '"' '{print $4}') -o $i
done 


for i in "CosmicMutantExport.tsv.gz" "CosmicNCV.tsv.gz"
do
    curl $(curl -H "Authorization: Basic $(echo "$1" | base64)" https://cancer.sanger.ac.uk/cosmic/file_download/GRCh37/cosmic/v90/$i | awk -F '"' '{print $4}') -o $i
done

gunzip *.gz

./prepare_annovar_user.pl -dbtype cosmic CosmicMutantExport.tsv -vcf CosmicCodingMuts.vcf >hg19_cosmic90.txt
./prepare_annovar_user.pl -dbtype cosmic CosmicNCV.tsv -vcf CosmicNonCodingVariants.vcf >>hg19_cosmic90.txt

sort -V hg19_cosmic90.txt >hg19_cosmic90_sort.txt

mv hg19_cosmic90_sort.txt hg19_cosmic90.txt

./index_annovar.pl hg19_cosmic90.txt 1000
```