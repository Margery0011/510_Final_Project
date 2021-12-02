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
     
- 2.2 Extract files

       Extract HT-Seq counts in different folders and put them into a new folder in both groups
       The example script is provided by the following code:
       ```
       setwd("*Directory*")
       dir.create("*NewFolderName*")
       for (dirname in dir('*Downloaded GGC files* ')){  
        file <- list.files(paste0(getwd(),'/*Downloaded GGC files dirname*),pattern = '*.counts')  
        file.copy(paste0(getwd(),'/*Downloaded GGC files* /',dirname,'/',file),'*NewFolderName*')  
       ```

       
- 2.3 Rename files
  
   1. Map Filenames to TCGA id & Save it as `sample2id_Duct/Lobular.txt`
      
      The example script is provided by the following code:
      ```
      metadata <- jsonlite::fromJSON("*Meta.json*")
      naid_df <- data.frame()
      for (i in 1:nrow(metadata)){
          naid_df[i,1] <- metadata$file_name[i]
          naid_df[i,2] <- metadata$associated_entities[i][[1]]$entity_submitter_id} 
        ```
      
      
   2. Use `change_name.sh` to change the filenames into TCGA-Format
   
   3. Add Pre-fix `Lobulargroup` in the filename of `Logroup` 
   
   4. Add Pre-fix `Ductgroup` in the filename of `Ductgroup`
   
   5. Paste these files into a new folder named `Lobular_Duct`

   6. set the directory to point at this file for further analysis in `Rstudio`

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
    ```
   ```
   directory <- "~/Lobular_Duct/" 
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

*Note : Extract the conditional information directly on the basis of the name of files , which ensures the one-to-one correspondence betweem the expression matrix and the sample*

- Step3 : Build the `DESeqDataset` ---Get 60483 elements

    ```
    library("DESeq2")
    dds <- DESeqDataSetFromHTSeqCount(sampleTable = sampleTable,
                                       directory = directory,
                                       design= ~ condition)
    dds
    ```
    
    ![1941638340881_ pic](https://user-images.githubusercontent.com/89502586/144184917-fa529835-d60f-49f1-ad21-8645bffcd387.jpg)
    
- Step4: Pre-filtering & Specify the factor levels  ---the number of elements has decreased from 60483 to 50860

     - 1. ***Remove the rows which are less than 10 reads***
     
     
          ```
            keep <- rowSums(counts(dds)) >= 10
            dds <- dds[keep,]
          ```
          As a result, the number of elements has decreased from 60483 to 50860
          
     - 2. ***By default, R will choose a reference level for factors based on alphabetical order, in this case , the reference is Ductgroup***
          
          `head(dds$condition)`
  
  ![1951638341336_ pic](https://user-images.githubusercontent.com/89502586/144185784-3fee4eab-51c6-49fc-bd5f-6a514726f653.jpg)
  
  log2 fold change and Wald test p value ： last level / reference level
  
  log2 fold change ： log2 (Lobulargroupt / Ductgroup)

- Step5: Differential Expression Analysis 

     - 1. ***Use function `results` to generate 6 columns including log2FC, P-value, corrected P-value .etc***
     - 2. ***Save them as "Lobular_Duct_res.csv". You can check them in Folder "results"*** [Lobular_Duct_res.csv](https://raw.githubusercontent.com/Margery0011/510_Final_Project/main/results/Lobular_Duct_res.csv)
    
    
    Parts of the results are showed below:
    
     ![1961638341892_ pic_hd](https://user-images.githubusercontent.com/89502586/144186802-d3765f11-da42-43fd-9cc9-05ba6d7c6b82.jpg)
     

### Define differential expressed genes and filter them

- Step1:

     - 1. Define GEG as  `padj < 0.05 & abs(log2FoldChange) > 1.5`
     - 2. Check its dimension 

     ```
     diff_gene_deseq2 <-subset(res, padj < 0.05 & abs(log2FoldChange) > 1.5)
     dim(diff_gene_deseq2)
     head(diff_gene_deseq2)
     ```

![2011638380505_ pic](https://user-images.githubusercontent.com/89502586/144285619-79adda3f-45d0-4769-9619-738285ecc629.jpg)

   - 3. 480 DEG are saved  the as `ReFiltered_DEG_Lobular_Duct.csv`
     
     - 4. You can check the result here. ReFiltered_DEG_Lobular_Duct.csv (need to change)
     
- Step2 : Name Transfer 
     
     - 1. Transfer the DEG results into data.frame
     
     ```
     library(dplyr)
     library(tibble)
     filtered_res <- diff_gene_deseq2 %>% 
     data.frame() %>% 
     rownames_to_column("gene_id")
     ```
     - 2. Remove the version number of EnsemblID

     ```
     row.names(filtered_res) <- filtered_res$gene_id
     gene_id <- substr(row.names(filtered_res),1,15)
     filtered_res$gene_id <- gene_id
     ```
     
     - 3. Add transfered gene symble ID for recognization and Entrizid  ID (just in case)
     
     ```
     library(AnnotationDbi)
     library(org.Hs.eg.db)
     filtered_res$symbol <- mapIds(org.Hs.eg.db,
                     keys=filtered_res$gene_id,
                     column="SYMBOL",
                     keytype="ENSEMBL",
                     multiVals="first")
     filtered_res$entrez <- mapIds(org.Hs.eg.db,
                     keys=filtered_res$gene_id,
                     column="ENTREZID",
                     keytype="ENSEMBL",
                     multiVals="first")
     ```
     - 4. Remove genes if the symbol is NA or duplicate 

     ```
     library(dplyr)
     gene_distinct <- filtered_res %>% 
     ## remove NA
     filter(symbol!="NA") %>% 
     ## remove duplicate
     distinct(symbol,.keep_all = T)
      ```
     
    The number of DEG has reduced from 480 to 328 
    
    
 - 5. Save the organized result as "symbolID_refiltered.csv". 
 
 Here is the result ["symbolID_refiltered.csv"](https://github.com/Margery0011/510_Final_Project/blob/main/Results/symbolID_refiltered.csv)


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

     
### Plot  Vignette 

- MA plot 
    
    - res

     ```
     plotMA(res, ylim=c(-2,2))
     ```

![1011637735879_ pic_hd](https://user-images.githubusercontent.com/89502586/144274461-70ac29c8-2fa8-442f-9e6b-517aa77cd8d7.jpg)

It shows the log2 fold changes attributable to a given variable over the mean of normalized counts for all the samples in the DESeqDataSet.

Points which fall out of the window are plotted as open triangles pointing either up or down.

     - resLFC
     
     ```
     plotMA(resLFC, ylim=c(-2,2))
     ```
     
     
![1981638377382_ pic_hd](https://user-images.githubusercontent.com/89502586/144277351-2a7f8315-0c6f-4eeb-aaf4-be202a960bb1.jpg)


Lots of noise associated with log2 fold changes from low count genes has been removed without requiring arbitrary filtering thresholds.
 

- Plot Counts

Use the function `plotCount` to look for differences between groups for a particular gene of interest. Counts groups by groups.

Use function `plotCounts` to normalize counts by the estimated size factors and add a pseudocount of 1/2 to allow for log scale plotting , counts are grouped by the variables in `intgroup`.

Plot-Gene can be selected by rowname or by numeric index , here we selected the gene with the smallest p value which is HORMAD1 (ensemblID : ENSG00000143452)


     ```
     plotCounts(dds, gene=which.min(res$padj), intgroup="condition")
     ```

![1041637735965_ pic](https://user-images.githubusercontent.com/89502586/144279820-e07fb29a-04e7-419d-8fc6-426d98c78013.jpg)


For customized plotting, an argument  `returnData` specifies that the function should only return a data.frame for plotting with `ggplot`.









     
 

   
