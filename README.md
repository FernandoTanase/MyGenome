# MyGenome
## University of Kentucky, Genome Sequencing.

# Sequence Quality Plots.
## Pre-Trimming:
### ![image](https://github.com/user-attachments/assets/57dfd546-49e6-4f6e-8684-788efe1d286b)
### ![image](https://github.com/user-attachments/assets/26e47aa5-a859-4219-ab5e-6ab6887220c6)
### ![image](https://github.com/user-attachments/assets/0f631b98-8025-4bbd-add5-28d153491df1)
## Post-Trimming:
### ![image](https://github.com/user-attachments/assets/f279c769-3fa9-412c-9ae5-b31aa135b852)
### ![image](https://github.com/user-attachments/assets/7b5812c5-fd71-4cad-a5f8-56d327d67960)
### ![image](https://github.com/user-attachments/assets/c54200eb-c471-4c8b-818c-3e8b7e2c2262)

# Adaptor Contamination.
## Pre-Trimming:
![image](https://github.com/user-attachments/assets/19568452-3820-4214-b91c-617719f662e3)
## Post-Trimming:
![image](https://github.com/user-attachments/assets/92f7e37e-a469-4abc-b5d6-caf831b02357)


# Upload Raw Genome to NCBI.
## Upload raw sequence data to NCBI (SRA)
- Create an SRA submission on the NCBI website.
- Download "key file" (aspera.openssh) on local machine, then scp to vm ('sequences' directory).
- wget the Aspera Connect Software into the 'sequences' directory:  
```wget https://d3gcli72yxqn2z.cloudfront.net/downloads/connect/latest/bin/ibm-aspera-connect_4.2.13.820_linux_x86_64.tar.gz```
- Uncompress the archive: 
```tar zxvf ibm-aspera-connect_4.2.13.820_linux_x86_64.tar.gz```
- Install the software: 
```bash ibm-aspera-connect_4.2.13.820_linux_x86_64.sh```
- To run the ascp transfer program, we need to tell the shell exactly where to find it. The installation places the software inside a "hidden" directory (.aspera) in your home directory: 
```ls ~/.aspera```
- To run the program we need to provide the complete path to it: 
```~/.aspera/connect/bin/ascp -i ~/sequences/aspera.openssh -QT -l100m -k1 -d ~/myGenome/Po18 subasp@upload.ncbi.nlm.nih.gov:uploads/fernando.tanase_uky.edu_bqluzRru```
- Submit screenshot of email confirmation/"BioProject submission (of my sample)" page + enter SRA accession number (SRR prefix) to the metadata sheet.

# Genome Quality Trimming, Module 5 (Lab2).
## Trim Sequence Reads
- Use command line tools to count sequence reads in the forward (_1.fq.gz) and reverse (_2.fq.gz) directions:
  7364314
- Assess sequence quality using FASTQC: 
```fastqc HD1_1.fq.gz  HD1_2.fq.gz```
- Download adaptors.fasta
- Trimmomatic: ```java -jar /home/fcta222/assembly/trimmomatic-0.38.jar PE -threads 2 -phred33 \
trimlog Br80_errorlog.txt \
./HD1_1.fq ./HD1_2.fq \
./HD1_1_paired.fq ./HD1_1_unpaired.fq \
./HD1_2_paired.fq ./HD1_2_unpaired.fq \
ILLUMINACLIP:./adaptors.fasta:2:30:10 \
SLIDINGWINDOW:20:20 MINLEN:120```
- Output, Started with arguments:
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
- Assess sequence quality of the trimmed reads (paired only) using FASTQC: 
```fastqc HD1_1_paired.fq HD1_2_paired.fq```
- Transfer the .html output files to your local machine using scp and open the file.
## Check the trimmed reads using FASTQC to ensure removal of poor quality "tails" and adaptor contamination.
- Successful trim.
## # raw reads (single end).
- ```zgrep -c "^@" HD1_1.fq.gz```
- Output: 7364314
## # cleaned reads used for assembly (single end).
- ```expr $(wc -l < HD1_2_paired.fq) / 4```
- Output: 6593963
## Use command line to count the total number of bases in the paired end reads sequence (forward + reverse reads)
- ```awk 'NR % 4 == 2' HD1_1_paired.fq | tr -d '\r\n' | wc -m```
- Output: 983195403
- ```awk 'NR % 4 == 2' HD1_2_paired.fq | tr -d '\r\n' | wc -m```
- Output: 988705693
  => 983195403 + 988705693 = 1971901096

# Genome Assembly, Module 4 (Lab3).
## Create a working directory on the MCC Supercomputer
- Directory: /project/farman_s25abt480
- Created: /project/farman_s25abt480/fcta222
## scp forward & reversed, trimmed assembly from VM -to-> Supercomputer personal directory: 
- ```scp fcta222@fcta222.cs.uky.edu:/home/fcta222/myGenome/Po18/HD1_1_paired.fq .``` (and HD1_2_paired.fq too).
## Copy the velvetoptimiser script to the directory:
- ```cp ../SLURM_SCRIPTS/velvetoptimiser_noclean.sh .```
## Add personal email address to the mail-user line of the slurm script for notifications:
- Found in velvetoptimiser_noclean.sh, line #SBATCH --mail-user farman@uky.edu,linkBlueID@uky.edu
## Submit assemblies to the SLURM queue:
- ```sbatch velvetoptimiser_noclean.sh HD1 61 131 10```
- Submitted batch job 30048974
- ```sbatch velvetoptimiser_noclean.sh HD1 81 121 2```
- .........
## Check progress by peeking at the SLURM log file:
- ```cat slurm-30048974.out```
## Check queued jobs or cancel job, commands:
- ```squeue | grep <linkBlueID>```
- ```scancel <jobID>```
## Rerun VelvetOptimiser using a narrower k-mer range and step size of 2 to reach the best possible dataset's assembly:
- ```sbatch velvetoptimiser_noclean.sh <genomeID> <start_kmer> <end_kmer> <step_size>```
