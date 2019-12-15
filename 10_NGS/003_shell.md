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