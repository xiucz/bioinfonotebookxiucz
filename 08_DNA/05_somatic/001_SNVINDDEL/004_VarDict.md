## github
github链接：https://github.com/AstraZeneca-NGS/VarDict

过滤策略：http://bcb.io/2016/04/04/vardict-filtering/

https://github.com/AstraZeneca-NGS/VarDict/blob/master/vcf2txt.pl

java版本：https://github.com/AstraZeneca-NGS/VarDictJava

<path_to_vardict_folder>/build/install/VarDict/bin/VarDict -G /path/to/hg19.fa -f $AF_THR -N sample_name -b /path/to/my.bam -z -c 1 -S 2 -E 3 -g 4 /path/to/my.bed | VarDict/teststrandbias.R | VarDict/var2vcf_valid.pl -N sample_name -E -f $AF_THR

<path_to_vardict_folder>/build/install/VarDict/bin/VarDict  -G /path/to/hg19.fa -f 0.001 -N sample_name -b /path/to/sample.bam  -z -R  chr7:55270300-55270348:EGFR | VarDict/teststrandbias.R | VarDict/var2vcf_valid.pl -N sample_name -E -f 0.001 >vars.vcf

https://www.jianshu.com/p/3bdd94be6949
