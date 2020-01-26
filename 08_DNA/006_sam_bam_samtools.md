[toc]

http://www.htslib.org/download/
```
[zhangbo@mu01 software]$ wget -c https://github.com/samtools/samtools/releases/download/1.9/samtools-1.9.tar.bz2

[zhangbo@mu01 software]$ tar -jxvf samtools-1.9.tar.bz2 
./configure --prefix=/where/to/install
make
make install
export PATH=/where/to/install/bin:$PATH    # for sh or bash users

```

## bam file visualize
http://jonathancrabtree.github.io/Circleator/tutorials/coverage_plots.html

https://blog.csdn.net/tanzuozhev/article/details/80854579


### sam文件格式sam格式的定义：
https://samtools.github.io/hts-specs/SAMv1.pdf
http://broadinstitute.github.io/picard/explain-flags.html
 1. 第3和第7列，可以用来判断某条reads是否比对成功到了基因组的染色体，左右两条reads是否比对到同一条染色体。
而第1，10，11列可以提取出来还原成我们的测序数据fastq格式的。
2. 第2列是二进制转换成十进制后的和。 
3. 第5列，比对结果的质量值，也是因工具而异。 
4. 第6列CIGAR是比较重要的，解释如下，其中M并不是说match，所以我们的PE 150的reads，大部分都会是150M，但是并不代表着跟参考序列一模一样。其中S/H是比较特殊的，很难讲清楚，但是大部分情况下用不到。
（soft-clipping碱基是指一条reads未匹配上当前基因组位置的部分，如果有多个reads在这种情况并且这些reads的soft-clipping碱基都能够比对在基因组另一位置，那么就可能存在SV） 
5. RG代表着你的sam文件比对来自于哪个样本的fastq程序结果。NM这个tag是编辑距离，大概就是你的reads如果想转变成参考基因组，需要改变多少个碱基，如果编辑距离是0才说明你的这个150bp长度的序列跟参考基因组一模一样。
6. 从整体来看，一个bam文件的行数是约等于reads数的，但考虑到少数reads可能比对多基因组上多个位置，所以行数一般是略大于reads数。

```
Tag	Meaning
NM	Edit distance
MD	Mismatching positions/bases
AS	Alignment score
BC	Barcode sequence
X0	Number of best hits
X1	Number of suboptimal hits found by BWA
XN	Number of ambiguous bases in the referenece
XM	Number of mismatches in the alignment
XO	Number of gap opens
XG	Number of gap extentions
XT	Type: Unique/Repeat/N/Mate-sw
XA	Alternative hits; format: (chr,pos,CIGAR,NM;)*
XS	Suboptimal alignment score
XF	Support from forward/reverse alignment
XE	Number of supporting seeds
```

## samtools
#### 对fasta文件建立索引
```
samtools faidx ref_genome.fasta

提取子序列
samtools faidx ref_genome.fasta scffold_10 > scaffold_10.fasta
```
#### sam文件<=>bam文件
```bash
samtools view -h sample.bam > sample.sam samtools view -bS sample.sam > sample.bam
```
#### view
```
0x1 PAIRED paired-end (or multiple-segment) sequencing technology
0x2 PROPER_PAIR each segment properly aligned according to the aligner
0x4 UNMAP segment unmapped 0x8 MUNMAP next segment in the template unmapped
0x10 REVERSE SEQ is reverse complemented
0x20 MREVERSE SEQ of the next segment in the template is reverse complemented
0x40 READ1 the first segment in the template
0x80 READ2 the last segment in the template
0x100 SECONDARY secondary alignment
0x200 QCFAIL not passing quality controls
0x400 DUP PCR or optical duplicate
0x800 SUPPLEMENTARY supplementary alignment
```

```bash
1)提取没有比对到参考序列上的比对结果
samtools view -bf 4 abc.bam > abc.f.bam

2)提取比对到参考序列上的比对结果
samtools view -bF 4 abc.bam > abc.F.bam

3)提取paired reads中两条reads都比对到参考序列上的比对结果，只需要把两个4+8的值12作为过滤参数即可
samtools view -bF 12 abc.bam > abc.F12.bam

4)提取bam文件中比对到caffold1上的比对结果，并保存到sam文件格式
samtools view abc.bam scaffold1 > scaffold1.sam

5)提取scaffold1上能比对到30k到100k区域的比对结果
samtools view abc.bam scaffold1:30000-100000 > scaffold1_30k-100k.sam

6)根据fasta文件，将 header 加入到 sam 或 bam 文件中
samtools view -T genome.fasta -h scaffold1.sam > scaffold1.h.sam

7)查看bwa比对结果中比对上基因组的unique mapped reads
samtools view xx.bam |grep "XT：A：U" | wc -l

8)samtools idxstats选项输出记录由tab分隔，其中第1列为参考序列ID(染色体号)，第二列为参考序列长度，第三列为mapped reads数，第四列为unmapped reads数

9)快速计算一个bam文件的reads数
samtools idxstats in.bam|awk "{s+=$3+$4}’END{print s}"

10)Subsample BAM file
samtools view -s 0.5 test.bam > test_hafl_random.bam

11)bam2fastq
bam2fastq工具链接：http://www.hudsonalpha.org/gsl/software/bam2fastq.php

12）
samtools views -c 计算的是alignments数目，而不是reads数目，一条reads可以比对到多个位置

13）提取某个染色体
samtools view -h  control_1.sort.bam   "chr17:40437407-40486397"  |samtools view -bS - >RNA-seq.bam

```

```bash
想知道有多少paired end reads有mate并且都有map时，可以使用-f 1 -F 12来过滤
samtools view -c -f 1 -F 12 test.bam
其中-f 1指定只包含那些paired end reads，-F 12是不包含那些unmapped(flag 0×0004)以及mate是unmapped(flag 0×0008)。0×0004 + 0×0008 = 12.
```
测序数据的双端的，那么sam文件的第3列是reads1的比对情况，第6列是reads2的比对情况。所以未比对成功的测序数据可以分成3类，仅reads1，仅reads2，和两端reads都没有比对成功。
也可以用下面的代码分步提取这3类未比对成功的reads:
```
samtools view -u  -f 4 -F264 alignments.bam  > tmps1.bam
samtools view -u -f 8 -F 260 alignments.bam  > tmps2.bam
samtools view -u -f 12 -F 256 alignments.bam > tmps3.bam
samtools merge -u - tmps[123].bam | samtools sort -n - unmapped
bamToFastq -bam unmapped.bam -fq1 unmapped_reads1.fastq -fq2 unmapped_reads2.fastq
```
```bash
samtools view sample_sorted.bam chr1:1234-123456
samtools flagstat sample_sorted.bam
```

#### sort
```bash
samtools sort sample.bam sort_default
samtools sort -n sample.bam sort_left
```
Sort alignments by leftmost coordinates, or by read name when -n is used.  默认按照染色体位置进行排序，而-n参数则是根据read名进行排序。

#### Ref_Info
https://www.biostars.org/p/102735/

#### flagstat
```
#Version: 1.3
samtools flagstat example.bam
14367369 + 0 in total (QC-passed reads + QC-failed reads) #总共的reads数
26939 + 0 secondary
0 + 0 supplementary
0 + 0 duplicates
13229644 + 0 mapped (92.08% : N/A) #总体上reads的匹配率
14340430 + 0 paired in sequencing #有多少reads是属于paired reads
7170215 + 0 read1
7170215 + 0 read2
13005936 + 0 properly paired (90.69% : N/A) #完美匹配的reads数：比对到同一条参考序列，并且两条reads之间的距离符合设置的阈值
13109520 + 0 with itself and mate mapped #paired reads中两条都比对到参考序列上的reads数
93185 + 0 singletons (0.65% : N/A) #单独一条匹配到参考序列上的reads数，和上一个相加，则是总的匹配上的reads数。
0 + 0 with mate mapped to a different chr # #paired reads中两条分别比对到两条不同的参考序列的reads数
0 + 0 with mate mapped to a different chr (mapQ>=5)

```

```
#cat junti.bowtie.err
5421701 reads; of these:
5421701 (100.00%) were paired; of these:
705166 (13.01%) aligned concordantly 0 times
4524924 (83.46%) aligned concordantly exactly 1 time
191611 (3.53%) aligned concordantly >1 times
----
705166 pairs aligned concordantly 0 times; of these:
226261 (32.09%) aligned discordantly 1 time
----
478905 pairs aligned 0 times concordantly or discordantly; of these:
957810 mates make up the pairs; of these:
644240 (67.26%) aligned 0 times
8949 (0.93%) aligned exactly 1 time
304621 (31.80%) aligned >1 times
94.06% overall alignment rate


A pair that aligns with the expected relative mate orientation and with the
expected range of distances between mates is said to align "concordantly". If
both mates have unique alignments, but the alignments do not match paired-end
expectations (i.e. the mates aren't in the expcted relative orientation, or
aren't within the expected disatance range, or both), the pair is said to align
"discordantly".

total reads: 5421701*2
mapping ratio: 1 - 644240*1/5421701*2
```

```
[0] samtools flagstat plus01791.bam

[1] 33037592 + 0 in total (QC-passed reads + QC-failed reads)
[2] 0 + 0 secondary
[3] 0 + 0 supplementary
[4] 0 + 0 duplicates
[5] 31144385 + 0 mapped (94.27% : N/A)
[6] 33037592 + 0 paired in sequencing
[7] 16518796 + 0 read1
[8] 16518796 + 0 read2
[9] 25887744 + 0 properly paired (78.36% : N/A)
[10] 31089116 + 0 with itself and mate mapped
[11] 55269 + 0 singletons (0.17% : N/A)
[12] 0 + 0 with mate mapped to a different chr
[13] 0 + 0 with mate mapped to a different chr (mapQ>=5)
```
```
[14] 16518796 reads; of these:
[15] 16518796 (100.00%) were paired; of these:
[16] 3574924 (21.64%) aligned concordantly 0 times
[17] 11980802 (72.53%) aligned concordantly exactly 1 time
[18] 963070 (5.83%) aligned concordantly >1 times
----
[19] 3574924 pairs aligned concordantly 0 times; of these:
[20] 1747451 (48.88%) aligned discordantly 1 time
----
[21] 1827473 pairs aligned 0 times concordantly or discordantly; of these:
[22] 3654946 mates make up the pairs; of these:
[23] 1893207 (51.80%) aligned 0 times
[24] 57506 (1.57%) aligned exactly 1 time
[25] 1704233 (46.63%) aligned >1 times
[26] 94.27% overall alignment rate
```
```
[27] 'sample' 'total_reads' 'mapped_reads' 'pair_mapped_reads' 'single_mapped_reads' 'unique_mapped_reads' 'multi_mapped_reads'
[28] plus01791 33037592 31144385 29382646 1761739 27514012 1704233
```
**TIPS:**
[1]: 'total' is the total number of alignments (lines in the sam file), not total reads.'total' is the total number of alignments (lines in the [sam](http://samtools.sourceforge.net/SAM1.pdf) file), not total reads.
[9]:"Properly paired" means both mates of a read pair map to the same chromosome, oriented towards each other, and with a sensible insert size.

#### Ref_Info
https://www.biostars.org/p/12475/

```
samtools view -@ 8 -bF 1024 过滤PCR扩增的接头序列
samtools view -bF 256 计算mappingratio的时候，因为是因为mem算法比对，需要先过滤打断比对的reads
samtools view -@ 8 -f 1 -F 12 提取paired mapped reads
```

## Concept

**1.** **Chimeric reads** occur when one sequencing read aligns to two distinct portions of the genome with little or no overlap. Chimeric reads are indicative of structural variation. Chimeric reads are also called **split reads**.断裂
+ **1.1.** After aligning with [bwa](http://bio-bwa.sourceforge.net/) mem, chimeric reads will have an SA tag
+ **1.2.** Also note that chimeric reads are not the same as chimeric genes. In RNA-seq chimeric reads may indicate the presence of chimeric genes but for DNA-seq they often are evidence for structural variation without necessarily being evidence for chimeric gene/transcript events.
+ **1.3.** In RNA-seq chimeric reads may indicate the presence of circRNAs
```
samtools view my_alignment.bam | grep 'SA:' | less
```

**2.** **soft-clip reads**
**3.** **hard-clip reads**
**4.** **clipped reads**
+ Hard masked bases do not appear in the SEQ string, soft masked bases do.
+ If your cigar is: `10H10M10H` then the SEQ will only be 10 bases long.
+ If your cigar is `10S10M10S` then the SEQ and base-quals will be 30 bases long.
+ In the case of soft-masking, even though the SEQ is present, it is not used by variant callers and not displayed when you view your data in a viewer. In either case, masked bases should not be used in calculating coverage.
+ Masking simply means the part of the read can not be aligned to the genome (simplified, but a reasonable assumption for most cases, I think). A deletion means that a stretch of genome is not present in the sample and therefore not in the reads.

```
Hi brentp. My understanding of the choice between soft-clipping and hard-clipping is that hard-clipping is applied when the clipped bases align elsewhere in the reference genome, i.e chimeric reads. At least in bwa this appears to be when hard clipping is used. I'm not sure about other aligners?

bwa-mem 0.7.5 release notes from http://seqanswers.com/forums/showthread.php?t=31237:

"Changed the way a chimeric alignment is reported (conforming to the upcoming
SAM spec v1.5). With 0.7.5, if the read has a chimeric alignment, the paired
or the top hit uses soft clipping and is marked with neither 0x800 nor 0x100
bits. All the other hits part of the chimeric alignment will use hard
clipping and be marked with 0x800 if option "-M" is not in use, or marked
with 0x100 otherwise."

As an example, here's part of a bam file with a read pair containing a chimeric read. The top hit is soft clipped and the second top hit is hard clipped and marked as secondary by BWA (-M option).

20692128 97 viral_genome 21417 60 69M32S chr7 101141242 0 TACATCTTCTCCCTCTCTCACGACACAAGAATTAGTCACATAGGGATGTTCTCGTAAATCTACATTATCTTACAAAAACATTTTTTAAAAATTTGCTAGGT GGGGGGGGGGGGGGEGGEGGGGGGGGGFGGGGGGGGGGGGGEGFFGGGGGGGFGGFGGGGEGGGGGGGGGGGEGEFFGGGFEGGGGGFGCGGGFBGGGBG@ NM:i:4 MD:Z:6G34G6C5C14 AS:i:49 XS:i:0 SA:Z:chr7,101141091,+,66S35M,60,0;

20692128 353 chr7 101141091 60 66H35M = 101141242 252 ATCTTACAAAAACATTTTTTAAAAATTTGCTAGGT GGGGGGEGEFFGGGFEGGGGGFGCGGGFBGGGBG@ NM:i:0 MD:Z:35 AS:i:35 XS:i:23 SA:Z:gi|224020395|ref|NC_001664.2|,21417,+,69M32S,60,4;

20692128 145 chr7 101141242 60 101M gi|224020395|ref|NC_001664.2| 21417 0 GCAACAGAGCGAGACCCTATATTCATGAGTGTTGCAATGAGCCAAGTAGTGGAGGTTGGCTTTTGAAGGCAGAAAAGGACTGAGAAAAGCTAACACAGAGA FEGCGGGGGCGEFCDEEEEGGGGGGGGGGGGGGGEGGGGGGFGGGEGGG

```
```
TACATCTTCTCCCTCTCTCACGACACAAGAATTAGTCACATAGGGATGTTCTCGTAAATCTACATTATCTTACAAAAACATTTTTTAAAAATTTGCTAGGT(R1:101bp中的69bp 69M32S)

ATCTTACAAAAACATTTTTTAAAAATTTGCTAGGT(R1:101bp中的35bp 66H35M)


GCAACAGAGCGAGACCCTATATTCATGAGTGTTGCAATGAGCCAAGTAGTGGAGGTTGGCTTTTGAAGGCAGAAAAGGACTGAGAAAAGCTAACACAGAGA(R2:101bp中的101bp 101M)
```
+ 当同一条reads比对到不同chr时（嵌合reads），会以hard-clip显示。比如上面的例子，R1分别比到了viral基因组和chr7上（前面69bp比到viral_genome，后面35比到chr7），R2比到了chr7。
+ 比对到一个位置的情况下，会以soft-clip显示。
+ ~~嵌合比对的reads，有一条是最优的read，因为我们map的时候设置了-M参数，因此认为较短的split的reads断定为优，这里是的62 clip 的hit断定为优。因此65个比对不上的显示为softclip，而另外一个hit，79 clip显示为hard clip，序列中不显示，并且存入0x800(supplementary alignment flag)~~
+ 为什么69M32S对应的是66H35M呢，理论上应该是69H32M才对，这是因为这里两个比对有三个碱基的overlap，所以前面有66+3个match，后面有32+3个match
+ 这种嵌合比对的reads含有SA tag


**5.** **supplementary alignment**
**6.** **secondary alignment**
Clipped alignment
Spliced alignment
Multi-part alignment
Padded alignment

**.** **Multiple mapping**
**6.** **representative**
+
**9.** **linear alignment**



![](/assets/sam01.jpg)
Example of extended CIGAR and the pileup output.

+ (a) Alignments of one pair of reads and three single-end reads.

+ (b) The corresponding SAM file. The ‘@SQ’ line in the header section gives the order of reference sequences. Notably, r001 is the name of a read pair. According to FLAG 163 (=1 + 2 + 32 + 128), the read mapped to position 7 is the second read in the pair (128) and regarded as properly paired (1 + 2); its mate is mapped to 37 on the reverse strand (32). Read r002 has three soft-clipped (unaligned) bases. The coordinate shown in SAM is the position of the first aligned base. The CIGAR string for this alignment contains a P (padding) operation which correctly aligns the inserted sequences. Padding operations can be absent when an aligner does not support multiple sequence alignment. The last six bases of read r003 map to position 9, and the first five to position 29 on the reverse strand. The hard clipping operation H indicates that the clipped sequence is not present in the sequence field. The NM tag gives the number of mismatches. Read r004 is aligned across an intron, indicated by the N operation.

+ (c) Simplified pileup output by SAMtools. Each line consists of reference name, sorted coordinate, reference base, the number of reads covering the position and read bases. In the fifth field, a dot or a comma denotes a base identical to the reference; a dot or a capital letter denotes a base from a read mapped on the forward strand, while a comma or a lowercase letter on the reverse strand.

clipped alignment因为着在比对过程中，并没有用到全部的read的序列，read两段的序列被截取了（clip or trim）。如下表示，即为clip alignment。

Alignment:
Read: ACGGTTGCGTTAA-TCCGCCACG
| ||||||||| ||||||
Reference: TAACTTGCGTTAAATCCGCCTGG

与clipped alignment对应的是spliced alignment，即read的中间没有比对到而两段比对上了。对应的表示如下：

Alignment:
Read: ACGGTTGCGTTAAGCTCATCCGCCACG
| ||||||||||||| |||||||||
Reference: ACGGTTGCGTTAA…..TCCGCCACG

clip alignment对应的CIGAR表示有两种S （soft clip） 和H （hard clip）。
BWA提到If the read has a chimeric alignment, the paired or the top hit uses soft clipping and is marked with neither 0x800 nor 0x100 bits. All the other hits part of the chimeric alignment will use hard clipping and be marked with 0x800 if option “-M” is not in use, or marked with 0x100 otherwise.

即如果发现嵌合比对，最好的比对top hit标记为soft clipping，其余的则标记为hard clipping。

如果是hard clip，则截取的部分不会在SAM文件对应的read中出现 (clipped sequences not present in SEQ)，如果是soft clip (clipped sequences present in SEQ)，则会出现。

You'll note that the soft-clipped region isn't used in the `samtools depth/ samtools mpileup` # https://bioinformatics.stackexchange.com/questions/157/are-soft-clipped-bases-used-for-variant-calling-in-samtools-bcftools

#### Ref_Info
https://www.biostars.org/p/109333/
https://davetang.org/wiki/tiki-index.php?page=SAM
https://www.cnblogs.com/leezx/p/6105646.html
https://www.cnblogs.com/ywliao/archive/2017/09/02/7444438.html

## Bwa Mem -M Option
```
-M mark shorter split hits as secondary (for Picard/GATK compatibility)
```
without -M, a split read is flagged as 2048 ( supplementary alignment ) ;
with option -M it is flagged as a duplicate flag=256 ( not primary alignment ): will be ignored by most 'old' tools.

using option -M it says the read to be secondary alignment(not primary alignment) whereas without -M option it gives as supplementary alignment(2048).

## 深入理解bam
How samtools calculate error rate：
samtools adds up the number of mismatches (from the NM auxiliary tag) and divides that by the number of aligned bases. Soft and hard-clipped bases wouldn't be included, since they aren't aligned.


#### Ref_Info
https://www.biostars.org/p/97323/
https://www.biostars.org/p/116201/



#### Ref_Info
http://mp.weixin.qq.com/s/WG0KLWPCzTyKheUMRbJlfA

## TOdolist
https://bioinf.comav.upv.es/courses/sequence_analysis/mapping.html

http://tiramisutes.github.io/2016/03/20/Samtools.html

![image](https://raw.githubusercontent.com/xiucz/pics/master/26AD9DC95EB35B8A201EA33440FBD96E.png)

#
http://genomespot.blogspot.com/2015/03/sambamba-vs-samtools.html

```
In fact, it is implemented in that you can use /dev/stdin and /dev/stdout, it's just that - is not currently recognized as a special filename.
I have an idea of adding -F option to other tools, at least in this particular case this would also help to prevent users from forgetting about setting --compression-level=0 when piping.
```

## mpileup
```
samtools mpileup -f [ref_fasta] [bam] -r [chr:start-end] > mpileupfile
```
在此列上，
+ “.”表示与正链上的参考碱基匹配，
+ “,”表示与负链上的参考碱基匹配，
+ “>”和“<”表示跳过参考基因，
+ “ACGTN”表示正链上的错配，
+ “acgtn”表示负链上的错配。
+ 此模式“\\+[0-9]+[ACGTNacgtn]+”表示在此位点至下一个位点之间与参考基因组对应位点相比，多了一段插入碱基，插入长度由模式中的整数表示。
+ 与此类似，“\\-[0-9]+[ACGTNacgtn]+”表示缺失，缺失的碱基使用“*”表示。
+ 同时，“^”表示reads的开始，
+ “$”表示reads的结束。
+ 在“^”后的字符的ASCII码值减去33表示比对质量值。
+ The “^]” signifies a base that was at the start of a read and the “$” signifies a base that was at the end of a read. 
+ 

For a biallelic site, the PL has three numbers, The first one is the probability that the site is homozgyous reference, the second is the probability that the sample is heterzygous, the third that it is homozygous for the alternate letter. The higher the number, the less likely it is that your sample is that genotype. So if your PL is 483,0,532 the software is quite sure that your sample is not homozygous reference or homozygous alternate, it’s heterozygous.


# 
## samtools和picard的排序问题
samtools和picard都有对SAM/BAM文件进行排序的功能，一般都是基于坐标排序（还提供了-n选项来设定用reads名进行排序），先是对chromosome/contig进行排序，再在chromosome/contig内部基于start-site从小到大排序，对startsite排序很好理解，可是对chromosome/contig排序的时候是基于什么标准呢？

基于你提供的ref.fa文件中的chromosome/contig的顺序。
