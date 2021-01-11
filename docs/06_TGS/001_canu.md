https://canu.readthedocs.io/en/latest/index.html

Canu分为三个步骤，纠错，修整和组装，每一步都差不多是如下几个步骤：
```
加载read到read数据库，gkpStore
对k-mer进行技术，用于计算序列间的overlap
计算overlap
加载overlap到overlap数据库，OvlStore
根据read和overlap完成特定分析目标
read纠错时会从overlap中挑选一致性序列替换原始的噪声read
read修整时会使用overlap确定read哪些区域是高质量区域，哪些区域质量较低需要修整。最后保留单个最高质量的序列块
序列组装时根据一致的overlap对序列进行编排(layout), 最后得到contig。
```
## 第一步：纠错。
三代测序本身错误率高，使得原始数据充满了噪音。这一步就是通过序列之间的相互比较纠错得到高可信度的碱基。主要调整两个参数
```
corOutCoverage: 用于矫正的数据最小coverage，默认是40x,但实际上的数据在30X-35X之间你可以自己设置为50,60,100,当设置为1000，可以用于组装出数据中质粒，一般该参数用于宏基因组组装。用于控制多少数据用于纠错。比如说拟南芥是120M基因组，100X测序后得到了12G数据，如果只打算使用最长的6G数据进行纠错，那么参数就要设置为50(120m x 50)。设置一个大于测序深度的数值，例如120，表示使用所有数据。
```
```
canu -correct \
    -p ath -d pb_ath \
    Threads=10 gnuplotTested=true\
    genomeSize=120m minReadLength=2000 minOverlapLength=500\
    corOutCoverage=120 corMinCoverage=2 \
    -pacbio-raw pb.fasta.gz
```

## 第二步：修整。
这一步的目的是为了获取更高质量的序列，移除可疑区域（如残留的SMRTbell接头).
```
canu -trim \
        -p ath -d pb_ath
        maxThreads=20 gnuplotTested=true\
        genomeSize=120m minReadLength=2000 minOverlapLength=500\
        -pacbio-corrected ath/pb_ath.correctedReads.fasta.gz
    
```

## 第三步: 组装。
在前两步获得高质量的序列后，就可以正式进行组装. 这一步主要调整的就是纠错后的序列的错误率， correctedErrorRate，它会影响utgOvlErrorRate。这一步可以尝试多个参数，因为速度比较块。
```
canu -assemble \
    -p ath -d ath-erate-0.035 \
    maxThreads=20 gnuplotTested=true \
    genomeSize=120m\
    correctedErrorRate=0.035 \
    -pacbio-corrected atg/pb_ath.trimmedReads.fasta.gz
```


https://www.jianshu.com/p/c1aeeae77cb5


https://www.jianshu.com/p/0102b41e1d04


4：几个重要的参数说明：


```
minReadLength 用于组装的最短reads,默认1000

 


 

contigFilter="2 1000 0.75 0.75 2"关于contig的过滤

has fewer than minReads (2) reads, or（这个值可以设置为5）
is shorter than minLength (1000), or
has a single read spanning singleReadSpan percent (75%) of the contig, or
has less than lowCovDepth (2) coverage over at least lowCovSpan fraction (0.75) of the contig
对于低覆盖数据correctedErrorRate=0.075（4.5%-7.5%或者更多）也可以大于1%

对于高覆盖度数据correctedErrorRate=0.040（4.0%-4.5%），默认The default is 0.045 for PacBio reads，也可以小于1%

 

如果是AT（GC）富集的样本，建议设置corMaxEvidenceErate=0.15
```

# HIFIcanu

https://medium.com/pacbio/hifi-assembler-series-part-2-hicanu-near-optimal-repeat-resolution-using-hifi-reads-412728ed167f