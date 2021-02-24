## Part 0: TCGA,cBIoportal,GEPIA2 application

### The Cancer Genome Atlas Program (TCGA)  
https://portal.gdc.cancer.gov/   
>The Cancer Genome Atlas (TCGA), a landmark cancer genomics program, molecularly characterized over 20,000 primary cancer and matched normal samples spanning 33 cancer types. This joint effort between the National Cancer Institute and the National Human Genome Research Institute began in 2006, bringing together researchers from diverse disciplines and multiple institutions.Over the next dozen years, TCGA generated over 2.5 petabytes of genomic, epigenomic, transcriptomic, and proteomic data. The data, which has already lead to improvements in our ability to diagnose, treat, and prevent cancer, will remain publicly available for anyone in the research community to use.   

![image](https://github.com/leiwaaping/bioinformatic-basic-practices/blob/main/pics/TCGA1.png)   

![image](https://github.com/leiwaaping/bioinformatic-basic-practices/blob/main/pics/TCGA2.png)

******************************************************** 

Q1: How many cases are involved in TCGA database? How many projects?  
Q2: How many primary site involved in TCGA database, why the cases size are different amoung various primary sites?   
Q3: what are the difference between projects,cases and files?  (click "Repository" to see more)  

***********************************************************  

**Task1: Find projects about breast cancer RNA-seq cohort.**  
"project" —— select "breast" in *Primary Site* and "RNA-seq" in *Experimental Strategy*  
In the end there are 6 projects (1700+ cases).

![image](https://github.com/leiwaaping/bioinformatic-basic-practices/blob/main/pics/TCGA3.png)  

**Task2: Find/Download a WGS mutation information (vcf files) from brain cancer case(age from 20 to 40).**  
"Repository" —— "Cases" —— select "brain" in *Primary Site* —— set age in *Age at Diagnosis*    
then "Files" —— select "VCF" in *Data Format*，click one samples into detail pages and click "Download".  
![image](https://github.com/leiwaaping/bioinformatic-basic-practices/blob/main/pics/TCGA4.png)  
![image](https://github.com/leiwaaping/bioinformatic-basic-practices/blob/main/pics/TCGA5.png)  

TCGA as a cancer samples data base, you can easily get a opened and small file with "Download" button.  
For large file and batch download, you can "Add to Cart" first, then download Manifest file and use [GDC Data Transfer Tool](https://gdc.cancer.gov/access-data/gdc-data-transfer-tool) in server.  
![image](https://github.com/leiwaaping/bioinformatic-basic-practices/blob/main/pics/TCGA5.png)  

Since most RNA-seq data in TCGA are controlled, if you want to do basic the gene analysis among TCGA samples, online tools GEPIA2 can help you!   
  
  
### Gene Expression Profiling Interactive Analysis(GEPIA2)  
http://gepia2.cancer-pku.cn/#index  
![image](https://github.com/leiwaaping/bioinformatic-basic-practices/blob/main/pics/GEPIA1.png)  

**Task1: Find differential expressed genes in ACC.**  
"Expression Analysis" —— "Differential Genes" —— select Dataset(cancer name) —— set Log2FC and qvalue cutoff (use default here) —— "List", then you will get the DE genes in ACC.  
![image](https://github.com/leiwaaping/bioinformatic-basic-practices/blob/main/pics/GEPIA2.png) 

**Task2: Check gene IGF2 expression in tumor samples and normal samples in ACC and BLCA.**  
"Expression Analysis" —— "Expression DIY" —— "Box Plot" —— add Dataset(cancer name)   
![image](https://github.com/leiwaaping/bioinformatic-basic-practices/blob/main/pics/GEPIA3.png)   
![image](https://github.com/leiwaaping/bioinformatic-basic-practices/blob/main/pics/GEPIA4.png)   

**Task3: Try to do a survival analysis, see if the IGF2 gene expression shows difference survival percentage in ACC.**  

**Task4：GEPIA2 is a helpful tool in TCGA RNA-seq basic analysis. Could you please come up with some questions related gene expression in Cancer samples, and try to answer them with GEPIA2?**  

  
  
Above analysis mostly based on gene expression. For mutations in genes, tool cbioportal is helpful.  

## cbioportal for cancer genome  
https://www.cbioportal.org/  
![image](https://github.com/leiwaaping/bioinformatic-basic-practices/blob/main/pics/cbioportal0.png)  

**Task1：check common BRCA1 gene mutations in breast cancer.**  
select "BRCA1" in *Quick search* —— click "mutation" —— search "breast", 27 mutations shown.  
![image](https://github.com/leiwaaping/bioinformatic-basic-practices/blob/main/pics/cbioportal2.png)   
  
**Task2：try to search clinical and mutation information about lung cancer.**   
![image](https://github.com/leiwaaping/bioinformatic-basic-practices/blob/main/pics/cbioportal3.png)    
*************************************  
Q1: What dose the heatmap shows?  
Q2: which is the most high risk gene for lung cancer?  
**************************************  

**Task3：vilizate and explain a study result .**   
select a study (take the MSK-IMPACT as example) —— "query by genes" —— input target genes —— "summit Query"  
![image](https://github.com/leiwaaping/bioinformatic-basic-practices/blob/main/pics/cbioportal4.png)  
![image](https://github.com/leiwaaping/bioinformatic-basic-practices/blob/main/pics/cbioportal5.png)  




 



