官网：https://github.com/google/deepvariant/blob/r1.1/docs/README.md
lifebit: https://github.com/lifebit-ai/DeepVariant

## 安装
### tensorflow 
参考:docs/11_ML/003_tensorflow.md
```
conda create -n tf-cpu tensorflow==1.13.1
```
### docker
```
docker run -it google/deepvariant:1.1.0 /bin/bash
# 交互式1
docker run -itv /local_data1/:/opt/deepvariant/ google/deepvariant:1.1.0 /bin/bash
# 交互式2（推荐）
docker run -itv /local_data1/:/local_data1/ google/deepvariant:1.1.0 /bin/bash
```

image recognition
+ **Make_examples**: The make_example step uses the reference genome and the BAM file in order to produce the piled-up images needed for the prediction. 
+ **Call_variants**:


  