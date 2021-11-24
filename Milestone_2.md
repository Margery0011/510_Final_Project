
#  Analyzing RNA-seq data with `DESeq2` based on the Tutorial 

### Preparation

   [DESeq2 Tutorial Website](http://bioconductor.org/packages/release/bioc/vignettes/DESeq2/inst/doc/DESeq2.html)

- Step1: Set the Directory to point at the folder with changed names of both groups & library all the required packages

- Step2: Generate required input for building `DESeqDataset`

    - 1. Generate the `sampleFiles` : ***Use `grep` to select those files containing string `group`***
    - 2. Generate the `sampleCondition` : ***Use `sub` to chop up the sample filename to obtain the condition status***
    - 3. Generate the `sampleTable` : ***Use `data.frame`*** to build the dataframe by `sampleFiles` & `sampleCondition`

*Note : Extract the conditional information directly on the basis of the name of files , which ensures the one-to-one correspondence betweem the expression matrix and the sample*

- Step3: Build the `DESeqDataset` ---Get 60483 elements

    ```
    library("DESeq2")
    dds <- DESeqDataSetFromHTSeqCount(sampleTable = sampleTable,
                                       directory = directory,
                                       design= ~ condition)
    dds
    ```
    ![](https://github.com/Margery0011/510_Final_Project/blob/main/images/911637723895_.pic.jpg)
      
- Step4: Pre-filtering & Specify the factor levels  ---the number of elements has decreased from 60483 to 50860

     - 1.***Remove the rows which are less than 10 reads***
     - 2.***Sepcify how the `contrast` is set by fuction `factor`***
   
- Step5: Differential Expression Analysis 

     - 1.***Use function `results` to generate 6 columns including log2FC, P-value, corrected P-value .etc***
     - 2.***Save them as "Lobular_Duct_res.csv". You can check them in Folder "results"***
    Parts of the results are showed below:
    ![](https://github.com/Margery0011/510_Final_Project/blob/main/images/921637724249_.pic.jpg)
    

### Choose Differential Expressed Gene

- Step1: Choose Differential Expressed Gene 
   
    - 1.***Filter the results by choosing `padj < 0.05 & abs(log2FoldChange) > 1`***   ---1490 elements left
    - 2***Save them as "DEG_Lobular_Duct.csv". You can check them in Folder "results"*** 

- Step2: Transfer the EnsemblID to  DED_hgnc_symble

    - 1.***Transfer the EnsemblID to hgnc_symble and add descriptions of filtered genes*** ---1454 elements left(Duplicate Names have been removed)
    - 2.***Save them as "DED_hgnc_symble.csv". You can check them in Folder "results"*** 
    
### Data Visulization
