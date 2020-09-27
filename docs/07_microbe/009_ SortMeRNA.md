由于版本的原因SortMeRNA version 2.1, 01/02/2016，需要说明一下：

SortMeRNA 是基于单端的数据去做分析的，存在一端比对上，另一端比对不上的情况，这种情况下，可以选择参数：--paired_in 则将两端的数据都算作rRNA
--paired_out 则将两端的数据都不算做rRNA

–paired-in 保证 –other 输出的 non-rRNA reads 都是符合 paired-end。
–paired-out 保证 –aligned 输出的 rRNA reads 都是符合 paired-end。


https://github.com/biocore/sortmerna/blob/master/scripts/test.jinja.yaml#L573