```
library("PureCN")
$ Rscript $PURECN/IntervalFile.R --infile baits_hg19.bed \
                  --fasta hg19.fa --outfile $OUT_REF/baits_hg19_intervals.txt \
                  --offtarget --genome hg19 \
                  --export $OUT_REF/baits_optimized_hg19.bed \
                  --mappability wgEncodeCrgMapabilityAlign100mer.bigWig \
                  --reptiming wgEncodeUwRepliSeqK562WaveSignalRep1.bigWig

#--offtarget, 将包含脱靶的reads，对于扩增子数据不建议使用；
#--genome, 利用gene symbols来注释外显子。对于人类参考基因组，不用使用b37/b38，使用hg19/hg38. without ALT contigs
```
