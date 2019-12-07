[toc]
conda是一个包管理器，miniconda是conda的安装包/工具，anaconda是一个conda的科学python的发行版。

## 
```
conda --version
conda update conda
#创建一个含有指定版本软件的环境
conda create -n myenv scipy=0.15.0 
conda install -n myenv bwa  #安装bwa到环境myenv中 也可以通过-c指定通过某个channel安装
conda list #查看当前环境中安装的软件
conda list -n myenv ＃查看某个环境中安装的包
conda list -n myenv scipy ＃查看myenv环境中是否安装软件scipy
conda remove --name myenv --all  #移除一个环境 --name／-n
conda env remove --name myenv    #效果同上
conda info --envs  #确认某个环境被删除，如果能在显示的环境list中看到说明没被删除，当前环境用"*"表示
conda env list #同上

conda list --explicit #查看当前环境的安装包的具体list
conda list --explicit > spec-file.txt #导出具体list
conda create --name myenv --file spec-file.txt #通过安装包的具体list在其他机器（最好系统相同）上重建环境
conda install --name oldmyenv --file spec-file.txt #将安装包list中的软件装到oldmyenv环境中

conda search numpy #差找某个包
conda update -n myenv numpy #更新myenv环境中的某个包
conda remove -n myenv numpy #删除myenv环境中的某个包

conda update conda #更新conda自身
```

## Pin versions with yml file
conda env create -n pcgr --file conda_environment.yml

```
name: pcgr

channels:
  - bioconda
  - conda-forge
  - defaults
  - anaconda

dependencies:
  #
  # PCGR: python
  - python>=3.6
  - pip
  - numpy
  - cython
```
    
## CondaHTTPError: HTTP 000 CONNECTION FAILED for url

```
conda config --set show_channel_urls yes
conda update anaconda-navigator
conda config --set ssl_verify no
```
==>目前我使用的镜像:
```
channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/msys2/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
  - https://mirrors.ustc.edu.cn/anaconda/pkgs/r/
#  - https://mirrors.ustc.edu.cn/anaconda/pkgs/free/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda/
  - bioconda
  - r
#  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
#  - defaults
#  - conda-forge
ssl_verify: false
show_channel_urls: true
```

+ 在写YAML的时候主要把你的channel写上去, 并且一定要把default放到最后.
+ 对于Python和R开发的工具,请尽量创建的新的环境进行管理.

## 使用yaml文件
https://github.com/SciLifeLab/Sarek/blob/master/docs/CONDA.md

## 镜像问题

```
二、使用官方镜像

2.1 设置官方镜像

目前，最佳解决办法是删除所有第三方镜像链接，恢复到默认设置，命令如下：

conda config --remove-key channels
conda config --show # 查看conda的配置，确认channels
增加r、conda-forge、bioconda的channels：

conda config --add channels r # R软件包
conda config --add channels conda-forge # Conda社区维护的不在默认通道中的软件
conda config --add channels bioconda # 生物信息学类工具
查看channels是否添加成功：

conda config --get channels
2.2 bioconda
```

##
### 1.jupyter notebook中设置显示最大行和列及浮点数,在head观察行和列时不会省略
```
pd.set_option(‘max_columns’,1000)
pd.set_option(‘max_row’,300)
pd.set_option(‘display.float_format’, lambda x: ‘%.5f’ % x)
```

```
#显示所有列
pd.set_option('display.max_columns', None)
#显示所有行
pd.set_option('display.max_rows', None)
#设置value的显示长度为100，默认为50
pd.set_option('max_colwidth',100)
```

###
https://github.com/mozilla/jupyter-notebook-gist

### 如何通过pip将packages安装到Conda环境中
使用环境中的pip安装
```
pip install --upgrade --force-reinstall <package>
```