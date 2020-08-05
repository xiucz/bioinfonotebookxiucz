paper:

https://www.nature.com/articles/nbt.2203#Sec11
https://www.nature.com/articles/nbt.2203.pdf?origin=ppub

https://www.jianshu.com/p/468077752689


https://www.genepattern.org/modules/docs/ABSOLUTE/2


----
Absolute quantification of somatic DNA alterations in human cancer
ovarian carcinoma
pervasive
recessive inactivation 隐形失活
tumorigenessusis 癌症发生

Such measurements are straightforward to interpret and, for alterations that are fixed in the cancer cell population, are **simple integer values**.

3155 cancer samples, >= 20 samples per 25 diesease


+ 样本混合物中Cancer细胞比例：$\alpha$ (假设单染色体组monogenomic，即有同源SCNAs)
+ 样本混合物中Normal细胞比例：$1-\alpha$ （染色体倍性为2）
+ 基因组某位点：$x$
+ Cancer细胞中该位点（整型）拷贝数表示为：$q(x)$
+ Cancer细胞平均倍性为：$\tau$，定义为整个基因组上$q(x)$的平均值

样本混合物中位点$x$的平均绝对拷贝数为：

$\alpha q(x) + 2(1-\alpha)  \rightarrow$  Cancer细胞比例 * 位点拷贝数 + 正常细胞比例 * 位点拷贝数2

样本混合物中平均倍性$D$为：

$\alpha \tau + 2(1-\alpha) \rightarrow$ Cancer细胞比例 * Cancer倍性 + 正常细胞比例 * 正常细胞倍性2

因此，位点$x$的相对拷贝数R为：

$R(x) = (\alpha q(x) + 2(1-\alpha)) / D = (\alpha / D) q(x) + (2(1-\alpha) / D)
\rightarrow$ 平均绝对拷贝数 / 平均倍性

因为q(x)取整数值，因此R(x)必然是离散值。最小值为(2(1-\alpha)/D)，发生在纯合性缺失位点，对应正常细胞的DNA比例。

The spacing between values $α/D$ corresponds to the concentration ratio of alleles present at one copy per cancer cell and 0 copies per normal cell.

>>> Notably, if a cancer sample is not strictly clonal, copy-number alteration occurring in substantial subclonal fraction will appear as outliers from this pattern.

## output

https://www.genepattern.org/analyzing-absolute-data