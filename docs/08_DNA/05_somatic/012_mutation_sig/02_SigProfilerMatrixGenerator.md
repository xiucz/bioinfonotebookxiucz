[toc]

https://osf.io/s93d5/wiki/5.%20Output%20-%20DBS/

```
$ python3
>>from SigProfilerMatrixGenerator.scripts import SigProfilerMatrixGeneratorFunc as matGen
>>matrices = matGen.SigProfilerMatrixGeneratorFunc(project, genome, vcfFiles, exome=False, bed_file=None, chrom_based=False, plot=False, tsb_stat=False, seqInfo=False)
```
## output
### SBS: Single Base Substitution
单碱基取代（SBS）的分类。 SBS的完整分类包括Watson-Crick碱基对中的两个碱基。 为了简化此表示法，可以使用嘌呤或嘧啶碱。 SigProfilerMatrixGenerator将嘧啶分类用作标准
### 

| File             | # of sequences                                                                                                                                                                    |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| test.DBS78.all   | 78 pyrimidine double nucleotide variants                                                                                                                                          |
| test.DBS186.all  | 36 dinucleotide combinations that have only all purines or all pyrimidines x 4 transcriptional bias categories = 144 + 42 classified in the Q transcriptional bias category = 186 |
| test.DBS1248.all | Possible starting nucleotides (4) x 78 x possible ending nucleotides (4)= 1,248 total combinations                                                                                |
| test.DBS2976.all | Possible starting nucleotides (4) x 186 x possible ending nucleotides (4) = 2,976                                                                                                 |

### DBS: Double Base Substitution

DBS-78

### Insertions and Deletions, Indels (ID)
### Transcriptional Strand Bias (TSB)

