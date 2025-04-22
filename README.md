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
- Submitted batch job 30167572
## Check progress by peeking at the SLURM log file:
- ```cat slurm-30048974.out```
## Check queued jobs or cancel job, commands:
- ```squeue | grep <linkBlueID>```
- ```scancel <jobID>```
## Rerun VelvetOptimiser using a narrower k-mer range and step size of 2 to reach the best possible dataset's assembly:
- ```sbatch velvetoptimiser_noclean.sh <genomeID> <start_kmer> <end_kmer> <step_size>```

# Genome Finalization (apply to step-size 2 assembly only).
## Rename the optimized assembly.
- In the MCC directroy "/project/farman_s25abt480/fcta222/HD1/velvet_HD1_81_121_2_noclean":
- ```mv contigs.fa HD1.famv contigs.fa HD1.fa```
## Copy the SimpleFastaHeaders.pl perl script into MCC:
- ```cp /project/farman_s25abt480/SCRIPTs/SimpleFastaHeaders.pl ```
## Using the script, rename the sequence headers to a standard format (HD1):
- ```perl SimpleFastaHeaders.pl /project/farman_s25abt480/fcta222/HD1/velvet_HD1_81_121_2_noclean/HD1.fa HD1```
## Copy the CullShortContigs.pl perl script into MCC:
- ```cp /project/farman_s25abt480/SCRIPTs/CullShortContigs.pl .```
## Use the CullShortContigs script on the file outputted by SimpleFastHeaders(HD1_nh.fasta):
- ```perl CullShortContigs.pl /project/farman_s25abt480/fcta222/SimpleFastaHeaders/HD1_nh.fasta```
- Output: "SimpleFastaHeaders/HD1_final.fasta"
## Using HD1_final.fasta, update the class metadata sheet (genome size & #contigs for step =2):
- number of contigs: ```grep -c "^>" HD1_final.fasta```
- genome size: ```awk '!/^>/ {total += length} END {print total}' HD1_final.fasta```
## Copy the SeqLen.pl perl script into MCC:
- ```cp /project/farman_s25abt480/SCRIPTs/SeqLen.pl .```
## Using SeqLen.pl, determine the final genome size (after culling):
- ```perl SeqLen.pl /project/farman_s25abt480/fcta222/SimpleFastaHeaders/HD1_final.fasta```
- Output: number of contigs = 3624 & genome size = 40980394.

# Check Genome Completeness using BUSCO.
## Background:
- BUSCO stands for "Benchmarking Using Single-Copy Orthologs" which simply means assessing genome completeness by using BLAST (tblastn) to search for a set of single-copy genes that are known to have "orthologsLinks to an external site." in genomes of related species. 
- BUSCO has a repository of ortholog databases (odb) for all kingdoms: animals, plants, fungi, etc.
- These databases can be further partitioned based on organismal phylum, class, etc.
- We will use odb10_ascomycota (a fungal phylum) for our analyses.
## Working in "/project/farman_s25abt480/fcta222/BUSCO" (MCC):
- Copy the BuscoSingularity.sh script: ```cp /project/farman_s25abt480/SLURM_SCRIPTS/BuscoSingularity.sh .```
-  Change email with personal email.
-  Run BUSCO: ```sbatch BuscoSingularity.sh /project/farman_s25abt480/fcta222/FinalizeAssmebly/SimpleFastaHeaders/fixed_HD1.fasta```
- Fold Coverage = (total bases in cleaned reads/genome sinze with step2), C = 98.6%, C+F= 98.8%.

# BLAST (against MoRepeats.fasta).
## Setup modern BLAST version in VM:
- Download latest version of BLAST (sec.4.3).
- Setup PATH variable & .bash_profile file for the latest blastn version.
## BLAST my genome:
- scp HD1_final.fasta from MCC -to-> VM: ```scp fcta222@mcc.uky.edu:/project/farman_s25abt480/fcta222/FinalizeAssmebly/SimpleFastaHeade
rs/HD1_final.fasta .```
- Run a blastn search using the sequence in MoRepeats.fasta as the query and my genome as the 
database: ```blastn -subject HD1/HD1_final.fasta -query MoRepeats.fasta -out MoRepeats.MyGenome.BLASTn0 \
-evalue 1e-20 -outfmt 0```
- Output: MoRepeats.MyGenome.BLASTn0
- Run it again with format 6: ```blastn -subject HD1/HD1_final.fasta -query MoRepeats.fasta -out MoRepeats.MyGenome.BLASTn6 \
-evalue 1e-20 -outfmt 6```
- Output: MoRepeats.MyGenome.BLASTn6
## Blast and the Command Line
### Filter BLAST results based on specific criteria:
- Blast format #6 in sec.4.8.
### Extract specific reult lines:
- Copy the SeqLen.pl script from MCC-to-> blast directory (in VM): ```scp fcta222@MCC.uky.edu:/project/farman_s25abt480/fcta222/FinalizeAssmebly/SeqLen/SeqLen.pl .```
- Use the script to find the ID and length of the longest contig in HD1: ```perl SeqLen.pl HD1_final.fasta | sort -k2n```: 3624    40980394
### Sort results:
- To uncover the order of repeat sequences on the contig: ```awk '$2 ~/contig<contigID>/' MoRepeats.MyGenomeBLASTn6 | sort -k9n```: replace <contigID> with specific contig#.

# BLAST (HD1 against MoMitochondrion.fasta & B71v2sh_masked.fasta).
## Download MoMitochondrion.fasta sequence from Farman Lab machine:
- ```scp ngs@10.163.183.71:Desktop/MoMitochondrion.fasta .```
## BLAST the MoMitochondrion.fasta sequence against HD1.fasta using output format 6 with specific column selections:
- ```singularity run --app blast2120 /share/singularity/images/ccs/conda/amd-conda1-centos8.sinf blastn -query MoMitochondrion.fasta -subject HD1_nh.fasta -evalue 1e-50 -max_target_seqs 20000 -outfmt '6 qseqid sseqid slen length qstart qend sstart send btop' -out MoMitochondrion.HD1.BLAST```
- Output file: 'MoMitochondrion.HD1.BLAST'
## Export list of contigs where alignment covers > 90% of the subject contig (NCBI upload along with HD1 assembly):
- ```awk '$4/$3 > 0.9 {print $2 ",mitochondrion"}' MoMitochondrion.HD1.BLAST > HD1_mitochondrion.csv```
## Copy the B71v2sh_masked.fasta genome:
- ```scp ngs@10.163.183.71:Desktop/B71v2sh_masked.fasta .```
## Run BLAST against B71v2sh_masked.fasta:
- ```singularity run --app blast2120 /share/singularity/images/ccs/conda/amd-conda1-centos8.sinf blastn -query B71v2sh_masked.fasta -subject HD1_final.fasta -evalue 1e-50 -max_target_seqs 20000 -outfmt '6 qseqid sseqid qstart qend sstart send btop' -out B71v2sh.HD1.BLAST```
- Output: B71v2sh.HD1.BLAST
## Export a list of contigs:
- ```awk '$3/$4 > 0.9 {print $2 ",mitochondrion"}' B71v2sh.HD1.BLAST > HD1_mitochondrion.csv```
## Identifying genetic variants between the B71v2sh genome and HD1 genome:
- Copy 'B71v2sh.HD1.BLAST' into the directory '/project/farman_s24cs485g/BLAST': ```cp B71v2sh.HD1.BLAST /project/farman_s25abt480/CLASS_BLASTs```

# Gene Prediction.
## I) SNAP.
### scp HD1.fasta (finalized):
- ```scp fcta222@mcc.uky.edu:/project/farman_s25abt480/fcta222/FinalizeAssmebly/SimpleFastaHeaders/HD1_final.fasta .```
### Rename file:
- ```mv HD1_final.fasta HD1.fasta```
### Run SNAP:
- ```snap-hmm Moryzae.hmm  HD1.fasta > HD1-snap.zff```
### Compute statistics using fathom:
- ```fathom HD1-snap.zff  HD1.fasta -gene-stats```
### Generate gff2 file:
- ```snap-hmm Moryzae.hmm  HD1.fasta -gff  >  HD1-snap.gff2```
## II) AUGUSTUS.
### Run AUGUSTUS:
- ```augustus --species=magnaporthe_grisea --gff3=on --singlestrand=true --progress=true ../snap/HD1.fasta > HD1-augustus.gff3```
## III) MAKER.
### Create maker config files:
- ```maker -CTL```
### Edit maker_opts.ctl with our parameters.
### Merge everything together into one GFF file:
- ```gff3_merge HD1.maker.output/HD1_master_datastore_index.log -o HD1-annotations.gff```

# Submit Genome Assembly to NCBI
## Special Fields
- BioProject:PRJNA926786
- BioSample: SAMNXXX (class worksheet)
- Genome Coverage = "# total bases in cleaned reads (R1 + R2)" / "Genome size (step = 2)"
- File uploaded: "fixed_HD1.fasta" INSTEAD OF "HD1_final.fasta" because of header formatting!
- Gaps -> "Did you randomly merge...": No (left selections as default)
- !!! Had to remove row "HD1_contig3819" from the submission bcause it was not present in the fasta file !!!
