## ack
## rsync
## tee

### 统计fastq中每个读长的counts数
```
直接用awk命令

awk '{if(NR%4==2) print length($1)}' read.fastq | sort -n | uniq -c > reads_length.txt
NR%4==2的意思是以每四行为一个组，统计每组的第二行（在fastq文件中就是碱基信息行）。

然后用R

library(ggplot2)

reads <- read.csv('reads_length.txt', sep=' ', header=FALSE)
ggplot(reads, aes(x=reads$V2, y=reads$V1)) + 
	geom_bar(stat='identity') + 
	xlab('read length') + 
	ylab('counts') + 
	ggtitle('Read Length Distribution')
```

### shell数组
```
https://www.cnblogs.com/qdhxhz/p/10902110.html

for i in `ls /local_data1/MED/projects/linzuopeng/shen/bam/*.sorted.bam`
do
sample=`basename $i`
#array=(`echo $str | tr '.' ' '`)
#array=(${string//,/ })

sample_tmp=
dir=`dirname $i`

oldIFS=$IFS
IFS=.
arr=($sample)
IFS=$oldIFS
#for s in ${arr[@]};do;echo "$s";done
#for i in "${!array[@]}"; do echo "$i：${array[i]}";done
sample_name=${arr[0]}

echo "/local_data1/work/zhangbo/software/samtools-1.9/samtools view -@ 4 -F 256 -ub $i > $i.tmp.bam \
&& mkdir -p $dir/$sample_name \
&& /local_data1/work/zhangbo/software/bamdst/bamdst -p \
/local_data1/MED/medicine_temp/S07604514_Covered_sorted_merged_hg19.bed \
-o $dir/$sample_name $i.tmp.bam \
&& rm -f $i.tmp.bam"

done
```