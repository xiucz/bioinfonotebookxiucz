## Data Resources Required
https://data.broadinstitute.org/Trinity/CTAT_RESOURCE_LIB/
plug-n是已经建立好的reference lib, 而source里面包含了所需的原始文件。从原始文件构建reference lib的命令如下:
```
tar xvf GRCh37_v19_CTAT_lib_Feb092018.source_data.tar.gz
cd GRCh37_v19_CTAT_lib_Feb092018/

/local_data1/work/zhangbo/software/STAR-Fusion/FusionFilter/prep_genome_lib.pl \
--genome_fa ref_genome.fa \
--gtf ref_annot.gtf \
-fusion_annot_lib CTAT_HumanFusionLib.v0.1.0.dat.gz \
--annot_filter_rule AnnotFilterRule.pm \
--pfam_db PFAM.domtblout.dat.gz
```

http://note.youdao.com/noteshare?id=365f4e006571dab8d6b4e411eca81e57

## 检出率

+ I change the option "--alignSplicedMateMapLminOverLmate" from the default value 0.66 to a lower one