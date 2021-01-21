对psl输出结果，需要注意一下几点：

1. blat的结果在subject上允许存在很大的gap（intron区域），所以同一个结果在query和subjects上覆盖的区域可能会相差很多，这一点与blast不同。
2. 在基因对基因组的比对中，block的个数不能等同于exon的个数。因为blat对block的定义是一个没有插入缺失的比对，任何插入或者缺失的碱基都会使一个block终止，所以一个exon很可能是有很多block构成的。因此exon和intron的个数要通过足够大的gap来判断。
3. psl结果里面碱基位置的计算是从0开始的而不是1.

```
#blat常见用法
#处理单个job
blat chr11.fa human/test.fa test.psl #输出不含序列
blat chr11.fa human/test.fa -out=pslx test.pslx #输出含序列
blat chr11.fa human/test.fa -out=blast test.blast #输出格式同NABI的blast格式
#并行处理多个jobs
time parallel blat chr{}.fa human/human.fa test_{}.psl ::: {1..22} X Y M
#
blat database query -out=blast8 output.psl
```

