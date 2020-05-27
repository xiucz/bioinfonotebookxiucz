github: https://github.com/OpenGene/fastp


处理步骤：
```
1, UMI preprocessing (--umi)
2, global trimming at front (--trim_front)
3, global trimming at tail (--trim_tail)
4, quality pruning at 5' (--cut_front)
5, quality pruning by sliding window (--cut_right)
6, quality pruning at 3' (--cut_tail)
7, trim polyG (--trim_poly_g, enabled by default for NovaSeq/NextSeq data)
8, trim adapter by overlap analysis (enabled by default for PE data)
9, trim adapter by adapter sequence (--adapter_sequence, --adapter_sequence_r2. For PE data, this step is skipped if last step succeeded)
10, trim polyX (--trim_poly_x)
11, trim to max length (---max_len)
```
