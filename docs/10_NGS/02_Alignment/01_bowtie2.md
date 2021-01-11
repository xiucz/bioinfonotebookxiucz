```
-t/--time --un <path> 将unpaired reads写入到<path>.
--al <path> 将至少能比对1次以上的unpaired reads写入<path>.
--un-conc <path> 将不能和谐比对的paired-end reads写入<path>.
--al-conc <path> 将至少能和谐比对一次以上的paired-end reads写入<path>.

```

```bash
10000 reads; of these:
#第一部分:是pair-end模式下的一致的比对结果
#表示总共有这么多 对 的reads
  10000 (100.00%) were paired; of these:
#表示reads1和reads2不能合理的比对到基因组上
    650 (6.50%) aligned concordantly 0 times
#表示合理比对到基因组上了，reads1和reads2同时比对上并且只有一种比对结果
    8823 (88.23%) aligned concordantly exactly 1 time
#表示合理比对到基因组上了，但是reads1和reads2可以同时比对上但是可以比对到多个地方
    527 (5.27%) aligned concordantly >1 times
    ----
#第二部分：是pair-end模式下不一致的比对结果
    650 pairs aligned concordantly 0 times; of these:
#表示reads1和reads2都能比对上，但是比对不合理（比如比对方向不对或者插入片段有限）
      34 (5.23%) aligned discordantly 1 time
    ----
#第三部分：对剩余reads（既不能concordantly，也不能discordantly 1 times）的单端模式的比对
#表示10000-(8823+527+34=616
    616 pairs aligned 0 times concordantly or discordantly; of these:
#表示总共有这么多条reads,616*2
      1232 mates make up the pairs; of these:
#表示没有比对上的
        660 (53.57%) aligned 0 times
#表示比对上并且只有一种比对结果的
        571 (46.35%) aligned exactly 1 time
#表示比对上但能比对到多个地方
        1 (0.08%) aligned >1 times
#表示比对率。(8823*2 + 527*2 + 34*2 + 571 + 1)/10000*2
96.70% overall alignment rate
```

```
03/18/2020 09:50:22 PM - kneaddata.utilities - INFO: Execute command: kneaddata_bowtie2_discordant_pairs --bowtie2 /local_data1/pipeline/Transcriptome/PB_Isoseq_noref_V1.0/software/bowtie2-2.3.5-linux-x86_64/bowtie2 --threads 5 -x /local_data1/MED/database/cat/GCF_000181335.3_Felis_catus_9.0_genomic --bowtie2-options "--very-sensitive --phred33" -1 /local_data1/MED/projects/zhangbo/mngs/YF2020-01/out/02_deplete/kneaddata/host/decompressed_59a0fv5r_GG-PCR.trim_1P.fastq -2 /local_data1/MED/projects/zhangbo/mngs/YF2020-01/out/02_deplete/kneaddata/host/decompressed_unqs1m8r_GG-PCR.trim_2P.fastq --un-pair /local_data1/MED/projects/zhangbo/mngs/YF2020-01/out/02_deplete/kneaddata/host/GG-PCR_GCF_000181335.3_Felis_catus_9.0_genomic_bowtie2_paired_clean_%.fastq --al-pair /local_data1/MED/projects/zhangbo/mngs/YF2020-01/out/02_deplete/kneaddata/host/GG-PCR_GCF_000181335.3_Felis_catus_9.0_genomic_bowtie2_paired_contam_%.fastq --un-single /local_data1/MED/projects/zhangbo/mngs/YF2020-01/out/02_deplete/kneaddata/host/GG-PCR_GCF_000181335.3_Felis_catus_9.0_genomic_bowtie2_unmatched_%_clean.fastq --al-single /local_data1/MED/projects/zhangbo/mngs/YF2020-01/out/02_deplete/kneaddata/host/GG-PCR_GCF_000181335.3_Felis_catus_9.0_genomic_bowtie2_unmatched_%_contam.fastq -S /dev/null


kneaddata_bowtie2_discordant_pairs --bowtie2 /local_data1/pipeline/Transcriptome/PB_Isoseq_noref_V1.0/software/bowtie2-2.3.5-linux-x86_64/bowtie2 --threads 6 -x /local_data1/MED/workdir/chengyuanfang/database/mouse_M25/ref/GRCm38.p6.genome.fa --bowtie2-options "--very-sensitive --phred33" -1 /local_data1/MED/projects/zhangbo/mngs/202012/whfs-xs-201118/20201218_bowtie2/decompressed_lf3e3m1k_OP4.trim_1P.fastq -2 /local_data1/MED/projects/zhangbo/mngs/202012/whfs-xs-201118/20201218_bowtie2/decompressed_1xkt1b58_OP4.trim_2P.fastq --un-pair /local_data1/MED/projects/zhangbo/mngs/202012/whfs-xs-201118/20201218_bowtie2/OP4_GRCm38.p6.genome.fa_bowtie2_paired_clean_%.fastq --al-pair /local_data1/MED/projects/zhangbo/mngs/202012/whfs-xs-201118/20201218_bowtie2/OP4_GRCm38.p6.genome.fa_bowtie2_paired_contam_%.fastq --un-single /local_data1/MED/projects/zhangbo/mngs/202012/whfs-xs-201118/20201218_bowtie2/OP4_GRCm38.p6.genome.fa_bowtie2_unmatched_%_clean.fastq --al-single /local_data1/MED/projects/zhangbo/mngs/202012/whfs-xs-201118/20201218_bowtie2/OP4_GRCm38.p6.genome.fa_bowtie2_unmatched_%_contam.fastq --reorder -S /dev/null

```

## 心得
+ 在利用bowtie2 比对 获取未比对的reads时，比如应用于去除核糖体，最好加上--reorder ，避免了生成的read pairs 乱序，虽然耗时长点。也并不是所有的都是乱序情况。