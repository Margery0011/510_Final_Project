## Section 1: Update

### 1. Filter:

- 1.1 Filter the Files: Choose the conditions in following graph to generate two groups


     1. Group of **Loular Carcinoma**  - Get 130 Files & 130 Cases ( referred as  `Logroup` in the following ) 
     
     ![image](https://github.com/Margery0011/510_Final_Project/blob/main/images/891637643908_.pic.jpg)

    
     2. Group of **Infiltrating Duct Carcinoma**  -Get 135 Files & 35 Cases ( referred as  `Ductgroup` in the following ) 

     ![image](https://github.com/Margery0011/510_Final_Project/blob/main/images/901637645023_.pic.jpg)

**Note: To make sure get  similar number of 2 groups, in the `Logroup`, I choosed the stage I, stage IA, stage IB, stage II, stage IIA. stage IIB , and in `Ductgroup`, the stages are only stage I ,stage IA and stage IB.**


### 2. DownLoad files and Change file Names

- 2.1 Download ((all done by clicking download bottons in the website) 
     
     1.  Download `Manifest` files of both groups
     2.  Download `json` files of both groups
     
- 2.2 Change Names 

   1. Extract HT-Seq counts in different folders and put them into a new folder in both groups
       The example script is provided by the following code:
       ```
       setwd("*Directory*")
       dir.create("*NewFolderName*")
       for (dirname in dir('*Downloaded GGC files* ')){  
        file <- list.files(paste0(getwd(),'/*Downloaded GGC files dirname*),pattern = '*.counts')  
        file.copy(paste0(getwd(),'/*Downloaded GGC files* /',dirname,'/',file),'*NewFolderName*')  
       ```
   
   2. Change the file names in new folder to distinguish 2 groups （All done with the built-in functions of the computer)
      the name of `Logroup` has been formatted to `logroup+number.gz`
      the name of `Ductgroup` has been formatted to `ductgroup+number.gz`
      
  3.   Paste these files into a new folder named `Duct_Lobular` 
  4.   set the directory to point at this file for further analysis in `Rstudio`


### 3. Analyzing RNA-seq data with `DESeq2` based on the Tutorial 

   [DESeq2 Tutorial Website](http://bioconductor.org/packages/release/bioc/vignettes/DESeq2/inst/doc/DESeq2.html)

- Step1: Set the Directory to point at the folder with changed names of both groups & library all the required packages

- Step2: Generate required input for building `DESeqDataset`

    - 1. Generate the `sampleFiles` : ***Use `grep` to select those files containing string `group`***
    - 2. Generate the `sampleCondition` : ***Use `sub` to chop up the sample filename to obtain the condition status***
    - 3. Generate the `sampleTable` : ***Use `data.frame`*** to build the dataframe by `sampleFiles` & `sampleCondition`

*Note : Extract the conditional information directly on the basis of the name of files , which ensures the one-to-one correspondence betweem the expression matrix and the sample*

- Step3: Build the `DESeqDataset`

    ```
    library("DESeq2")
    dds <- DESeqDataSetFromHTSeqCount(sampleTable = sampleTable,
                                       directory = directory,
                                       design= ~ condition)
    dds
    ```
      
- Step4: Pre-filtering & Specify the factor levels

     - 1.***Remove the rows which are less than 10 reads***
     - 2.***Sepcify how the `contrast` is set by fuction `factor`***
- Step5: Differential Expression Analysis 

     - 1.***Use function `results` to generate 6 columns including log2FC, P-value, corrected P-value .etc***
     - 2.***Save them as "res.csv". You can check them in Folder "results"***
![](https://github.com/Margery0011/510_Final_Project/blob/main/images/github3.png)

### Deseq2_shrinked.Rmd

- step1: Change the name of group Lobular to "logroup" and the name of group Duct to "ductgroup", put them in a new folder named "gdc_download_36Duct"

！[](https://github.com/Margery0011/510_Final_Project/blob/main/images/711636791515_.pic.jpg)

- Repeat step2-5 in Deseq2.Rmd and save the reulst as "res_shinked.csv".You can check them in Folder "results".

### Optional Scripts Explanation

**Prepocess_2group.Rmd**

- Copy the HTseq-counts file in different folder into one folder
- Read multiple HTseq-counts file from GGC into R in batches
- Find the corresponding TCGA id of file name and get the count matrix
- Add column name and gene name to the read data
- Map the file name and TCGA id , save them as "file_id_tcga_duct.csv " and "file_id_tcga_lo.csv". You can check them in Folder "results"
 

**ID Transfer.Rmd**

- Transfer the EnsemblID to Genename
- Save the files as "Lo_Counts_expMatrix.csv" and "Duct_Counts_expMatrix.csv". You can check them in Folder "results"




## Section 2 :Next step

- Shrink the size of Group Duct -Done
- Rename the htseq-counts file -Done
- Use the changed-name files to do Deseq2 -Done
- Change Path/file name


## Section3: Data

## Section4: Known Issues

- There are way too many more samples in one group than the other --Samples have been shrinked
- The name of htseq-counts file is not clear --name has been changed
- Change the Name of Path in different scripts in order to increase readability and repeatability 
