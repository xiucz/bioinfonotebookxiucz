https://lbgi.fr/AnnotSV/

##
https://lbgi.fr/AnnotSV/annotations
1. Genes-based annotations:
2. Annotations with features overlapping the SV:
3. Annotations with features overlapped with the SV:
4. Breakpoints annotations:


### 1. Genes-based annotations:
**目的：**
“基因注释”旨在提供与SV重叠的已知基因的信息，以便列出来自注释良好的RefSeq数据库的基因。
这些注释包括基因的定义和相应的转录本（RefSeq），CoDing序列（CDS）和转录本的长度，SV在基因中的位置（例如«txStart-exon3»）和坐标。 
SV与抄本之间的交集。

header1 | header2
---  |---
Gene name |	genesymbol
NM   | 转录本号
CDS length | SV与CDS的交集长度
tx length |	SV与转录本的交集长度
location | SV在基因中位置 (e.g. « txStart-exon1 », « intron3-exon7 »)
intersectStart |	Start position of the intersection between the SV and the transcript
intersectEnd |	End position of the intersection between the SV and the transcript