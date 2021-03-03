# bioinformatic-basic-practices   Part I

This guidebook aims to demonstrate the RNA-seq analysis pipeline (Part I) and the basics statistical analysis behind Differential Gene expression detection (Part II) 

## basic linux prcatices  

### introduction
**UNIX** is an operating system which was first developed in the 1960s, and has been under constant development ever since.  
**Linux** is an entire family of open-source Unix operating systems, that are based on the Linux Kernel. This includes all of the most popular Linux based systems like Ubuntu, Fedora, Mint, Debian, and others. More accurately, they’re called distributions or distros.  
The **shell** acts as an interface between the user and the kernel. Most Linux distributions use a graphic user interface (GUI) as their shell, mainly to provide ease of use for their users，like virtual machine with linux system.  But here we highly recommend using a command-line interface (CLI) because it’s more powerful and effective. Tasks that require a multi-step process through GUI can be done in a matter of seconds by typing commands into the CLI.  The CLI can be easily get access to by Terminal in Mac and Putty/Xshell (install required) in Windows.

### practices tutorial
  - [UNIX Tutorial for Beginners](http://www.ee.surrey.ac.uk/Teaching/Unix/)
  
*************************************************************
*Q1: Define relative path and absolute path*  
*Q2：What ERRORs did you meet and how with deal with those errors?*  
*Q3: if you are using remote server, how to transfer files between server and your local PC? (hint: scp for Mac, xftp for Windows)*   
*************************************************************
  
### linux_practice(opinional) 
(provide hints and answer from the example file only)  

1.Download a gtf file for plactise; or you can use any other gtf file you have.
```
wget http://hgdownload.soe.ucsc.edu/goldenPath/felCat9/bigZips/genes/felCat9.refGene.gtf.gz
gunzip -d felCat9.refGene.gtf.gz
```

2.How many rows in that gtf file?    
(8487 felCat9.refGene.gtf)  
  
3.How to extract the first 10 rows of the file?  
(try "head")  
  
4.Save all "CDS" transcript information to a new file called "CDS.txt".  
(try "grep" and ">" )  
  
5.print out the first column (ChromosomeID) of file CDS.txt.  
(try "cut")

6.search keyword "gene" in gtf file , why the result from "grep -c" and "grep -o \*\*|wc -l" are different?  
($grep -c "gene" felCat9.refGene.gtf, 8487; $grep -o "gene" felCat9.refGene.gtf|wc -l, 16974)  

7.How many kind of transcript(column 3) listed in the gtf file? and what's the number size of each kind of transcript respectively?  
(combine "grep" "sort" and "uniq" together)  

8.use vim editor to add a header at the first line  of gtf file.  
("vim" and "I" to get access to the editing mode."Esc" to the control mode,":wq" to save and quit)  

9.Jump to line 1000 of gtf file in vim editor  
  
10.replace all "refGene" with "CatRef".  
(try "vim" or "sed")
    
    
## RNA-seq analysis pipelines  
for individual PC and server user, we set two different fastq file and reference file. For the memory of private PC might be overload to run a normal RNA-seq dataset, so just switch to a smaller dataset. but all pipeline and command should be similar, here we demonstrate in server, please modify your file name and file path according to actual condition.  


### Download data (for user with individual PC)

1.  Search **fastq file(raw reads data)** for ENCODE K562 polyA+ RNA-seq data.  
  - For polyA+ RNA, we will use this: https://www.encodeproject.org/experiments/ENCSR000AEM/
  - For total RNA, we will use this: https://www.encodeproject.org/experiments/ENCSR000AEL/
  - Note that processed files are already on those webpages, but it will be good to start from the raw fastq files as it will help you understand the process and you will all   the raw files alter for TE analysis.  
  
2. Download files. You will find the raw sequencing files on the "file details" tab from the respective links. The RNA-seq files are paired end so there are two files (Read 1 and Read 2) for each replicate. Download both ends for both replicates for both polyA+ and total RNA. There should be 8 files in total.
  - You can get the url link for the fastq files by right clicking and coping the download icon next to the Accession. Download the files to the server directly using wget with the url.
  - After you download the files rename them appropriately (can also use wget options to rename the file during download).
3. Also download the reference human genome and appropriate annotations from [ENCODE](https://www.encodeproject.org/data-standards/reference-sequences/). Need:
  - **Reference genome** - GRCh38_no_alt_analysis_set_GCA_000001405.15
  - **Gene annotations** - ENCFF159KBI  
  
  
### Download data (for user with server)  

1. Search **fastq file(raw reads data)** for [GSE102349](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE102349) in NCBI Database.
  - randomly select two RNA-seq data , here take GSM2735202 and GSM2735204 as examples.
  - click in samples "GSM2735202" - click SRA "SRX3070255" - get Run ID [SRR5908747](https://trace.ncbi.nlm.nih.gov/Traces/sra/?run=SRR5908747)
  
 **For saving times, aLL files have download to Jason's lab server under path ~/nas/Prcatices/, for practice, use both hg19 or hg38 as reference is fine**
 
2. Download SRA file and convert SRA file to Pair end fastq files (1 SRA will generate 2 fastq file)
  ```
  prefetch SRR5908747
  fastq-dump --split-3 SRR5908747.sra
  ```
  - prefetch download
  ```
  wget https://ftp-trace.ncbi.nlm.nih.gov/sra/sdk/2.9.6/sratoolkit.2.9.6-ubuntu64.tar.gz  
  tar zxvf sratoolkit.2.9.6-ubuntu64.tar.gz  
  cd sratoolkit.2.9.6-ubuntu64  
  
  #add path  
  echo ' export PATH=$PATH:YOUR_PATH/sratoolkit.2.9.6-ubuntu64/bin' >> ~/.bashrc  
  source ~/.bashrc
```  

3.  Also download the reference human genome and appropriate annotations from [UCSC gene browser](https://hgdownload.soe.ucsc.edu/downloads.html#human).
  - **Reference genome** - hg19.fa.gz  
  - **Gene annotations** - genes/hg19.refGene.gtf.gz  
  ```
  #Use `wget` and download files to server directly.
  wget http://hgdownload.soe.ucsc.edu/goldenPath/hg19/bigZips/hg19.fa.gz
  wget https://hgdownload.soe.ucsc.edu/goldenPath/hg19/bigZips/genes/hg19.refGene.gtf.gz
  
  #To decompress a .gz file , use the -d option:
  gzip -d hg19.fa.gz
  gzip -d hg19.refGene.gtf.gz
  ```
  
*************************************************************
*Q4: Define Exon，Intron，gene expression*  
*Q5：Explain the principle of high throughput sequencing（like illumina) in your own words*  
*Q6: what is the format for fastq file and gtf file?*   
*************************************************************




  
### RNA-seq analysis pipelines  (run command line in CLI, don forget to modify to a proper path/file)  
ref:[Introduction to RNA-Seq using high-performance computing](https://hbctraining.github.io/Intro-to-rnaseq-hpc-O2/lessons/03_alignment.html)  

1. **quality check the fastq files** using [Fastqc](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/) Basically need to check if trimming of the RNA-seq reads are required.  
```
# make an directory to save the output files.
mkdir fastqc/
fastqc -o fastqc/ SRR5908747_1.fastq
```  

2. (optional)  trim and filter reads.
If need trimming, can use [bbduk.sh](https://jgi.doe.gov/data-and-tools/bbtools/bb-tools-user-guide/bbduk-guide/) or [trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic)
  
3. **alignment** using [STAR](https://github.com/alexdobin/STAR)  
- Index (need to run once only)
- Alignment 

```
# default N = 1, if your server have more available CPU, you can set a larger N, and run faster.
# index reference file and generated an SA file. only need to run once,make directories to save the index files and output bam file.
mkdir ref_index/
STAR --runThreadN N --runMode genomeGenerate --genomeDir ref_index/ --genomeFastaFiles ~/nas/Prcatices/ref/hg19.fa

# run alignment
mkdir bam/
STAR --runThreadN N --genomeDir ref_index/ --readFilesIn ~/nas/Prcatices/fastq/SRR5908747_1.fastq ~/nas/Prcatices/fastq/SRR5908747_2.fastq --outFileNamePrefix bam/SRR5908747
```

  
4. (optional)  Visualise alignment using [IGV](https://software.broadinstitute.org/software/igv/) and also learn to upload aligned reads (bigwig output from STAR) onto the [UCSC genome browser](https://genome.ucsc.edu/)  


5. **Quantifying reads** using [featureCounts](http://subread.sourceforge.net/)  
```
makdir count_result/
featureCounts -T N -a  ~/nas/Prcatices/ref/hg19.refGene.gtf -o count_result/SRR5908747count.txt bam/SRR5908747Aligned.out.sam
``` 
- featureCounts download  
  download [subread-2.0.1-Linux-x86_64.tar.gz](https://sourceforge.net/projects/subread/files/subread-2.0.1/) or other latest version.
  ```  
  tar zxvf subread-2.0.1-Linux-x86_64.tar.gz  
  cd subread-2.0.1-Linux-x86_64  
  
  #add path  
  echo ' export PATH=$PATH:YOUR_PATH/subread-2.0.1-Linux-x86_64/bin' >> ~/.bashrc  
  source ~/.bashrc
```  

*************************************************************
*Q7: How to evaluate the quality of fastq file?*  
*Q8：what is the meaning of each column in a Sam file?*  
*Q9: which value in featureCounts output file can see as the expression level of a gene?*   
*************************************************************



  
 
  
  
