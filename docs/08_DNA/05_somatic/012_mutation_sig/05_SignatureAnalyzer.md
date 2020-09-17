https://software.broadinstitute.org/cancer/cga/msp

SignatureAnalyzer分析突变特征

> SignatureAnalyzer使用一种NMF的贝叶斯转换，通过自动相关性确定技术来推断特征的数量，并为特征配置文件和属性提供高度可解释性和稀疏表示，在数据拟合和模型复杂性之间取得平衡。SignatureAnalyzer采用两步特征提取策略，分别使用SBSs的1536个五核苷酸context、83个indels特征和78个DBS特征。除了单独提取SBS、indel和DBS特征外，本工作还基于所有1697个特性(1536 SBS + 78 DBS +83 indels)执行了一个“复合”特征提取。
