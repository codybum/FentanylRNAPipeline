# FentanylRNAPipeline

## Pipline Processes
1. FastQC is used to check fastq quality
2. Reference Index is generated 
3. Alignment is conducted
4. BAM files were processed for read summarization using featureCounts 
5. Processed data were then analyzed for differential expression using DESeq2 in R software. (see attached DESeq.R)

## FastQC
* Action: Generate QC information for each fastq file
* Command: fastqc -o fastqc_results [fastq file path] > fastqc_results/[fastq file id].log
* Example: fastqc -o fastqc_results genomic_download_nicotine_fentanyl/30-841498228/00_fastq/PFC213_R1_001.fastq.gz > fastqc_results/PFC213.log

## Generate Reference Index
* Action: The -runMode genomeGenerate option in the STAR manual directs STAR to generate genome indices. The -genomeDir specifies the path to the directory.
* Command: ./STAR --runThreadN [number of threads] --runMode genomeGenerate --genomeDir [index dst path] --genomeFastaFiles [reference files] --sjdbGTFfile [reference GTF file] 
* Example: ./STAR --runThreadN 40 --runMode genomeGenerate --genomeDir rn_star_index --genomeFastaFiles Rattus_norvegicus.mRatBN7.2.dna.toplevel.fa --sjdbGTFfile Rattus_norvegicus.mRatBN7.2.109.gtf

## STAR Alignment
* Action: Align/generate BAMs for fastq pairs
* Command: ./STAR --runThreadN [number of threads] --readFilesIn [fastq read pair] --genomeDir [reference index] --outSAMtype BAM SortedByCoordinate --outFileNamePrefix [output location] --outSAMunmapped Within --readFilesCommand zcat
* Example: ./STAR --runThreadN 20 --readFilesIn genomic_download_nicotine_fentanyl/30-841498228/00_fastq/PFC213_R1_001.fastq.gz genomic_download_nicotine_fentanyl/30-841498228/00_fastq/PFC213_R2_001.fastq.gz --genomeDir rn_star_index --outSAMtype BAM SortedByCoordinate --outFileNamePrefix fentanyl/Group-1_PFC213 --outSAMunmapped Within --readFilesCommand zcat

## Generate feature counts
* Action: Generate feature counts from BAMs
* Command: ./featureCounts -p --countReadPairs -t exon -g gene_id -a [reference GTF file] -o [output of feature count text file] -C -B -T 40 [location of BAM files] > [log file path] &
* Example: ./featureCounts -p --countReadPairs -t exon -g gene_id -a Rattus_norvegicus.mRatBN7.2.109.gtf -o counts/fentanyl_counts.txt -C -B -T 40 fentanyl/*.bam > fc.log &
