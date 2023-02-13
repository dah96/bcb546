#UNIX Assignment

##Data Inspection

###Attributes of `fang_et_al_genotypes`

```
du -h fang_et_al_genotypes.txt
wc -l fang_et_al_genotypes.txt
tail -n +6 fang_et_al_genotypes.txt | awk -F "\t" '{print NF; exit}'
```

By inspecting this file I learned that:

1. fang_et_al_genotypes.txt file size is 10.5M. 
2. There are 2783 lines in fang_et_al_genotypes.txt
3. There are 986 columns in fang_et_al_genotypes.txt

###Attributes of `snp_position.txt`

```
du -h snp_position.txt
wc -l snp_position.txt
tail -n +3 snp_position.txt | awk -F "\t" '{print NF; exit}'
```

By inspecting this file I learned that:

1. snp_position.txt file size is 85.0k.
2. There are 984 lines in snp_position.txt
3. There are 15 columns in snp_position.txt


##Data Processing

###Maize Data

```
cut -f 1,3,4 snp_position.txt > snp_cut.txt
grep -v "SNP_ID" snp_cut.txt | sort -k1,1 snp_cut.txt > snp_cut_sorted.txt
grep -E -w "(Sample_ID|ZMMIL|ZMMLR|ZMMMR)" fang_et_al_genotypes.txt > genotypes_maize.txt
awk -f transpose.awk genotypes_maize.txt > transposed_genotypes_maize.txt
grep -v "Sample_ID" transposed_genotypes_maize.txt | sort -k1,1 > maize_genotypes_sort.txt
join -1 1 -2 1 -t $'\t' snp_cut_sorted.txt maize_genotypes_sort.txt > maize_joined.txt
sed 's/unknown/?/g' maize_joined.txt | sort -k3,3n > maize_q_sorted.txt
awk '$2 ~ /1/' maize_q_sorted.txt > maize_q_chr1.txt
```
Repeat for chr2-10
```
sed 's/unknown/-/g' maize_joined.txt | sort -k 3 -r -n  > maize_-_sorted.txt
awk '$2 ~ /1/' maize_-_sorted.txt > maize_-_chr1.txt
```
Repeat for chr 2-10
```
grep -w "unknown" maize_joined.txt > maize_unknown.txt
grep -w "multiple" maize_joined.txt > maize_multiple.txt
```
The code written here first cuts the desired columns out of snp_position.txt. The header is then removed from the new file and it is then sorted alphabetically by the SNP_ID column. The header and all maize SNPs are then taken out of fang_et_al_genotypes.txt and transposed using the provided transpose.awk script. The header is then removed and the Sample_IDs are then sorted alphabetically to be the same as the SNP_IDs allowing for the 2 files to be joined together. The joined file is then used to produce the desired end files by replacing text as needed, sorting in forward or reverse order of positon, and pulling out each chromosome or unknown/multiple SNP locations.

###Teosinte Data

```
grep -E -w "(Sample_ID|ZMPBA|ZMPIL|ZMPJA)" fang_et_al_genotypes.txt > genotypes_teosinte.txt
awk -f transpose.awk genotypes_teosinte.txt > transposed_genotypes_teosinte.txt
grep -v "Sample_ID" transposed_genotypes_teosinte.txt | sort -k1,1 > teosinte_genotypes_sort.txt
join -1 1 -2 1 -t $'\t' snp_cut_sorted.txt teosinte_genotypes_sort.txt > teosinte_joined.txt
sed 's/unknown/?/g' teosinte_joined.txt | sort -k3,3n > teosinte_q_sorted.txt
awk '$2 ~ /1/' teosinte_q_sorted.txt > teosinte_q_chr1.txt
```
Repeat for chr2-10
```
sed 's/unknown/-/g' teosinte_joined.txt | sort -k 3 -r -n  > teosinte_-_sorted.txt
awk '$2 ~ /1/' teosinte_-_sorted.txt > teosinte_-_chr1.txt
```
Repeat for chr2-10
```
grep -w "unknown" teosinte_joined.txt > teosinte_unknown.txt
grep -w "multiple" teosinte_joined.txt > teosinte_multiple.txt
```
The header and all teosinte SNPs are then taken out of fang_et_al_genotypes.txt and transposed using the provided transpose.awk script. The header is then removed and the Sample_IDs are then sorted alphabetically to be the same as the SNP_IDs allowing for the 2 files to be joined together. The joined file is then used to produce the desired end files by replacing text as needed, sorting in forward or reverse order of positon, and pulling out each chromosome or unknown/multiple SNP locations.
