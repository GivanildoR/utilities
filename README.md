# utilities for the genomic polyploid data analysis 🥔  
Just to support the script organization  
  
`module load bcftools` 
  
### Checking the total of SNPs in a vcf file  
`bcftools view -H USP_191101_RAW_SNPs.vcf | wc -l`
  
### Changing/modifying/renaming the sample names in the vcf file    
Go to [Renaming the samples in the vcf file](https://github.com/GivanildoR/tutorial_polyploid/blob/main/README.md)

### Now we can check the new names  
`bcftools query -l tetraploid_lmh_renamed.vcf`

### Converting a vcf file to another one with different ploidy easily using bcftools  
`bcftools +fixploidy USP_191101_RAW_SNPs_renamed.vcf -- -f 4 > tetraploid.vcf`  

### We can check how many SNPs we have (it didn't change)  
`bcftools view -H tetraploid.vcf | wc -l ` 

### Filtering using bcftools  
`bcftools filter -i 'QUAL>10 && FORMAT/DP>40 && AF>0.01 && F_MISSING<0.1' -o tetraploid_filtered.vcf tetraploid.vcf`  
QUAL>10: Filters out variants with a quality score less than or equal to 10.  
FORMAT/DP>40: Filters out variants where the depth of coverage is less than or equal to 40.  
AF>0.05: Filters out variants with an allele frequency less than or equal to 0.05.  
F_MISSING<0.1: Filters out variants where the fraction of missing genotypes is greater than or equal to 10%.  

### Filtering by bi-allelic SNPs  
`bcftools view -m2 -M2 -v snps -o tetraploid_lgc_filtered_chr_renamed_recod_bi.vcf tetraploid_lgc_filtered_chr_renamed_recod.vcf`  

### Filtering by non-indels  
`bcftools view -v snps tetraploid_lgc_filtered_chr_renamed_recod_bi.vcf.gz -o out_snps.vcf`  

### We can check the chromosome that remained  
`awk '!/^#/ {print $1}' tetraploid_filtered.vcf | sort -u`  

### We can check how many SNPs we have (it changed)  
`bcftools view -H tetraploid_filtered.vcf | wc -l `

### For the next analysis we don't need the others chromosome, so we can remove them  
`$ bgzip -c tetraploid_filtered.vcf > tetraploid_filtered.vcf.gz   
 $ bcftools index tetraploid_filtered.vcf.gz   
 $ bcftools view -r chr01,chr02,chr03,chr04,chr05,chr06,chr07,chr08,chr09,chr10,chr11,chr12 tetraploid_filtered.vcf.gz -o tetraploid_filtered_chr.vcf`  

### We can transform the  tetraploid_filtered_chr.vcf to a diploid format to use in packages that don't support the poly format  
`bcftools +fixploidy tetraploid_filtered_chr.vcf -- -f 2 > diploid_filtered_chr.vcf`  

### Calculating the pi value for each SNP  
`vcftools --vcf your_file.vcf --site-pi --out output`  


