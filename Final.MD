# Differential gene expression of patients with 

## DATA Acquisition & Pre-processing

**I. Filter:**

- 1.1 Filter the Files: Choose the conditions in following graph to generate two groups


     1. Group of **Loular Carcinoma**  - Get 130 Files & 130 Cases ( referred as  `Logroup` in the following ) 
     
     
     ![891637643908_ pic](https://user-images.githubusercontent.com/89502586/143204022-62808922-5318-4e95-8c83-a5acae0c873b.jpg)

    
     2. Group of **Infiltrating Duct Carcinoma**  -Get 135 Files & 135 Cases ( referred as  `Ductgroup` in the following ) 

     
    ![901637645023_ pic](https://user-images.githubusercontent.com/89502586/143204093-76add9a3-a720-4c81-a680-df0dbe0534bc.jpg)

*Note: To make sure get  similar number of 2 groups, in the `Logroup`, I choosed the stage I, stage IA, stage IB, stage II, stage IIA. stage IIB , and in `Ductgroup`, the stages are only stage I ,stage IA and stage IB.*


**II.  DownLoad files and Pre-process Filenames**

- 2.1 Download (all done by clicking download bottons in the website) 
  
     
     1.  Download `Manifest` files of both groups
     2.  Download `json` files of both groups
     3.  Download `clinical` files of both groups
     

       
## Rename files & Build a clinical data  frame

Done by [Changefilenames.Rmd](https://github.com/Margery0011/510_Final_Project/blob/main/Scripts/Changefilenames.Rmd)

*Do not need to change the name of uploaded files in GoogleDrive*

- Step1 : Read Downloaded clinical csv in both groups
    
    - 1. Read files
     
     ```{r}
     coldata_lobular <- read.csv("~/Files/linicalLobular.tsv",sep = "\t")
     coldata_Duct <- read.csv("~/Files/clinicalDuct.tsv",sep = "\t")
     ```
     - 2. Remove duplicated values

     ```
     coldata_Duct <- coldata_Duct  %>% 
       distinct(case_submitter_id,.keep_all = T)
     coldata_lobular <- coldata_lobular  %>% 
       distinct(case_submitter_id,.keep_all = T)
     ```
     
     - 3. Combine 2 groups

     ```
          coldata <- rbind(coldata_Duct,coldata_lobular)
    
     ```
     
- Step2: Extract files (Do not need when Using data in GoogleDrive

    Extract HT-Seq counts in different folders and put them into a new folder in both groups
    The example script is provided by the following code:
       
       ```
          setwd("*Directory*")
          dir.create("*NewFolderName*")
          for (dirname in dir('*Downloaded GGC files* ')){  
               file <- list.files(paste0(getwd(),'/*Downloaded GGC files dirname*),pattern = '*.counts')  
               file.copy(paste0(getwd(),'/*Downloaded GGC files* /',dirname,'/',file),'*NewFolderName*')  
       ```     

- Step3 : Find the corresponding  TCGA id by filename and change htseq counts filenames 

     
     - 1. Map Filenames to TCGA id & Save it as `Duct/Lobular_filename_TCGAid.txt`
      
  
  The example script is provided by the following code:
      
     ```
      metadata <- jsonlite::fromJSON("*Meta.json*")
      naid_df <- data.frame()
      for (i in 1:nrow(metadata)){
          naid_df[i,1] <- metadata$file_name[i]
          naid_df[i,2] <- metadata$associated_entities[i][[1]]$entity_submitter_id} 
        
     ```
     
     - 2. only grab the TCGA's first 12 characters in clinical file

     ```
     attach(naid_df)
     naid_df$TCGA_id=substr(TCGA_id,regexpr("T",TCGA_id,),regexpr("T",TCGA_id)+11)  
     ```
     
     - 3. save the file  to  change file names  

     
     ```
     write.table(naid_df,"Lobular/Duct_filename_TCGAid.txt", quote = FALSE, row.names = FALSE,col.names = F)
     ```
  
  Used Files : 
  
  [Lobular_filename_TCGAid.txt](https://github.com/Margery0011/510_Final_Project/blob/main/Scripts/Lobular_filename_TCGAid.txt)
  
  [Duct_filename_TCGAid.txt](https://github.com/Margery0011/510_Final_Project/blob/main/Scripts/Duct_filename_TCGAid.txt)
  
    
    
- Step4: Change filename 

     - 1. Use `change_name.sh` to change the filenames into TCGA-Format

     ```
     #!/bin/bash

     cat $1 |while read line
     do
          arr=($line)
          filename=${arr[0]}
          submitterid=${arr[1]}
          mv ${filename} ${submitterid}.htseq.counts.gz
     done
     ```
     
     *Useage* : 
     
     bash change_name.sh ~/Files/Lobular_filename_TCGAid.txt
     
     bash change_name.sh ~/Files/Duct_filename_TCGAid.txt

     - 2. I want the filename to be as "Ductgroup/Lobulargroup + Digital seria number" 
     - 3. So, I Used Excel to connect this name to TCGA id  
     
     **Built Files **
     
     [Lobulargroup_id.xlsx](https://github.com/Margery0011/510_Final_Project/blob/main/Scripts/Lobulargroup_id.xlsx)
     
     [Ductgroup_id.xlsx](https://github.com/Margery0011/510_Final_Project/blob/main/Scripts/Ductgroup_id.xlsx)
     
     - 4. Use `name_change.sh` Change the filename (TCGA-ID format ) to "Ductgroup/Lobulargroup + Digital seria number" based on their correspondence  (Built Files)
     
     ```
     #!/bin/bash

     cat $1 |while read line
     do
          arr=($line)
          filename=${arr[1]}
          TCGAid=${arr[0]}
          mv ${filename}.gz ${TCGAid}.gz
     done
     ```
     
     **Used files**
     
     [Ductgroupchange.txt](https://github.com/Margery0011/510_Final_Project/blob/main/Scripts/Ductgroupchange.txt)
     
     [Lobulargroupchange.txt](https://github.com/Margery0011/510_Final_Project/blob/main/Scripts/Lobulargroupchange.txt)
     
     *Useage* : 
     
     bash name_change.sh ~/Files/Ductgroupchange.txt
     bash name_change.sh ~/Files/Lobulargroupchange.txt
     
     
     - 5. Paste these files into a new folder named `New_Lobular_Duct`
     

     - 6. Set the directory to point at this file for further analysis in `Rstudio`
   

## Analyzing RNA-seq data with DESeq2


   [DESeq2 Tutorial Website](http://bioconductor.org/packages/release/bioc/vignettes/DESeq2/inst/doc/DESeq2.html)
   
   
   
### Differential expression analysis

- Step1: Set the Directory to point at the folder with changed names of both groups & library all the required packages
  
  ```{r}
    library("DESeq2")
    library("apeglm")
    library("ggplot2")
    library("vsn")
    library("pheatmap")
    library("RColorBrewer")
   directory <- "~/New_Lobular_Duct/" 
   ```
   *Note: in the script, it is the Absolute path* 

- Step2 : Generate required input for building `DESeqDataset`

    - 1. Generate the `sampleFiles` : ***Use `grep` to select those files containing string `group`***
    - 2. Generate the `sampleCondition` : ***Use `sub` to chop up the sample filename to obtain the condition status***
    - 3. Generate the `sampleTable` : ***Use `data.frame`*** to build the dataframe by `sampleFiles` & `sampleCondition`

```
    sampleFiles <- grep("group",list.files(directory),value=TRUE)
    sampleCondition <- sub("(.*group).*","\\1",sampleFiles)
    sampleTable <- data.frame(sampleName = sampleFiles,
                          fileName = sampleFiles,
                          condition = sampleCondition)
    sampleTable$condition <- factor(sampleTable$condition)
```

- Step3 : Build the `DESeqDataset` ---Get 60483 elements

    ```
    library("DESeq2")
    dds <- DESeqDataSetFromHTSeqCount(sampleTable = sampleTable,
                                       directory = directory,
                                       design= ~ condition)
    dds
    ```
    
    ![1941638340881_ pic](https://user-images.githubusercontent.com/89502586/144184917-fa529835-d60f-49f1-ad21-8645bffcd387.jpg)


*Note : Extract the conditional information directly on the basis of the name of files , which ensures the one-to-one correspondence betweem the expression matrix and the sample*


- Step4 : Build sampleTable with **multiple factors** (condition: Ductgroup/Lobulargroup & Stage)

     - 1. remove the suffix of filename in `sampleTable`

     ```
     library(tidyr)
     sampleTable <- sampleTable %>%
          tidyr::separate(fileName,into = c("fileName"),sep = "\\.")
     ```
     
     - 2. Merge the name information and clinicla information
     
     ```
          colnames(Col_Duct_Lobular)[2] <- "fileName"
          sampleTable <- merge(sampleTable,Col_Duct_Lobular,by="fileName")
     ```
     
     - 3. Select the necessary columnns only
     
     ```
          library(dplyr)
          sampleTableselect <- sampleTable%>%
               dplyr::select(fileName,sampleName.x,condition,ajcc_pathologic_stage)
     ```

     - 4. Add the `Stage` factor
     
     ```
          sampleTableselect$condition <- factor(sampleTableselect$condition)
          sampleTableselect$Stage <- factor(sampleTableselect$ajcc_pathologic_stage)
     ```
     

     - 5. Build the `DESeqDataset` by Multiple factors
     
     ```
          ddsMF <- DESeqDataSetFromHTSeqCount(sampleTable = sampleTableselect,
                                       directory = directory,
                                       design= ~ condition + Stage)
          ddsMF
     ```
     
     ![2071638486138_ pic](https://user-images.githubusercontent.com/89502586/144516844-6cf5cba0-822d-4b5b-9448-b71292537066.jpg)


    
- Step5: Pre-filtering & Specify the factor levels  ---the number of elements has decreased from 60483 to 50860

     - 1. ***Remove the rows which are less than 10 reads***
     
     
          ```
            keep <- rowSums(counts(dds)) >= 10
            dds <- dds[keep,]
          ```
          As a result, the number of elements has decreased from 60483 to 50860
          
     - 2. **Check Factors**
          
     ```
          head(ddsMF$condition)
          head(ddsMF$Stage)
     ```
          
  ![2161638491629_ pic](https://user-images.githubusercontent.com/89502586/144524654-d23994e0-2b8e-44fe-a246-02b7aab9e387.jpg)

  
  log2 fold change and Wald test p value ： last level / reference level
  
  log2 fold change ： log2 (Lobulargroupt / Ductgroup)

- Step5: Differential Expression Analysis 

     - 1. Use function `results` to generate 6 columns including log2FC, P-value, corrected P-value .etc
     - 2. Save them as "Lobular_Duct_res.csv". You can check them in Folder "Results" [Res_Lobular_Duct_All.csv
](https://github.com/Margery0011/510_Final_Project/blob/main/Results/Res_Lobular_Duct_All.csv)
   
### Define differential expressed genes and filter them

- Step1:

     - 1. Define GEG as  `padj <= 0.05 & abs(log2FoldChange) >= 1.5`
     - 2. Check its dimension 

     ```
          diff_gene_deseq2 <-subset(res, padj <= 0.05 & abs(log2FoldChange) >= 1.5)
          dim(diff_gene_deseq2)
          head(diff_gene_deseq2)
     ```


     - 3. 463 DEG are saved  the as `New_DEG_Lobular_Duct.csv"`
     
     - 4. You can check the result here [New_DEG_Lobular_Duct.csv]( https://github.com/Margery0011/510_Final_Project/blob/main/Results/New_DEG_Lobular_Duct.csv)
     

### Log fold change shrinkage for visualization and ranking

Pass the `dds` object to the function  `lfcShrink` and use `apeglm`  to shrink effect size.

```
     resLFC <- lfcShrink(dds, coef="condition_Lobulargroup_vs_Ductgroup", type="apeglm")
     resLFC
```

![1971638377071_ pic_hd](https://user-images.githubusercontent.com/89502586/144276277-82db13a4-478c-4ec1-93d0-ede11b3ab8e5.jpg)

`resLFC` is more compacted compared to `res`
 column `stat` is removed after shrinking 

### Information of results columns

     ```
          mcols(res)$description   
     ```
     
 ![1991638379214_ pic](https://user-images.githubusercontent.com/89502586/144282346-b283506a-9e11-4383-9ae2-678ad21ae51a.jpg)

     
### Plot Vignette

- 1. MA- Plot

`plotMA` shows the log2 fold changes attributable to a given variable over the mean of normalized counts for all the samples in the DESeqDataSet


the plotMA function is used to plot the histogram of mean of Normalized Counts. If the adjusted P value is less than 0.1, the color is marked.Whatever exceeds it is marked as a triangle

X ： Mean of Normalized Counts

Y : Log Fold Change 

Docs above the line mean Up-regulated

Docs below the line mean Down-regulated

  - res

`plotMA(res, ylim=c(-2,2))`

![991637735042_ pic_hd](https://user-images.githubusercontent.com/89502586/144653670-2ea2fca1-7a73-4a1e-bd75-247efbe949c5.jpg)
                                       
    
  - resLFC 

`plotMA(resLFC, ylim=c(-2,2))`

![1001637735149_ pic](https://user-images.githubusercontent.com/89502586/144653192-56cc05de-da3c-4869-89ee-c225e9faa599.jpg)

`resLFC` directly removes the noise associated with LFC from low-expressed genes without the need to manually set the threshold


  - different Types

`apeglm `is the adaptive t prior shrinkage estimator from the apeglm package (Zhu, Ibrahim, and Love 2018). As of version 1.28.0, it is the default estimator.

`ashr` is the adaptive shrinkage estimator from the ashr package (Stephens 2016). Here DESeq2 uses the ashr option to fit a mixture of Normal distributions to form the prior, with method="shrinkage".

`normal` is the the original DESeq2 shrinkage estimator, an adaptive Normal distribution as prior.

![2211638565892_ pic_hd](https://user-images.githubusercontent.com/89502586/144673661-eb201646-ebcf-491f-996f-b639ecdee8df.jpg)

`type='apeglm` and `type='ashr'` have shown to have less bias than `type='normal'`









- 2. Plot Count

From the IPA  result, gene `MAGEA4` which is  Down-Regulataed compared to the  reference (Duct group)  is in the PATHway of disease "HER2 non-overexpressing breast carcinoma" (Category : Cancer,Organismal Injury and Abnormalities,Reproductive System Disease) , so I chose this gene to Plot Counts
  
  - Plot Counts

```
  d <- plotCounts(dds, gene="ENSG00000147381.10", intgroup="condition", 
                returnData=TRUE)
  library("ggplot2")
  ggplot(d, aes(x=condition, y=count)) + ggtitle("MAGEA4")+
    geom_point(position=position_jitter(w=0.1,h=0)) + 
    scale_y_log10(breaks=c(25,100,400))
```
![2221638567293_ pic](https://user-images.githubusercontent.com/89502586/144675796-7bd81773-988e-4ac4-8e16-54838007c852.jpg)


  - Box plot
  
```

  d1 <- plotCounts(dds,gene="ENSG00000147381.10", intgroup="condition",returnData = T)
              
  ggplot(d1,aes(condition, count)) + geom_boxplot(aes(fill=condition)) + scale_y_log10()

```

![2231638567382_ pic](https://user-images.githubusercontent.com/89502586/144675873-bfd3ad6a-dbd0-4916-a9c0-c1d2bc041f5f.jpg)



From the boxplot, it is obvious that this gene is signicant expressed between 2 groups.

### Data transformations and visualization 

The mean and standard deviation of the converted data between samples were plotted by these Transformations

- dds

```
vsd <- vst(dds, blind=FALSE)
ntd <- normTransform(dds)
```

`meanSdPlot(assay(ntd))`

![2241638568415_ pic](https://user-images.githubusercontent.com/89502586/144677759-8d8e5370-216f-4737-8347-8ff468c07827.jpg)

this gives log2(n + 1)

`meanSdPlot(assay(vsd))`


![2251638568497_ pic](https://user-images.githubusercontent.com/89502586/144677871-99c19958-b889-4014-8189-77e1b3e56b93.jpg)

variance stabilizing transformation

The shifted logarithm has elevated standard deviation in the lower count range, and while for the variance stabilized data the standard deviation is roughly constant along the whole dynamic range.

- ddsMF



```
vsd <- vst(ddsMF, blind=FALSE)
ntd <- normTransform(ddsMF)
```


### Data Evaluation and visualization (Using Multiple Factors : condition and Stage)

- Heatmap of the count matrix

      - ntdMF
      
![2261638569283_ pic](https://user-images.githubusercontent.com/89502586/144679361-6885a07e-fb97-4c9b-b252-8aef21c46167.jpg)

     - vsdMF
     
 ![2271638569294_ pic](https://user-images.githubusercontent.com/89502586/144679427-fde0fbcd-8b09-487f-937a-85204d0bc16b.jpg)


-  Heatmap of the sample-to-sample distances

Sample Clustering 

Here, I applyed the `dist` function to the transpose of the transformed count matrix to get sample-to-sample distances.

`sampleDists <- dist(t(assay(vsdMF)))`

Use `Euclidean distance` 

```
     library("RColorBrewer")
     sampleDistMatrix <- as.matrix(sampleDists)
     rownames(sampleDistMatrix) <- paste(vsdMF$condition, vsdMF$type,vsdMF$Stage, sep="-")
     colnames(sampleDistMatrix) <- NULL
     colors <- colorRampPalette( rev(brewer.pal(9, "Blues")) )(255)

     pheatmap(sampleDistMatrix,
               clustering_distance_rows=sampleDists,
               clustering_distance_cols=sampleDists,
               col=colors,show_rownames = F)
```

![2281638569531_ pic](https://user-images.githubusercontent.com/89502586/144679963-65ee89b3-a67f-4368-8786-644cfbed4420.jpg)

This heatmap shows the similarity between samples.

##  Transfer Names

Done by this script [Name_Transfer(Genome & DEG).Rmd](https://github.com/Margery0011/510_Final_Project/blob/main/Scripts/Name_Transfer(Genome%20%26%20DEG).Rmd)

### Map EnsemblD to Gene ID in Genome

I transfered the Ensembl ID to gene ID and remove the version number in Ensembl ID in all the files

  - 1. Make readble gtf file in R

     ```
          if(!require("rtracklayer")) BiocManager::install("rtracklayer") 
          gtf1 <- rtracklayer::import("Homo_sapiens.GRCh38.104.chr.gtf")
          gtf_df <- as.data.frame(gtf1)
      
      ```
      
  - 2. Use AnnotationDbi package to do ID transfer

```
get_map = function(input) {
  if (is.character(input)) {
    if(!file.exists(input)) stop("Bad input file.")
    message("Treat input as file")
    input = data.table::fread(input, header = FALSE)
  } else {
    data.table::setDT(input)
  }
  
  input = input[input[[3]] == "gene", ]
  
  pattern_id = ".*gene_id \"([^;]+)\";.*"
  pattern_name = ".*gene_name \"([^;]+)\";.*"
  
  
  gene_id = sub(pattern_id, "\\1", input[[9]])
  gene_name = sub(pattern_name, "\\1", input[[9]])
  
  Ensembl_ID_TO_Genename <- data.frame(gene_id = gene_id,
                                        gene_name = gene_name,
                                        stringsAsFactors = FALSE)
  return(Ensembl_ID_TO_Genename)
}

Ensembl_ID_TO_Genename <- get_map("gencode.v29.annotation.gtf") 
```


  - 3. Remove the version number of EnsembleID

```{r}
gtf_Ensembl_ID <- substr(Ensembl_ID_TO_Genename[,1],1,15)
Ensembl_ID_TO_Genename <- data.frame(gtf_Ensembl_ID,Ensembl_ID_TO_Genename[,2])

colnames(Ensembl_ID_TO_Genename) <- c("Ensembl_ID","gene_id")

```

  - 4. Save the file as "Ensembl_ID_TO_Genename.csv".
 
 [Ensembl_ID_TO_Genename.csv](https://raw.githubusercontent.com/Margery0011/510_Final_Project/main/Results/Ensembl_ID_TO_Genename.csv)



### Transfer the name in DEG

  - 1. Read DEG  file 

```
     diff_gene_deseq2 <- read.csv("~/Results/New_DEG_Lobular_Duct.csv")
```

  - 2. Change column name

```
     colnames(diff_gene_deseq2)[1] <- "gene_id"
```


  - 3. Remove the version number

```
     library(tidyr)
     diff_gene_deseq2 <- diff_gene_deseq2 %>%
          tidyr::separate(gene_id,into = c("gene_id"),sep = "\\.")
```

  - 4. Get gene symbol ID in DEG

```
     library(AnnotationDbi)
     library(org.Hs.eg.db)
     diff_gene_deseq2$symbol <- mapIds(org.Hs.eg.db,
                              keys=diff_gene_deseq2$gene_id,
                              column="SYMBOL",
                              keytype="ENSEMBL",
                              multiVals="first")
```


  - 5. Remove duplicated genes 

```
     library(dplyr)
     diff_gene_deseq2 <- diff_gene_deseq2 %>% 
     ## Remove NA
     filter(symbol!="NA") %>% 
     ## Remove Duplicate
     distinct(symbol,.keep_all = T)
```

Number has been reduced to 309 from 463

  - 6. Save the correspondence between Gene id and Ensembl ID 

```
     DEG_Ensemble_Symbol <- diff_gene_deseq2[,-c(2:7)]
     write_csv(DEG_Ensemble_Symbol, "DEG_Ensemble_Symbol.csv")
```

[DEG_Ensemble_Symbol.csv](https://github.com/Margery0011/510_Final_Project/blob/main/Results/DEG_Ensemble_Symbol.csv)

  - 7. Remove the ensemble ID 

```
     diff_gene_deseq2$gene_id <- diff_gene_deseq2$symbol
     diff_gene_deseq2 <- diff_gene_deseq2[,-8]
```


  - 8. Export Results to csv file

```
     write.csv(diff_gene_deseq2,"New_symbolID_refiltered.csv",row.names = F)
```
[New_symbolID_refiltered.csv](https://github.com/Margery0011/510_Final_Project/blob/main/Results/New_symbolID_refiltered.csv)


## IPA Analysis




     
 

   
