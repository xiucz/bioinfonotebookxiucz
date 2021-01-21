# sambamba
## sambamba vs. picard
[markdup read choice #126](https://github.com/biod/sambamba/issues/126)

https://github.com/biod/sambamba/issues/267

### 内存问题
```
In my case, this appears to be caused by a large --io-buffer-size (but well below my available RAM). In particular, use of --io-buffer-size {4096, 2048} all resulted in this error. However, the error did not occur when this argument is not provided and also did not occur for explicitly-provided smaller values (all of --io-buffer-size {256, 512, 1024} completed without any reported errors).
https://github.com/biod/sambamba/issues/169
```

https://github.com/biod/sambamba/issues/240

# 
https://github.com/statgen/bamUtil

#
https://github.com/GregoryFaust/samblaster

# 
https://gitlab.com/german.tischler/biobambam2



## vs 
https://github.com/FrickTobias/BLR/issues/113

+ From what I know, samblaster and bammarkduplicates can do one-pass duplicate marking, depending on how your file is sorted (the former would work for name-sorted, the latter for coordinate-sorted file).
