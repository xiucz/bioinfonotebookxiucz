## INTRODUCTION

Following Kõljalg et al. (2013), each terminal fungal taxon for which two or more ITS sequences are available is referred to as a species hypothesis (SH). One sequence is chosen to represent each SH; these sequences are called representative sequences (RepS) when chosen automatically by the computer and reference sequences (RefS) when those choices are overridden (or confirmed) by users with expert knowledge of the taxon at hand.

真菌界估计有2.2-3.8百万种的异养真核生物，DNA序列通常用于DNA条形码来探索真菌群落。大约600 bp的ITS是本领域的主要遗传标记，目前国际核苷酸合作序列数据库（International Nucleotide Sequence Databases Collaboration, INSDC）提供超过1,000,000的Sanger测序全长真菌ITS作为参考数据库。但这些序列用于为其它序列分类注释前，需要进行严格筛选和处理注释，因此2003年启动了真菌分子鉴定联合据库UNITE，致力于提供真菌的可重复鉴定，并通过收集和传播从其序列数据中得知的所有真菌的分类、生态和地理元数据，促进真菌学进展。

真菌的宏条形码与大量的OTU一直存在斗争。UNITE定期将序列按几个序列相似性阈值进行聚类，以获得假定物种**（species hypotheses, SHs）**—— 近似物种水平OTU。这些SHs分配有DOI，即使没有完整的名称也可以保持引用的统一性。大家可以下载多序列比对文件、使用blast比对SHs、使用最近基于概率分类的方法PROTAX、或其它序列搜索工具。此外，SHs提供了QIIME、MOTHUR、USEARCH和MICCA等主流软件兼容的数据格式文件 (https://unite.ut.ee/repository.php )。

UNITE支持基于网页的序列数据第三方注释，以反映最近的命名和分类变化，并纠正公共DNA序列中分类注释和其他元数据项的通常次优状态。通过组织针对性注释，例如植物病原真菌或构建真菌组，鼓励参与序列注释工作。所做的任何更改都是通过DOI的共同作者进行，由DataCite（https://www.datacite.org/ ）监视的所有DOI（SHs）。UNITE面向社区的特性鼓励参与，但也包含了处理用户提供的注释审查过程。这一体系与一系列其他质量控制措施一起实施，以保持UNITE项目的高质量标准。

2003年，UNITE首次公开发行。从那时起，随着高通量测序（high-throughput sequencing, HTS）方法和认识到未被描述的“黑暗（dark，末被发现的）”类群渗透到真菌生命树中并可能主导地球的功能性生物多样性，真菌学发生了深远的变化。术语规则和分类原则的变化进一步推动了这一领域。在这篇文章中，我们详细介绍了我们为应对真菌和分子生态群落的技术和概念进步所带来的挑战而联合实施的最新应对方案。

## 下载
```
wget https://files.plutof.ut.ee/public/orig/98/AE/98AE96C6593FC9C52D1C46B96C2D9064291F4DBA625EF189FEC1CCAFCF4A1691.gz

tar xzf 98AE96C6593FC9C52D1C46B96C2D9064291F4DBA625EF189FEC1CCAFCF4A1691.gz

cd sh_qiime_release_04.02.2020/developer/

#Fix formatting errors that prevent importation of the reference sequences into QIIME2. There are white spaces that interfere, and possibly some lower case letters that need to be converted to upper case.
awk '/^>/ {print($0)}; /^[^>]/ {print(toupper($0))}' sh_refs_qiime_ver8_99_04.02.2020_dev.fasta | tr -d ' ' > sh_refs_qiime_ver8_99_04.02.2020_dev_uppercase.fasta

```