[toc]
是一个包管理器，mini是的安装包/工具，ana是一个的科学python的发行版。

## 
```
 --version
 update 
 create -n myenv scipy=0.15.0 #创建一个含有指定版本软件的环境
 install -n myenv bwa  #安装bwa到环境myenv中 也可以通过-c指定通过某个channel安装
 list #查看当前环境中安装的软件
 list -n myenv ＃查看某个环境中安装的包
 list -n myenv scipy ＃查看myenv环境中是否安装软件scipy
 remove --name myenv --all  #移除一个环境 --name／-n
 env remove --name myenv    #效果同上
 info --envs  #确认某个环境被删除，如果能在显示的环境list中看到说明没被删除，当前环境用"*"表示
 env list #同上

 list --explicit #查看当前环境的安装包的具体list
 list --explicit > spec-file.txt #导出具体list
 create --name myenv --file spec-file.txt #通过安装包的具体list在其他机器（最好系统相同）上重建环境
 install --name oldmyenv --file spec-file.txt #将安装包list中的软件装到oldmyenv环境中

 search numpy #差找某个包
 update -n myenv numpy #更新myenv环境中的某个包
 remove -n myenv numpy #删除myenv环境中的某个包

 update  #更新自身
```

## Pin versions with yml file
 env create -n pcgr --file _environment.yml

```
name: pcgr

channels:
  - bio
  - -forge
  - defaults
  - ana

dependencies:
  #
  # PCGR: python
  - python>=3.6
  - pip
  - numpy
  - cython
```
    
## HTTPError: HTTP 000 CONNECTION FAILED for url

```
 config --set show_channel_urls yes
 update ana-navigator
 config --set ssl_verify no
```
==>目前我使用的镜像:
```
channels:
  - https://mirrors.tuna.tsinghua.edu.cn/ana/cloud/msys2/
  - https://mirrors.tuna.tsinghua.edu.cn/ana/pkgs/free/
  - https://mirrors.ustc.edu.cn/ana/pkgs/r/
#  - https://mirrors.ustc.edu.cn/ana/pkgs/free/
  - https://mirrors.tuna.tsinghua.edu.cn/ana/cloud/bio/
  - bio
  - r
#  - https://mirrors.tuna.tsinghua.edu.cn/ana/cloud/-forge/
#  - defaults
#  - -forge
ssl_verify: false
show_channel_urls: true
```

+ 在写YAML的时候主要把你的channel写上去, 并且一定要把default放到最后.
+ 对于Python和R开发的工具,请尽量创建的新的环境进行管理.

## 使用yaml文件
https://github.com/SciLifeLab/Sarek/blob/master/docs/.md

## 镜像问题

```
二、使用官方镜像

2.1 设置官方镜像

目前，最佳解决办法是删除所有第三方镜像链接，恢复到默认设置，命令如下：

 config --remove-key channels
 config --show # 查看的配置，确认channels
增加r、-forge、bio的channels：

 config --add channels r # R软件包
 config --add channels -forge # 社区维护的不在默认通道中的软件
 config --add channels bio # 生物信息学类工具
查看channels是否添加成功：

 config --get channels
2.2 bio
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

### 如何通过pip将packages安装到环境中
使用环境中的pip安装
```
pip install --upgrade --force-reinstall <package>
```