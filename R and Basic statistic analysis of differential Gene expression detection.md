# bioinformatic-basic-practices   Part II

This guidebook aims to demonstrate the RNA-seq analysis pipeline ([Part I](https://github.com/leiwaaping/bioinformatic-basic-practices/blob/main/linux%20and%20RNA-seq%20analysis%20pipeline.md)) and the basics statistical analysis behind Differential Gene expression detection (Part II) 

## R 


### R environment set up  

You can select to run R in your private PC,download R and Rstudio locally:  
- **R** :https://cran.r-project.org/  
- Rstudio:https://rstudio.com/products/rstudio/download/ (Windows recommended)   
 Mac user can type "R" in Terminal and run R directly.  
 
 or run ** in jupyter notebook with R environment via conda (multiple steps required).
 you can activate jupyter notebook in JW's lab server with command:  
 ```
 jupyter notebook --ip=147.**.**.**
 ```
 copy the first printed http web link and open it in browser.click top right button "new" - "R" to created a new R program, or double click to open code **
 
 
### R practise  
 - [R Tutorial for Beginners: Learn R Programming Language](https://www.guru99.com/r-tutorial.html) 

### Expression matrix download  
Download Expression matrix and clinical information from NCBI ([GSE102349](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE102349))
- sample clinical information: click Series Matrix File(s) and download file *GSE102349_series_matrix.txt.gz*
- expression data：*GSE102349_NPC_mRNA_processed.txt.gz* 

### Pearson Correlation  
 
#### read in file  

```{r}
dat <- as.data.frame(read.table("GSE102349_NPC_mRNA_processed.txt",header = TRUE,sep = "\t", dec = ".",na.strings = "NA",stringsAsFactors=FALSE,check.names = FALSE))
row.names(dat) <- dat[,1]
dat<-dat[,-1]
head(dat)
dim(dat)
```


 
 
 
 
 

