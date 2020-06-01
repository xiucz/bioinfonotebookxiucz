[TOC]

https://www.hgvs.org/mutnomen/refseq.html

ftp://hgdownload.cse.ucsc.edu/goldenPath/hg38/database/refGene.txt.gz

https://github.com/counsyl/hgvs

http://www.sohu.com/a/158915410_603295

https://www.jianshu.com/p/e7826662b942
## normalisation
http://www.ensembl.info/2018/06/22/cool-stuff-the-vep-can-do-normalisation/

http://asia.ensembl.org/info/docs/tools/vep/script/vep_other.html#refseq

## biocommons_hgvs

https://github.com/biocommons/hgvs

```
# fields of the HGVS name are available as attributes:
#
# hgvs_name.transcript = 'NM_000352.3'
# hgvs_name.kind = 'c'
# hgvs_name.mutation_type = '>'
# hgvs_name.cdna_start = hgvs.CDNACoord(215, -10)
# hgvs_name.cdna_end = hgvs.CDNACoord(215, -10)
# hgvs_name.ref_allele = 'A'
# hgvs_name.alt_allele = 'G'
```

```
#/lustre/Work/zhangbo/software/Anaconda3/envs/python2/bin/python2

import re
import hgvs.parser

def rewrite_hgvs(hvar):
    return re.sub(r"c.(\w)(\d+)(\w)", r"c.\2\1>\3", hvar)

hgvs_format = rewrite_hgvs("NM_001197320.1:c.C281T")

hgvsparser = hgvs.parser.Parser()
var_c = hgvsparser.parse_hgvs_variant(hgvs_format)
str(var_c)

import hgvs.dataproviders.uta
import hgvs.assemblymapper

# initialize the mapper for GRCh37 with splign-based alignments
hdp = hgvs.dataproviders.uta.connect()
am = hgvs.assemblymapper.AssemblyMapper(hdp, assembly_name='GRCh37', alt_aln_method='splign', replace_reference=True)
var_p = am.c_to_p(var_c)

var_p.posedit.uncertain = False
var_p.format(conf={"p_3_letter": False})

# configuration may also be set globally
hgvs.global_config.formatting.p_3_letter = False
str(var_p)
```

## transvar
http://transvar.readthedocs.io/en/latest/index.html

## Mutalyzer
https://mutalyzer.nl

### api
### github
https://genomemedicine.biomedcentral.com/articles/10.1186/gm543

http://andrewjesaitis.com/2017/03/the-state-of-variant-annotation-in-2017/

## Tips
+ 'NM_001079843.2:c.3327C>T'
+ 

https://chengcz.github.io/2018/201806_HumanGene/


https://www.bioinfo-scrounger.com/archives/677/