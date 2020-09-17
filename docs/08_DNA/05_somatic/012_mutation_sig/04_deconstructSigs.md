https://mp.weixin.qq.com/s/k7yzk9hPX3Bi-ohAo83ZYw

https://zhuanlan.zhihu.com/p/99537144

https://zhuanlan.zhihu.com/p/98446111


https://my.oschina.net/u/4400737/blog/3244085

https://fougner.co/notes/mutspec

vcf input
```
sampleID <- "Your_SampleID_Here"
input_vcf <- "/path/to/variants.vcf"

vcf_colnames <- c("CHROM", "POS", "ID", "REF", "ALT", "QUAL", "FILTER", "INFO", "FORMAT", sampleID)
variants <- read.delim(file = input_vcf, header = FALSE, sep = '\t',
                       comment.char = '#', col.names = vcf_colnames, check.names = FALSE)

# add sample ID column
variants[["SampleID"]] <- sampleID

# convert to signatures format
sigs.input <- mut.to.sigs.input(mut.ref = variants,
                                sample.id = "SampleID",
                                chr = "CHROM",
                                pos = "POS",
                                ref = "REF",
                                alt = "ALT")
```