```bash
# -I: split index for every ~500G input bases, this number is far more than the reference.
```

## 多重比对
https://github.com/lh3/minimap2/issues/164
+ With this option, `--split-prefix tmp`, minimap2 will not write a unmapped record multiple times.
+ Yes, multi-part is turned on by default. You can increase option -I to 500G (a number larger than the total size of nt) to force minimap2 to generate one index. Depending on -k and -w in use, 4x as much might not be enough.
+ When you have a huge database like "nt", minimap2 will split it into multiple parts and align all queries against each part independently. For most parts, minimap2 will print unmapped records. This is a limitation of minimap2.

### multi-part vs. single-part