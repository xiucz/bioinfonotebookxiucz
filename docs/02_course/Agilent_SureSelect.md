## 下载
```
> wget -cb https://sequencing.roche.com/content/dam/rochesequence/worldwide/shared-designs/MedExome_design_files.zip
> wget -cb https://sequencing.roche.com/content/dam/rochesequence/worldwide/shared-designs/MedExomePlusMito_design_files.zip
> wget -cb https://sequencing.roche.com/content/dam/rochesequence/worldwide/shared-designs/SeqCapEZ_Exome_v3.0_Design_Annotation_files.zip
> wget -cb https://sequencing.roche.com/content/dam/rochesequence/worldwide/shared-designs/Exome_UTR_Design_Annotation_Files.zip
> wget -cb https://sftp.rch.cm/diagnostics/sequencing/nimblegen_annotations/ez_hgsc_vcrome/VCRome_2.1_design_files.zip
```

## 按染色体分割bed文件
```
chr_par(){
for chr in `seq 1 22` X Y
do
grep "^chr$chr"$'\t' $i >chr/"$name".chr"$chr".bed
grep "^chr$chr"$'\t' $i | awk -F '\t' '{print $1"\t"$2-100"\t"$3+100"\t"$4}' >chr/"$name".chr"$chr".200bp.
done
}
```
