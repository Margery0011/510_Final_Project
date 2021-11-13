## Section 1: Update

### Filter:

- Filter the Files:

Data Category <- transcriptome profiling

Experimental Strategy <- RNA-Seq

Workflow Type <- HTSeq - Counts

Access <- open

- Filter the Cases:

Diagnoses Ajcc Pathologic Stage <- stage i;stage ia

Primary Site <- breast

Program <- TCGA-BRCA

Samples Sample Type <- primary tumor

Diagnoses Primary Diagnosis <- infiltrating duct carcinoma; lobular carcinoma

**Note: Because the number of sample diagnosed as infiltrating duct carcinoma(136) is 4 times more than sample diagnosed as lobular carcinoma(20) after filtering , so I narrow down the scale of group infiltrating duct carcinoma based on the comparasion of their clinical file. I use the condition of most patients in the clinical data of the lo group as the control variable to continue to filter the duct group.**

Adding conditions are shown in the follwing image:

![](https://github.com/Margery0011/510_Final_Project/blob/main/images/701636788772_.pic.jpg)

### DownLoad

- Download "gdc-client" from GDC Data portal webstie: https://portal.gdc.cancer.gov/ 
- Download "Manifest" file by `gdc-client download -m -Manifest.txt`
- Download "json" file


### Prepocess_2group.Rmd

- Copy the HTseq-counts file in different folder into one folder
- Read multiple HTseq-counts file from GGC into R in batches
- Find the corresponding TCGA id of file name and get the count matrix
- Add column name and gene name to the read data
- Map the file name and TCGA id , save them as "file_id_tcga_duct.csv " and "file_id_tcga_lo.csv". You can check them in Folder "results"
 

### ID Transfer.Rmd

- Transfer the EnsemblID to Genename
- Save the files as "Lo_Counts_expMatrix.csv" and "Duct_Counts_expMatrix.csv". You can check them in Folder "results"

### Deseq2.Rmd :Use `DESeqDataSetFromHTSeqCount` to input `HTseq` data & Use ***htseq-count input*** to build DESeqDataSet

- Step1: Change the name of group Lobular to "treated" and the name of group Duct to "untreated"

![](https://github.com/Margery0011/510_Final_Project/blob/main/images/github1.png)

- Step2: Specify the data location

    - 1.***Use function `list.files` to list all the files under current folder***
 
    - 2.***Find the files whose name contains character "treated"***

    - 3.***Extract the conditional information directly on the basis of the name of files , which ensures the one-to-one correspondence betweem the expression matrix and the sample***

- Step3: Build the `DESeqDataset`
- Step4: Pre-filtering & Specify the factor levels

     - 1.***Remove the rows which are less than 10 reads***
     - 2.***Sepcify how the `contrast` is set by fuction `factor`***
- Step5: Differential Expression Analysis 

     - 1.***Use function `results` to generate 6 columns including log2FC, P-value, corrected P-value .etc***
     - 2.***Save them as "res.csv". You can check them in Folder "results"***
![](https://github.com/Margery0011/510_Final_Project/blob/main/images/github3.png)

### Deseq2_shrinked.Rmd

- step1: Change the name of group Lobular to "logroup" and the name of group Duct to "ductgroup", put them in a new folder named "gdc_download_36Duct"

![](https://github.com/Margery0011/510_Final_Project/blob/main/images/711636791515_.pic.jpg)

- Repeat step2-5 in Deseq2.Rmd and save the reulst as "res_shinked.csv".You can check them in Folder "results".


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
