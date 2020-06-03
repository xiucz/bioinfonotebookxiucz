## 方法概述
Manta将SV和indel发现过程分为两个主要步骤：
（1）扫描基因组以找到SV相关区域;（2）在这些区域中发现的SV的分析，评分和输出

Build breakend association graph:

扫描整个基因组以发现可能的SV和大的插入缺失的证据。该证据被列举成具有连接基因组的所有区域的边的图，所述区域具有可能的breakend关联。

Analyze graph edges to find SVs:

分析单个图形边缘或高度连接的边缘组，以发现和评分与边缘相关联的SV。

