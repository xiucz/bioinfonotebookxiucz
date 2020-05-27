#### Removing adapters
接头的种类：
+ 
####  Demultiplexing

```
#adapter解析；reads根据adapter不同输出到不同的文件
cutadapt -a one=TATA -a two=GCGC -o trimmed-{name}.fastq.gz input.fastq.gz
#result files: demulti-one.fastq.gz, demulti-two.fastq.gz and demulti-unknown.fastq.gz

cutadapt -a file:barcodes.fasta --no-trim --untrimmed-o untrimmed.fastq.gz -o trimmed-{name}.fastq.gz input.fastq.gz
#result files: trimmed-first.1.fastq.gz, trimmed-second.1.fastq.gz, trimmed-unknown.1.fastq.gz and trimmed-first.2.fastq.gz, trimmed-second.2.fastq.gz, trimmed-unknown.2.fastq.gz
```

https://www.jianshu.com/p/4ee2f4d2292f
