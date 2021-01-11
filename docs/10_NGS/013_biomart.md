[TOC]

# About
+ mart: is and object of class Mart, which is created by the useMart function.
  + host: The URI to host the service
  + biomart: Marts of the different versions
  dataset: The data for different organisms
+ filters: A vector of filters that one will use as input to the query
+ values: A vector of values for the filters. In case multiple filters are in use, the values argument requires a list of values where each position in the list   corresponds to the position of the filters in the filters argument
+ attributes: A vector of attributes that one wants to retrieve (= the output of the query)

# 1. Useage
```
source("http://bioconductor.org/biocLite.R")
chooseCRANmirror()
chooseBioCmirror()
biocLite("biomaRt")
```

```
library(biomaRt)
listEnsembl()
#listEnsembl(version=93)
```
## 2. 
### 2.1 marts
```
marts <- listMarts(host="www.ensembl.org")
```
### 2.2 datasets
```
ensembl <- useMart(host="www.ensembl.org",biomart="ENSEMBL_MART_ENSEMBL")
listDatasets(ensembl)
listFilters(ensembl)
listAttributes(ensembl) #获取属性
attributePages(ensembl)
listDatasets(ensembl)
```

### 2.3 filters & atrributes
```
ensembl <- useMart(host="www.ensembl.org", biomart="ENSEMBL_MART_ENSEMBL", dataset="hsapiens_gene_ensembl")

filters <- listFilters(ensembl)
grep(pattern="refseq", x=filters$description, ignore.case=TRUE)
grep(pattern="ucsc", x=filters$description, ignore.case=TRUE)


attributes <- listAttributes(ensembl)
grep(pattern="entrez", x=attributes$description, ignore.case=TRUE)
attributes[grep(pattern="entrez", x=attributes$description, ignore.case=TRUE), ]
```


# 2. 
## 2.1. 获取HGMD
```
library(biomaRt)

HGMD_snp = useEnsembl(biomart="snp", dataset="hsapiens_snp", version=93)

HGMD_result <- getBM(attributes=c('chr_name', 'chrom_start','chrom_end','refsnp_id','associated_gene','allele','phenotype_description'), filters = 'variation_source', values ="HGMD-PUBLIC", mart = HGMD_snp)
```
## 2.2.
```
my_chr <- c(1:23, "M", "X", "Y")
my_refseq_mrna <- getBM(mart=ensembl, 
                        filters="chromosome_name", values=my_chr, 
                        attributes="refseq_mrna")
my_entrez_gene <- getBM(mart = ensembl,
                        filters = 'chromosome_name', values = my_chr,
                        attributes = 'entrezgene')
 
my_ucsc_gene <- getBM(mart = ensembl,
                   filters = 'chromosome_name', values = my_chr,
                   attributes = 'ucsc')
 
my_ensembl_gene_id <- getBM(mart = ensembl,
                 filters = 'chromosome_name', values = my_chr,
                 attributes = 'ensembl_gene_id')

my_annotation <- getBM(mart = ensembl,
                       filters = 'chromosome_name', values = my_chr,
                       attributes = c('ucsc', 'ensembl_gene_id', 'refseq_mrna', 'entrezgene'))
mark_na <- function(x, ...){
               ret <- sapply(list(...), is.na)
               ret <- gsub(pattern=FALSE, replacement=x, x=ret)
               ret <- gsub(pattern=TRUE, replacement=NA, x=ret)
}

my_venn <- my_annotation
for (i in 1:dim(my_annotation)[1]){
         my_venn[i,] <- mark_na(row.names(my_annotation)[i], my_annotation[i,])
}

my_venn_ucsc <- as.vector(na.omit(my_venn[, 1]))
my_venn_ensembl <- as.vector(na.omit(my_venn[, 2]))
my_venn_refseq <- as.vector(na.omit(my_venn[, 3]))
my_venn_entrez <- as.vector(na.omit(my_venn[, 4]))
library(gplots)
VennList <- list(UCSC = my_venn_ucsc,
               Ensembl = my_venn_ensembl,
               RefSeq = my_venn_refseq,
               Entrez = my_venn_entrez)
venn(VennList)
```


## 2.3 得到hg19/grch37的坐标
```
grch37 = useMart(biomart="ENSEMBL_MART_ENSEMBL", host="grch37.ensembl.org", path="/biomart/martservice", dataset="hsapiens_gene_ensembl")
```

```
ensembl_75 = useMart(biomart="ENSEMBL_MART_ENSEMBL", host="feb2014.archive.ensembl.org", path="/biomart/martservice", dataset="hsapiens_gene_ensembl")
```

## 2.4 获取rs号 对应的坐标位点
```
library(biomaRt)
grch37_snp = useMart(biomart="ENSEMBL_MART_SNP", host="grch37.ensembl.org", path="/biomart/martservice", dataset="hsapiens_snp")
snp_ids = c("rs1149222", "rs4148808")
snp_ids = read.csv("rs.txt", header = T, sep = "\t")
snp_attributes = c("refsnp_id", "chromosome_name", "start_position") #'ensembl_gene_stable_id'
snp_locations = getBM(attributes=snp_attributes, filters="snp_filter", values=snp_ids, mart=grch37_snp)
final_o = merge(snp_locations, snp_ids, by.y = "Location", by.x = "refsnp_id", all.x = TRUE)
write.table(final_o, "final_o.xls", sep = "\t", quote = F, row.names = F)
```

#### 
https://bioinformatics.stackexchange.com/questions/2503/how-to-get-a-list-of-genes-corresponding-to-the-list-of-snps-rs-ids


#### Ref_Info
https://rstudio-pubs-static.s3.amazonaws.com/211762_87895871d3644f339317847d9820959a.html





## 
https://www.biostars.org/p/136775/

## 速度慢
```
library(httr)
library(jsonlite)

chr <- 8
s1 <- seq(143262170, 144261715, by = 10000)
s2 <- c(s1[-1]-1, 144261715)
regions <- matrix(c(rep(chr, length(s1)), s1, s2), ncol = 3)

get_snps_via_rest <- function(values) {
  server <- "https://grch37.rest.ensembl.org"
  ext <- paste0("/overlap/region/human/", values[1], ":", values[2], "-", values[3], "?feature=variation;")
  r <- GET(paste(server, ext, sep = ""), content_type("application/json"))
  fromJSON(content(r, as = "text", encoding = "UTF-8"))
}

res_list2 <- apply(regions, 1, get_snps_via_rest)
snp_id8 <- do.call(rbind, res_list2)
```