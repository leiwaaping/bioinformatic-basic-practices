# bioinformatic-basic-practices  

This guidebook aims to understand the RNA-seq count data analysis pipeline and the basics of the statistical tests behind Differential Gene expression detection  

## basic linux prcatices  

### introduction
**UNIX** is an operating system which was first developed in the 1960s, and has been under constant development ever since.  
**Linux** is an entire family of open-source Unix operating systems, that are based on the Linux Kernel. This includes all of the most popular Linux based systems like Ubuntu, Fedora, Mint, Debian, and others. More accurately, they’re called distributions or distros.  
The **shell** acts as an interface between the user and the kernel. Most Linux distributions use a graphic user interface (GUI) as their shell, mainly to provide ease of use for their users，like virtual machine with linux system.  But here we highly recommend using a command-line interface (CLI) because it’s more powerful and effective. Tasks that require a multi-step process through GUI can be done in a matter of seconds by typing commands into the CLI.  The CLI can be easily get access to by Terminal in Mac and Putty/Xshell (install required) in Windows.

### practices tutorial
  - UNIX Tutorial for Beginners http://www.ee.surrey.ac.uk/Teaching/Unix/ 

*Q1: Define relative path and absolute path*  
*Q2：What ERRORs did you meet and how with deal with those errors?*  
*Q3: if you are using remote server, how to transfer files between server and your local PC? (hint: scp for Mac, xftp for Windows)*   

  
    
    
## RNA-seq analysis pipelines

### Download data (for user with individual PC)

1.  Search **fastq file(raw reads data)** for ENCODE K562 polyA+ RNA-seq data.  
  - For polyA+ RNA, we will use this: https://www.encodeproject.org/experiments/ENCSR000AEM/
  - For total RNA, we will use this: https://www.encodeproject.org/experiments/ENCSR000AEL/
  - Note that processed files are already on those webpages, but it will be good to start from the raw fastq files as it will help you understand the process and you will all the raw files alter for TE analysis.  
  
2. Download files. You will find the raw sequencing files on the "file details" tab from the respective links. The RNA-seq files are paired end so there are two files (Read 1 and Read 2) for each replicate. Download both ends for both replicates for both polyA+ and total RNA. There should be 8 files in total.
  - You can get the url link for the fastq files by right clicking and coping the download icon next to the Accession. Download the files to the server directly using wget with the url.
  - After you download the files rename them appropriately (can also use wget options to rename the file during download).
3. Also download the reference human genome and appropriate annotations from ENCODE (https://www.encodeproject.org/data-standards/reference-sequences/). Need:
  - **Reference genome** - GRCh38_no_alt_analysis_set_GCA_000001405.15
  - **Gene annotations** - ENCFF159KBI  
  
  
  ### Download data (for user with server)
  1. Search **fastq file(raw reads data)** for [GSE102349](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE102349) in NCBI Database.
  - randomly select two RNA-seq data , here take GSM2735202 and GSM2735204 as examples.
  - click in samples "GSM2735202" - click SRA "SRX3070255" - get Run ID [SRR5908747](https://trace.ncbi.nlm.nih.gov/Traces/sra/?run=SRR5908747)
  
 For saving times, aLL files have download to Jason's lab server under path ~/nas/Prcatices/  
 
  2. Download SRA file and convert SRA file to Pair end fastq files (1 SRA will generate 2 fastq file)
  ```
  prefetch SRR5908747
  fastq-dump --split-3 SRR5908747.sra
  ```
  
  3.  Also download the reference human genome and appropriate annotations from [UCSC gene browser](https://hgdownload.soe.ucsc.edu/downloads.html#human).
  - **Reference genome** - hg38.fa.gz  
  - **Gene annotations** - genes/hg38.refGene.gtf.gz  
  ```
  #Use `wget` and download files to server directly.
  wget http://hgdownload.soe.ucsc.edu/goldenPath/hg38/bigZips/hg38.fa.gz
  wget https://hgdownload.soe.ucsc.edu/goldenPath/hg38/bigZips/genes/hg38.refGene.gtf.gz
  ```
  
  *Q1: concepts understand:  
  a. Exon and Intron  
  b. Gene Expression 
  *
  
  
  
