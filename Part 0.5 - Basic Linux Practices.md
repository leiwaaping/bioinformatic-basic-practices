# basic linux practices  

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
