
```
wget -qO- https://ftp.ncbi.nih.gov/snp/organisms/human_9606_b151_GRCh37p13/VCF/00-All.vcf.gz \
   | gunzip -c \
   | vcf2bed --sort-tmpdir=${PWD} - \
   | awk -v FS="\t" -v OFS="\t" '{ print $4, $1, $2, $3 }' \
   | sort -k1,1 \
   > hg19.dbSNPAllV151.sortedByName.txt
```



## 根据rs号获取对应的信息
### 
```
https://www.ncbi.nlm.nih.gov/snp/?term=rs140337953+or+rs116440577+or+rs150021059
```

```
#http://codextechnicanum.blogspot.com/2017/07/querying-ncbi-dbsnp-for-rsid-mergers_23.html
#
from Bio import Entrez
import sys

Entrez.email = 'yourEmail@institution.com'

handle = Entrez.efetch(db="snp", id="rs140337953, rs116440577, rs150021059", rettype = 'chr', retmode = 'text')
sys.stdout.write(handle.read())
handle.close()
```

## 根据gene 获取rs号
```
library(rentrez)
# for converting gene name -> gene id
gene_search <- entrez_search(db="gene", term="(PTEN[Gene Name]) AND Homo sapiens[Organism]", retmax=1)
geneId <- gene_search$ids 
# elink function
snp_links <- entrez_link(dbfrom='gene', id=geneId, db='snp')

# access results with $links
length(snp_links$links$gene_snp)
5779

head(snp_links$links$gene_snp)
'864622690' '864622594' '864622518' '864622451' '864622387' '864622341' 

#多个基因
multi_snp_links <- entrez_link(dbfrom='gene', id=c("5728", "374654"), db='snp', by_id=TRUE)
lapply(multi_snp_links, function(x) head(x$links$gene_snp))
```

### api
```
https://api.ncbi.nlm.nih.gov/variation/v0/#/
```

```
http://mulinlab.tmu.edu.cn/gwas4d
```

```
http://cbportal.org/3dsnp/
```

## 利用annovar 获取rs号信息
```
[zhangbo@mu01 1218]$ #/local_data1/MED/programs/zhangbo/PERL/perl-5.28.2/bin/perl /local_data1/MED/programs/Annotools/annovar_2019Oct24/annovar/table_annovar.pl rs.snp138.txt --outfile rs.snp138.txt.anno --buildver hg19 /local_data1/MED/database/hg19/humandb --otherinfo --remove --protocol refGene --operation g   --nopolish
[zhangbo@mu01 1218]$ #/local_data1/MED/programs/Annotools/annovar_2019Oct24/annovar/convert2annovar.pl  -format rsid  rs.txt   -dbsnpfile /local_data1/MED/database/hg19/humandb/hg19_snp138.txt > rs.snp138.txt
```

## 同一个rs对应不同的NC 和NG
```
https://www.pharmgkb.org/variant/PA166155091

```