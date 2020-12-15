## qiime2自己建立分类器 greengene
https://www.omicsclass.com/article/1360

## 
```
# 
https://view.qiime2.org/
qiime tools peek filename.qza
qiime tools export
```
## 安装
https://docs.qiime2.org/2020.8/install/native/


## 流程
http://interact.majorbio.com/article/detail/508

https://forum.qiime2.org/t/fungal-its-analysis-tutorial/7351

## dada2
```
In your own data you should follow this process to determine whether you have enough sequence length to proceed with pairend-end sequences, or if you must proceed with single-end reads due to insufficient read overlap (as we have done above):

Determine the expected amplicon length for your primers
Check the amplicon length and assess the quality of your reads (use the demux.qzv or demux-trimmed.qzv summaries)
Are your (trimmed) reads long enough to overlap? dada2 expects a minimum overlap of 12 nt, so your reads are long enough if: length(forward read) + length(reverse read) ≥ 12 nt.
If your answer to #3 is “definitely not!”, proceed with single-end reads as we have shown here. If your answer to #3 is “maybe?”, give paired-end denoising a shot but pay very close attention to how many reads are being lost after merging (check out the stats file that is output by dada2).

```

```
The trim left option in dada2 can be used to either trim adapters (as you have done in vsearch) or remove low quality portions of your reads which sometimes occur during the first 10-20 bp on the 5’ end. This is not uncommon from Illumina runs. When these low quality tails are present, dada2’s internal filtering system may discard these reads before denoising even occurs. However, if those portions are removed, then those reads can be saved from the filtering step, albeit with slightly shorter lengths.
```

https://forum.qiime2.org/t/its-analysis-does-it-make-sense-to-use-dada2-trunc-len-parameters/11494/5

## tree
https://microbiomejournal.biomedcentral.com/articles/10.1186/s40168-016-0153-6