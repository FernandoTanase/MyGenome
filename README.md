## MyGenome
University of Kentucky, Genome Sequencing.

##(4) Upload RAW Data
-Download "key file" (aspera.openssh) on local machine, then scp to vm.
- TO BE CONTINUED>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

##(5)Trim Sequence Reads
-Use command line tools to count sequence reads in the forward (_1.fq.gz) and reverse (_2.fq.gz) directions
  7364314
-Assess sequence quality using FASTQC: fastqc HD1_1.fq.gz  HD1_2.fq.gz
-Download adaptors.fasta
-Trimmomatic: java -jar /home/fcta222/assembly/trimmomatic-0.38.jar PE -threads 2 -phred33 \
-trimlog Br80_errorlog.txt \
./HD1_1.fq ./HD1_2.fq \
./HD1_1_paired.fq ./HD1_1_unpaired.fq \
./HD1_2_paired.fq ./HD1_2_unpaired.fq \
ILLUMINACLIP:./adaptors.fasta:2:30:10 \
SLIDINGWINDOW:20:20 MINLEN:120

TrimmomaticPE: Started with arguments:
 -threads 2 -phred33 -trimlog Br80_errorlog.txt ./HD1_1.fq ./HD1_2.fq ./HD1_1_paired.fq ./HD1_1_unpaired.fq ./HD1_2_paired.fq ./HD1_2_unpaired.fq ILLUMINACLIP:./adaptors.fasta:2:30:10 SLIDINGWINDOW:20:20 MINLEN:120
Using PrefixPair: 'AGATGTGTATAAGAGACAG' and 'AGATGTGTATAAGAGACAG'
Using Long Clipping Sequence: 'GTCTCGTGGGCTCGGAGATGTGTATAAGAGACAG'
Using Long Clipping Sequence: 'TCGTCGGCAGCGTCAGATGTGTATAAGAGACAG'
Using Long Clipping Sequence: 'GATCGGAAGAGCACACGTCTGAACTCCAGTCACTTAGGCATCTCGTATGC'
Using Long Clipping Sequence: 'CTGTCTCTTATACACATCTCCGAGCCCACGAGAC'
Using Long Clipping Sequence: 'CTGTCTCTTATACACATCTGACGCTGCCGACGA'
ILLUMINACLIP: Using 1 prefix pairs, 5 forward/reverse sequences, 0 forward only sequences, 0 reverse only sequences
Input Read Pairs: 7364314 Both Surviving: 6593963 (89.54%) Forward Only Surviving: 133300 (1.81%) Reverse Only Surviving: 596622 (8.10%) Dropped: 40429 (0.55%)
TrimmomaticPE: Completed successfully

-Assess sequence quality of the trimmed reads (paired only) using FASTQC: fastqc HD1_1_paired.fq HD1_2_paired.fq
-Transfer the .html output files to your local machine using scp and open the file.

##(6) Check your trimmed reads using FASTQC to ensure removal of poor quality "tails" and adaptor contamination
- TO BE CONTINUED>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
