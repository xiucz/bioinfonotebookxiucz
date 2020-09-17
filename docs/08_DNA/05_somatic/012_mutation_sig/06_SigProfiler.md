https://cancer.sanger.ac.uk/cosmic/signatures/sigprofiler.tt

SigProfiler分析突变特征
> SigProfiler包含了两个不同的步骤来识别突变特征，第一步(SigProfilerExtraction)包含一个层次重新提取基于体细胞突变的突变特征和他们的直接序列的context，第二步(SigProfilerAttribution)关注准确估算体细胞突变的数量与每个样本中的每个提取的突变特征的关联。


本工作开发了对每种突变类型的分类。
> 对于SBSs，主要分类由96类组成 (available at https://cancer.sanger.ac.uk/cosmic/signatures/SBS)，6种碱基替代（C>A, C>G, C>T, T>A, T>C 和 T>G）加上lanking 5′和 3′ bases。在一些分析中，考虑突变碱基的flanking bases 5′ 和3’(产生1536类)，或者选择转录基因组区域内的突变，根据突变的嘧啶是否落在转录或未转录的链上(产生192个类别)进行分类。

> 对DBSs分类成78类（available at https://cancer.sanger.ac.uk/cosmic/signatures/DBS）。Indels被划分为缺失或插入，如果是一个碱基，则被划分为C或T，根据它们发生的单核核苷酸重复道的长度。根据indel、repeat和micro同源性的大小，将较长的indel分为出现在重复位点或在缺失边界有重叠的微同源性。

