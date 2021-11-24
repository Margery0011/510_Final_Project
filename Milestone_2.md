
#  Analyzing RNA-seq data with `DESeq2` based on the Tutorial   [deseq2.Rmd](https://github.com/Margery0011/510_Final_Project/blob/main/scripts/deseq2.Rmd)

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

     - 1. ***Remove the rows which are less than 10 reads***
     - 2. ***Sepcify how the `contrast` is set by fuction `factor`***
   
- Step5: Differential Expression Analysis 

     - 1. ***Use function `results` to generate 6 columns including log2FC, P-value, corrected P-value .etc***
     - 2. ***Save them as "Lobular_Duct_res.csv". You can check them in Folder "results"***
    Parts of the results are showed below:
    ![](https://github.com/Margery0011/510_Final_Project/blob/main/images/921637724249_.pic.jpg)
    
    
### Data Visulization

***MAplot***

   - res
 
![1011637735879_ pic_hd](https://user-images.githubusercontent.com/89502586/143189864-0a7173ae-420c-4f86-9239-a4693dddc9b5.jpg)

   - resLFC
   

![1021637735884_ pic](https://user-images.githubusercontent.com/89502586/143191701-2fb96b31-2e65-4036-bf1d-11334e3c782b.jpg)


![1031637735953_ pic_hd](https://user-images.githubusercontent.com/89502586/143190356-64c888c4-2efd-4078-be27-08413dafa3b5.jpg)

***Plot Counts***


![1041637735965_ pic](https://user-images.githubusercontent.com/89502586/143190454-b2efea93-13ac-49f1-9653-0ea76ffaa262.jpg)


![1051637735970_ pic](https://user-images.githubusercontent.com/89502586/143190622-79fa3090-4a51-4f6d-8117-f6042bcffe64.jpg)

***meanSdPlot***

   - ntd

![1061637735979_ pic](https://user-images.githubusercontent.com/89502586/143190809-68bad2f7-2036-475f-8346-36198f7ac810.jpg)

   - vsd
   
![1081637736297_ pic](https://user-images.githubusercontent.com/89502586/143190994-d1f5618a-2baf-4738-8da4-b5d12802b9f5.jpg)

***HeatMap***

   - ntd

![1091637736334_ pic](https://user-images.githubusercontent.com/89502586/143191038-254028be-fd05-4f29-b5cf-6679f3f9a2de.jpg)

   - vsd

![1111637736343_ pic](https://user-images.githubusercontent.com/89502586/143191101-cbd637d4-f351-4e18-bd16-a5c0dc1a020c.jpg)

   - sample-to-sample distances
   
![1121637736412_ pic](https://user-images.githubusercontent.com/89502586/143191156-0b23d937-5654-46cb-9a70-9ee208633995.jpg)


***PCA PLots***

   - Default
   
![1131637736424_ pic](https://user-images.githubusercontent.com/89502586/143191505-71031ce6-a0da-475f-8910-8824d487cb22.jpg)

   - Customize 
   
![1151637736439_ pic](https://user-images.githubusercontent.com/89502586/143191519-8926c59d-5c93-4802-9d35-54afb5056bd2.jpg)

### DEG

- Step1: Choose Differential Expressed Gene 
   
    - 1. ***Filter the results by choosing `padj < 0.05 & abs(log2FoldChange) > 1`***   ---1490 elements left
    - 2. ***Save them as `DEG_Lobular_Duct.csv`. You can check them in Folder "results"*** 

# Transfer ID:  2 scripts



## [Diff_Ensembl_Transform.Rmd](https://github.com/Margery0011/510_Final_Project/blob/main/scripts/Diff_Ensembl_Transform.Rmd)

I transfered the ensembl ID to hgnc_symble and add descriptions

    - 1. ***Remove the number after \. in the EnsemblID***
    - 2. ***Transfer the EnsemblID to hgnc_symble and add descriptions of filtered genes*** ---1454 elements left(Duplicate Names have been removed)
    - 3. ***Save them as `DED_hgnc_symble.csv`. You can check them in Folder "results"*** 


### Results:

`diff_id_1.txt` : Only Ensembl ID of DEG  [diff_id_1.txt](https://github.com/Margery0011/510_Final_Project/blob/main/results/diff_id_1.txt)

`DED_hgnc_symble.csv` : Ensembl ID , hgnc_symbol and descriptions of DEG [DED_hgnc_symble.csv](https://github.com/Margery0011/510_Final_Project/blob/main/results/DED_hgnc_symble.csv)

`DEG_TransformedID_Lobular_Duct_res.csv` :Ensembl ID , hgnc_symbol descriptions and DESeq2 results of DEG [DEG_TransformedID_Lobular_Duct_res.csv](https://github.com/Margery0011/510_Final_Project/blob/main/results/DEG_TransformedID_Lobular_Duct_res.csv)

## [ID_Transfer(Succint_Version)](https://github.com/Margery0011/510_Final_Project/blob/main/scripts/ID_Transfer(Succint_Version).Rmd)

I transfered the Ensembl ID to gene ID and remove the version number in Ensembl ID in all the files.

### Results:

`Ensembl_ID_TO_Genename.csv` : Mapping Ensembl_ID to gene_id of Genome [Ensembl_ID_TO_Genename.csv]()

`DEG_res_geneid_nopoint.csv` ：Same as `DEG_TransformedID_Lobular_Duct_res.csv` without descriptions [DEG_res_geneid_nopoint.csv](https://github.com/Margery0011/510_Final_Project/blob/main/results/DEG_res_geneid_nopoint.csv)

`Lobular_Duct_res_geneid_nopoint.csv` : Ensembl_ID , gene_id and DESeq2 results of All the samples [Lobular_Duct_res_geneid_nopoint.csv](https://github.com/Margery0011/510_Final_Project/blob/main/results/Lobular_Duct_res_geneid_nopoint.csv)

`DEG_Lo_Duct_Counts_expMatrix.csv` : Matrix  [](https://github.com/Margery0011/510_Final_Project/blob/main/results/DEG_Lo_Duct_Counts_expMatrix.csv)

`All_Lo_Duct_Counts_expMatrix.csv` : Matrix [](https://github.com/Margery0011/510_Final_Project/blob/main/results/All_Lo_Duct_Counts_expMatrix.csv)


# Data

All Data has been uploaded to my Google Drive

# Known Issues

Too many differential expressed genes

Not so organized  （No Annotation in the `Diff_Ensembl_Transform.Rmd` and the code here is not succinct and clear

Have not change the directory
