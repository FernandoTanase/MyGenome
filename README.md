## MyGenome
University of Kentucky, Genome Sequencing.

##(4) Upload RAW Data


##(5)Trim Sequence Reads
-Use command line tools to count sequence reads in the forward (_1.fq.gz) and reverse (_2.fq.gz) directions
  7364314
-Assess sequence quality using FASTQC: fastqc HD1_1.fq.gz  HD1_2.fq.gz
-Download adaptors.fasta
-Trimmomatic: java -jar trimmomatic-0.38.jar PE -threads 2 -phred33 -trimlog Br80_errorlog.txt HD1_1.fastq HD1_2.fastq HD1_1_paired.fastq HD1_1_unp
aired.fastq HD1_2_paired.fastq HD1_2_unpaired.fastq ILLUMINACLIP:adaptors.fasta:2:30:10 SLIDINGWINDOW:20:20 MINLEN:150
*Error: Unable to access jarfile trimmomatic-0.38.jar*......
