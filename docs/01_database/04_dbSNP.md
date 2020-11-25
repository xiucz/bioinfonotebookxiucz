https://www.jianshu.com/p/3d8600af2928


# 
## 根据rs号获取对应的信息
### 
```
https://www.ncbi.nlm.nih.gov/snp/?term=rs140337953+or+rs116440577+or+rs150021059
```

###
```
#http://codextechnicanum.blogspot.com/2017/07/querying-ncbi-dbsnp-for-rsid-mergers_23.html

from Bio import Entrez
import sys

Entrez.email = 'yourEmail@institution.com'

handle = Entrez.efetch(db="snp", id="rs140337953, rs116440577, rs150021059", rettype = 'chr', retmode = 'text')
sys.stdout.write(handle.read())
handle.close()
```


### api
```
https://api.ncbi.nlm.nih.gov/variation/v0/#/
```