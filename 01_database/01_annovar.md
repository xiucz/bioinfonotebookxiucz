## 版本更新
**2019Oct24：**
+ 利用annotate_variation.pl中的-downdb参数可以允许refGeneWithVer作为一个有效的基因注释
+ add -intronhgvs argument to print out HGVS notations for intronic variants;
+ add startloss and startgain as functional consequences that affects the first ATG codon; add -nofirstcodondel to table_annovar by default to enable calculation of amino acid changes for certain variants previously annotated as 'wholegene'; minor adjustment on nonframeshift vs startloss vs stopgain for certain variants with multiple valid notations; changed p. notation for block substitution that does not cause protein change; changed table_annovar so that ExAC and gnomAD are treated as float fields in VCF annotation; allow genericdb for region annotation; allow chromosome name to contain . or - for certain species; the -polish argument is ON by default in table_annovar.pl; table_annovar.pl can generate column headers such as Otherinfo, Otherinfo2, Otherinfo3, etc; fixed a bug of cdot notation for block substitutions that cover 5UTR and start codon