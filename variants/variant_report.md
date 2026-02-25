# Session 5: Mapping and variant calling
## 5.1)
## Exercise 1:
### Note that there are 2 FASTQ files with the same name but different numeric suffix, why?
There are two FASTQ files because the sequencing was performed using paired-end sequencing.
The file ending with _1.fastq.gz contains the forward reads (read 1), while the file ending with _2.fastq.gz contains the reverse reads (read 2) from the same DNA fragments.
Each pair of reads corresponds to opposite ends of the same fragment, which improves alignment accuracy and variant detection.

### Check first SAMEA2569438.chr10_1.fastq.gz and then SAMEA2569438.chr10_2.fastq.gz, can you spot * the difference? Do you recognize the typical FASTQ format of these files?
When comparing SAMEA2569438.chr10_1.fastq.gz and SAMEA2569438.chr10_2.fastq.gz, the main difference is that they contain reads from opposite ends of the same DNA fragments (read 1 and read 2).
The read identifiers are similar but indicate whether the read is the first or second in the pair.
Both files follow the standard FASTQ format, where each sequencing read is described using four lines: a header line, the nucleotide sequence, a plus sign line, and a line containing the quality scores.

### Are read starts and ends similar in terms of error rate?
We can clearly observe in the figure below that the error rate is low at the beginning of the reads and increases toward the end of the reads. The quality scores are high at the start and gradually decrease as the read position increases, indicating a higher error rate near the end of the reads. This pattern is typical of Illumina sequencing data.

## Exercise 2:
### Compare the different file sizes for each of the alignment files generated in the previous section (formats SAM, BAM and CRAM).
The SAM file is the largest (91 MB) because it is a plain text format and is not compressed. The BAM file is smaller (32 MB) since it is the binary and compressed version of the SAM format. The CRAM file is the smallest (13 MB) because it uses reference-based compression, storing only the differences between the reads and the reference genome.

## Exercise 3:
Using the output of samtools mpileup, we estimated the sequencing depth across chromosome 10 and observed that approximately 0.22325% of the chromosome positions have a read depth greater than 100

## Exercise 4:
Following the indicated steps, the reference genome, gene annotation, and alignment files were loaded into IGV. We then zoomed in to the genomic region chr10:9,768,000–9,784,000 to visualize the aligned reads. The mapped reads and gene models in this region can be observed in the figure below.

## Exercise 5:
### Take a look to INDEL variant at 10:9,058,200–9,058,229. What are the reference and alternative alleles? Is this position heterozygous in your mapped sample?
By inspecting the filtered VCF in IGV at position chr10:9,058,210, an INDEL variant is observed.  
The reference allele is CAA, and the alternative allele is CAAA, corresponding to an insertion of one adenine base.
According to the genotype information (AC = 2, AF = 1), this variant is homozygous for the alternative allele (1/1) in the mapped sample.

### Check the SNPs at 10:9,059,325-9,059,426. Are they all similar in terms of read dpeth (DP)?
No, the SNPs in the region 10:9,059,325–9,059,426 do not all have similar read depth. By inspecting the SNPs in IGV, different DP values can be observed for different positions, indicating variability in sequencing coverage across this region.

### Examining the aligned reads supporting the SNPs at 10:10,000,166-10,000,226 by loading the BAM and index files. Do any of these fall into a gene model? Save the resulting image.
The figure below shows that no gene model is present in the genomic region 10:10,000,166–10:10,000,226. By loading the BAM file together with the gene annotation track in IGV and examining the region 10:10,000,166–10:10,000,226, we observe that the SNPs are supported by aligned reads but do not overlap with any annotated gene model. Therefore, none of the SNPs in this region fall within a gene. The visualization of this region is shown in the figure below.

## 5.2)
## Problems faced
During the analysis, several minor issues were encountered, mainly related to the Docker environment. Some commands initially failed due to incorrect working directories or because the input. 
