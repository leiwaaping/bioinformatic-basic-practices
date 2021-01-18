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

### basics statistical analysis with R
 
- read in gene expression data, there shoube be 24530 genes and 113 samples

```{r}
dat <- as.data.frame(read.table("GSE102349_NPC_mRNA_processed.txt",header = TRUE,sep = "\t", dec = ".",na.strings = "NA",stringsAsFactors=FALSE,check.names = FALSE))
row.names(dat) <- dat[,1]
dat<-dat[,-1]
head(dat)
dim(dat)
```
- check missing data  

```
#check missing value
f<-function(x) sum(is.na(x))
#1 = summarize by row; 2= summarize by column
d<-as.data.frame(apply(dat,2,f))
d$id=row.names(d)
head(d[order(d[,1],decreasing=TRUE),])

#convert missing value to 0
dat[is.na(dat)] <- 0
```

- **pearson correlation test**  
test correlation between two samples, here take NPC0001PT00264T00264 and NPC0001PT00004T00004 as an example.  
Generate correlation plot (using R) between quantified genes from two samples (or gene from polyA+ and total RNA-seq).Don't forget to log the RNA expression before scatter plot.  

```
#install.packages("ggplot2")
#install.packages("dplyr")

library(ggplot2)
library(dplyr)

a=dat[,"NPC0001PT00264T00264"]
b=dat[,"NPC0001PT00004T00004"]
cor(a,b,method="pearson")
ggplot(dat,aes(x=log(dat[,"NPC0001PT00264T00264"]),y=log(dat[,"NPC0001PT00004T00004"])))+ geom_point(size=1,shape=15)+geom_smooth(method=lm)
```

multiple correlation test for the whole matrix.  

```
c=cor(dat,method="pearson")
head(c)
```
- gain sample information  

see file *GSE102349_series_matrix_survival.csv*, clinical file should involved Sample ID, "event", "time to event" and "clinical stage" extracted from file *GSE102349_series_matrix.txt.gz*. Here we also group samples based on clinical stage. 

```
info <- as.data.frame(read.table("GSE102349_series_matrix_survival.csv",header = TRUE,sep = ",", dec = ".",na.strings = "NA",stringsAsFactors=FALSE,check.names = FALSE))
row.names(info) <- info[,1]
info<-info[,-1]
dim(info)

#remove missing data and filter out clinical stage I and II samples
info=info[!info[, "time"] == "N/A",]
info=info[info[, "clinical stage"] == "III" | info[, "clinical stage"] == "IV",]

#set time to numeric datatype
info$time=as.numeric(as.character(info$time))

dim(info)
head(info)
table(info$`clinical stage`)
```

- **survival plot**  

```
#install.packages("survminer")
#install.packages("survival")

library(survival)
library(ggplot2)
library(survminer)
library(dplyr)

#set time value to numeric 
info$time=as.numeric(as.character(info$time))

#set status value to numeric
a <- sub("Disease progression",1,info$event)
info$event <- as.numeric(as.character(sub("Last follow-up",0,a)))
head(info)

coxph(Surv(time, event)~clinical_stage, data=info)
fit <- survfit(Surv(time, event)~clinical_stage, data=info)
ggsurvplot(fit, conf.int=TRUE, pval=TRUE)
```


- **Perform differential gene expression analysis** using [edgeR](https://bioconductor.org/packages/release/bioc/html/edgeR.html)  
```
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

BiocManager::install("edgeR")
```




- **ploting heatmap** 

```
#install.packages("pheatmap")
```




*************************
*Q1:How large difference among the results from pearson correlation, spearman collelation and kendall collelation*
*Q2:which raw information should be extracted from GSE102349_series_matrix.txt file?*




 
 
 
 
 

