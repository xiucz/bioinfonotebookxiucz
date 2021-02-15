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
## 其工作原理分为三步：

+ ①先将reads通过STAR比对到参考基因组，筛选出split和discordant reads作为候选的融合基因序列；
+ ②将候选融合基因序列与参考基因序列进行比对，根据overlaps预测出融合基因；
+ ③对预测结果做过滤，去除假阳性结果。

## 检出率

+ I change the option "--alignSplicedMateMapLminOverLmate" from the default value 0.66 to a lower one

## 设计引物来对该融合转录本进行验证