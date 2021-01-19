# bioinformatic-basic-practices   Part II

This guidebook aims to demonstrate the RNA-seq analysis pipeline ([Part I](https://github.com/leiwaaping/bioinformatic-basic-practices/blob/main/linux%20and%20RNA-seq%20analysis%20pipeline.md)) and the basics statistical analysis behind Differential Gene expression detection (Part II) 

## R 


### R environment set up  

You can select to run R in your private PC,download R and Rstudio locally:  
- **R** :https://cran.r-project.org/  
- Rstudio:https://rstudio.com/products/rstudio/download/ (Windows recommended)   
 Mac user can type "R" in Terminal and run R directly.  
 
 or run [DE.ipynb](https://github.com/leiwaaping/bioinformatic-basic-practices/blob/main/DE.ipynb) in jupyter notebook with R environment via conda (multiple steps required).
 you can activate jupyter notebook in JW's lab server with command:  
 ```
 jupyter notebook --ip=147.**.**.**
 ```
 copy the first printed http web link and open it in browser.click top right button "new" - "R" to created a new R program, or double click to open code [DE.ipynb](https://github.com/leiwaaping/bioinformatic-basic-practices/blob/main/DE.ipynb)
 
 
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

- **PCA plot**  

```
#install.packages('ggfortify')
library(ggfortify)
dat_pca=t(dat)
#head(dat_pca[,1:100])

##apply PCA - scale. = TRUE is highly advisable, but default is FALSE. 
out_pca <- prcomp(dat_pca,scale= TRUE)
plot(out_pca,type="l")
autoplot(out_pca,data=dat_pca,size=0.1,label=FALSE,label.size=5)
```  
  
  
- read in sample information  
Sample ID, "event", "time to event" and "clinical stage" gain  

see file *GSE102349_series_matrix_survival.csv*, clinical file should involved Sample ID, "event", "time to event" and "clinical_stage" extracted from file *GSE102349_series_matrix.txt.gz*. Here we also group samples based on clinical stage. 

```
info <- as.data.frame(read.table("GSE102349_series_matrix_survival.csv",header = TRUE,sep = ",", dec = ".",na.strings = "NA",stringsAsFactors=FALSE,check.names = FALSE))
row.names(info) <- info[,1]
info<-info[,-1]
dim(info)

#remove missing data and filter out clinical stage I and II samples
info=info[!info[, "time"] == "N/A",]
info=info[info[, "clinical_stage"] == "III" | info[, "clinical_stage"] == "IV",]

#set time to numeric datatype
info$time=as.numeric(as.character(info$time))

dim(info)
head(info)
table(info$`clinical_stage`)
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

library(edgeR)
library(stats)
library(ggplot2)

group_list=info$clinical_stage
dge <- DGEList(counts=dat[,rownames(info)],group=group_list)
dge <- calcNormFactors(dge)

#filter low expressed gene
#keep <- rowSums(cpm(dge)>1) >= 2# at least at two sample cpm larger than 1
#dge <- dge[keep, , keep.lib.sizes=FALSE]

#normalization and Estimated dispersion，gene with large dispersion can be selected as differential expressed genes.
dge <- calcNormFactors(dge)
dge <- estimateCommonDisp(dge, verbose=TRUE)
dge <-estimateTagwiseDisp(dge)

```
see input expression data, sample information and differential expression analysis result.

```
head(dat)
head(info)
head(dge)
```  

- **VOLCONA PLOT**  
```
#selecte differential expression genes
selected <- exactTest(dge,pair = c("III","IV")) #group stage III vs stage IV
top <- topTags(selected,n=15000)  #get top diff 15000 genes

#extract differential expressed genes
diff=top$table
diff=diff[is.na(diff$FDR)==FALSE,]
head(diff)
dim(diff)

#draw picture
library(ggplot2)
DEG=diff
colnames(DEG)
plot(DEG$logFC,-log10(DEG$PValue))
#logFC_cutoff <- with(DEG,mean(abs(logFC)) + 2*sd(abs( logFC)) )
logFC_cutoff=1  #set cut off
DEG$change = as.factor(ifelse(DEG$PValue < 0.05 & abs(DEG$logFC) > logFC_cutoff,
                              ifelse(DEG$logFC > logFC_cutoff ,'UP','DOWN'),'NOT')
)
table(DEG$change)
this_tile <- paste0('Cutoff for logFC is ',round(logFC_cutoff,3),
                    '\nThe number of up gene is ',nrow(DEG[DEG$change =='UP',]) ,
                    '\nThe number of down gene is ',nrow(DEG[DEG$change =='DOWN',])
)
g = ggplot(data=DEG, aes(,x=logFC, y=-log10(PValue),color=change)) + geom_point(alpha=0.4, size=1.75) +
  theme_set(theme_set(theme_bw(base_size=20)))+ xlab("log2 fold change") + ylab("-log10 p-value") + 
  ggtitle(this_tile) +  theme(plot.title = element_text(size=15,hjust = 0.5)) + 
  scale_colour_manual(values = c('blue','black','red')) ## corresponding to the levels(res$change)
print(g)
#ggsave(g,filename = 'volcano.pvalue.png')
```



- **HEATMAP PLOT** 

```
#install.packages("pheatmap")
library(edgeR)
library(stats)
library(ggplot2)
library(pheatmap)

group_list=info$clinical_stage
dge <- DGEList(counts=dat[,rownames(info)],group=group_list)
dge <- calcNormFactors(dge)

#normalization and Estimated dispersion，gene with large dispersion can be selected as differential expressed genes.
dge <- calcNormFactors(dge)
dge <- estimateCommonDisp(dge, verbose=TRUE)
dge <-estimateTagwiseDisp(dge)

#selecte differential expression genes
selected <- exactTest(dge,pair = c("III","IV")) #group stage III vs stage IV
head(selected$table)

#extract differential expressed genes
diff=selected$table
up=diff[order(diff[,"logFC"],decreasing=TRUE),][1:50,] 
low=diff[order(diff[,"logFC"],decreasing=FALSE),][1:50,]
diff=rbind(up,low)
head(diff)
dim(diff)

aa=t(dat[row.names(diff),])
aa=t(scale(aa))

#deal with outlier to embellish the figure （if necessary)
aa[aa>=4]=4

p<-pheatmap(aa,show_rownames=T, cluster_cols=T, cluster_rows=T,cex=1, clustering_distance_rows="euclidean", cex=1,clustering_distance_cols="euclidean", clustering_method="complete", border_color=FALSE)
p
#colnames(aa[,p$tree_col[["order"]]]) 
#rownames(aa[p$tree_row[["order"]],]) 

```




*************************
*Q1:How large difference among the results from pearson correlation, spearman collelation and kendall collelation*
*Q2:which raw information should be extracted from GSE102349_series_matrix.txt file?*
*Q3: What edgeR actually do? Why need to do normalization?
*（additional）Try to understand each figure*
***************************




 
 
 
 
 

