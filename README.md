## MyGenome
University of Kentucky, Genome Sequencing.

## Module 5
# (4) Upload raw sequence data to NCBI (SRA)
- Create an SRA submission on the NCBI website.
- Download "key file" (aspera.openssh) on local machine, then scp to vm ('sequences' directory).
- wget the Aspera Connect Software into the 'sequences' directory:  wget https://d3gcli72yxqn2z.cloudfront.net/downloads/connect/latest/bin/ibm-aspera-connect_4.2.13.820_linux_x86_64.tar.gz
- Uncompress the archive: tar zxvf ibm-aspera-connect_4.2.13.820_linux_x86_64.tar.gz
- Install the software: bash ibm-aspera-connect_4.2.13.820_linux_x86_64.sh
- To run the ascp transfer program, we need to tell the shell exactly where to find it. The installation places the software inside a "hidden" directory (.aspera) in your home directory: ls ~/.aspera
- To run the program we need to provide the complete path to it: ~/.aspera/connect/bin/ascp -i ~/sequences/aspera.openssh -QT -l100m -k1 -d ~/myGenome/Po18 subasp@upload.ncbi.nlm.nih.gov:uploads/fernando.tanase_uky.edu_bqluzRru
- Submit screenshot of email confirmation/"BioProject submission (of my sample)" page + enter SRA accession number (SRR prefix) to the metadata sheet.

# (5)Trim Sequence Reads
- Use command line tools to count sequence reads in the forward (_1.fq.gz) and reverse (_2.fq.gz) directions
  7364314
- Assess sequence quality using FASTQC: fastqc HD1_1.fq.gz  HD1_2.fq.gz
- Download adaptors.fasta
- Trimmomatic: java -jar /home/fcta222/assembly/trimmomatic-0.38.jar PE -threads 2 -phred33 \
- trimlog Br80_errorlog.txt \
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

- Assess sequence quality of the trimmed reads (paired only) using FASTQC: fastqc HD1_1_paired.fq HD1_2_paired.fq
- Transfer the .html output files to your local machine using scp and open the file.

# (6) Check your trimmed reads using FASTQC to ensure removal of poor quality "tails" and adaptor contamination
- Successful trim.
  
# # raw reads (single end)
- zgrep -c "^@" HD1_1.fq.gz
    7364314

# # cleaned reads used for assembly (single end)
- ![image](https://github.com/user-attachments/assets/88d7805d-9e28-4cfe-8568-bd4d6ffff889)
  6593963
  
# (8) Use command line to count the total number of bases in the paired end reads sequence (forward + reverse reads)
- awk 'NR % 4 == 2' HD1_1_paired.fq | tr -d '\r\n' | wc -m
  983195403
- awk 'NR % 4 == 2' HD1_2_paired.fq | tr -d '\r\n' | wc -m
  988705693

  => 983195403 + 988705693 = 1971901096

## Module 4/Lab3
# Create a working directory on the MCC Supercomputer
- Directory: /project/farman_s25abt480
- Created: /project/farman_s25abt480/fcta222
# scp trimmed assembly from VM -to-> Supercomputer: 
- ```powershell scp fcta222@fcta222.cs.uky.edu:/home/fcta222/myGenome/Po18/HD1_1_paired.fq .``` (and HD1_2_paired.fq too).
# Use Velvet Advisor to help us find a good k-mer value befor ebeginning assembly
- https://dna.med.monash.edu/~torsten/velvet_advisor/
# Run velveth using the suggested k-mer value
-  velveth Bcereus_velvet1 181 -shortPaired -fastq.gz -separate \
Bcereus_S1_L001_R1_001.fastq.gz Bcereus_S1_L001_R2_001.fastq.gz
# Run velvetg to perform the assembly
- velvetg Bcereus_velvet1
# Run velvet optimiser using a range of k-mer values 
- velvetoptimiser -s 121 -e 201 -x 10 -d Bcereus_velvet_optimal \
f '-shortPaired -fastq.gz -separate Bcereus_S1_L001_R1_001.fastq.gz Bcereus_S1_L001_R2_001.fastq.gz' \
t 1


