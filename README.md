# Reannotate_SUMSTATS

Pipeline to Annotate SUMSTATS:

1. When you download publically available SUMSTATS file(s) make sure to record the study name, version/date of download, and genome built in the file name.

2. LiftOver this file to hg38 if needed. 

3. Reannotate this file so it will have the SNP ids have the same annotation of our hg38 VCF files.

4. Use these new annotations to merge accross datasets - make sure they are all the same!


## Example of a pipeline:

### First make a bed file out of the sumstats - These fields will change according to each file, be careful! 


### Then liftOver
echo "hg38_pos" > /secondary/projects/bras/Catarina/PRS_analysis/200k_Exomes/genetic_cauc/data/FEB/AD_sumstats_Jansenetal_2019sept.5e8.bed.hg38
CrossMap.py bed /primary/projects/bras/software/liftOver/hg19ToHg38.over.chain.gz /secondary/projects/bras/Catarina/PRS_analysis/200k_Exomes/genetic_cauc/data/FEB/AD_sumstats_Jansenetal_2019sept.5e8.bed | cut -f5,6 | sed 's/\t/:/g' >> /secondary/projects/bras/Catarina/PRS_analysis/200k_Exomes/genetic_cauc/data/FEB/AD_sumstats_Jansenetal_2019sept.5e8.bed.hg38


### Then paste it to original sumstats
paste <(less -S /secondary/projects/bras/Catarina/PRS_analysis/200k_Exomes/genetic_cauc/data/FEB/AD_sumstats_Jansenetal_2019sept.5e8.txt.gz) /secondary/projects/bras/Catarina/PRS_analysis/200k_Exomes/genetic_cauc/data/FEB/AD_sumstats_Jansenetal_2019sept.5e8.bed.hg38 | bgzip > /secondary/projects/bras/Catarina/PRS_analysis/200k_Exomes/genetic_cauc/data/FEB/AD_sumstats_Jansenetal_2019sept.5e8.bed.hg38.gz


### Make a pvar style file out of this:
echo "#CHROM POS ID REF ALT" | sed 's/ /\t/g' > /secondary/projects/bras/Catarina/PRS_analysis/200k_Exomes/genetic_cauc/data/FEB/AD_sumstats_Jansenetal_2019sept.5e8.pvar

paste <(less -S /secondary/projects/bras/Catarina/PRS_analysis/200k_Exomes/genetic_cauc/data/FEB/AD_sumstats_Jansenetal_2019sept.5e8.bed.hg38.gz | cut -f2) <(less -S /secondary/projects/bras/Catarina/PRS_analysis/200k_Exomes/genetic_cauc/data/FEB/AD_sumstats_Jansenetal_2019sept.5e8.bed.hg38.gz | cut -f15 | cut -f2 -d":") <(less -S /secondary/projects/bras/Catarina/PRS_analysis/200k_Exomes/genetic_cauc/data/FEB/AD_sumstats_Jansenetal_2019sept.5e8.bed.hg38.gz | cut -f15) <(less -S /secondary/projects/bras/Catarina/PRS_analysis/200k_Exomes/genetic_cauc/data/FEB/AD_sumstats_Jansenetal_2019sept.5e8.bed.hg38.gz | cut -f5) <(less -S /secondary/projects/bras/Catarina/PRS_analysis/200k_Exomes/genetic_cauc/data/FEB/AD_sumstats_Jansenetal_2019sept.5e8.bed.hg38.gz | cut -f4) | tail -n +2 >> /secondary/projects/bras/Catarina/PRS_analysis/200k_Exomes/genetic_cauc/data/FEB/AD_sumstats_Jansenetal_2019sept.5e8.pvar


### annotate:
/primary/projects/bras/software/annotate_pvar/annotate_pvar_with_chr.py --pvar /secondary/projects/bras/Catarina/PRS_analysis/200k_Exomes/genetic_cauc/data/FEB/AD_sumstats_Jansenetal_2019sept.5e8.pvar --dbsnp /secondary/projects/bras/reference_datasets/dbSNP/dbSNP_151_GRCh38p7_final.vcf.gz --out /secondary/projects/bras/Catarina/PRS_analysis/200k_Exomes/genetic_cauc/data/FEB/AD_sumstats_Jansenetal_2019sept.5e8.ann.pvar


### Remove extra header with nano ###### need to improve this!!
nano /secondary/projects/bras/Catarina/PRS_analysis/200k_Exomes/genetic_cauc/data/FEB/AD_sumstats_Jansenetal_2019sept.5e8.ann.pvar


### Change header:
sed -i '1s/ID/dbSNP_v151_hg38/g' /secondary/projects/bras/Catarina/PRS_analysis/200k_Exomes/genetic_cauc/data/FEB/AD_sumstats_Jansenetal_2019sept.5e8.ann.pvar


### Paste everything together:
paste <(less -S /secondary/projects/bras/Catarina/PRS_analysis/200k_Exomes/genetic_cauc/data/FEB/AD_sumstats_Jansenetal_2019sept.5e8.bed.hg38.gz) <( cut -f3 /secondary/projects/bras/Catarina/PRS_analysis/200k_Exomes/genetic_cauc/data/FEB/AD_sumstats_Jansenetal_2019sept.5e8.ann.pvar) | bgzip > /secondary/projects/bras/Catarina/PRS_analysis/200k_Exomes/genetic_cauc/data/FEB/AD_sumstats_Jansenetal_2019sept.5e8.hg38.ann.txt.gz


### Ready to use in downstream analysis! - Use last column annotated!
