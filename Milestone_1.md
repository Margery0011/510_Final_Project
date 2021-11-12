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

### Deseq2.Rmd

- Change the name of group Lobular to "treated-number" and the name of group Duct to "untreated-number", put them in a new folder named "SampleFiles"

![](https://github.com/Margery0011/510_Final_Project/blob/main/images/github1.png?raw=true)

- Use ***htseq-count input*** to build DESeqDataSet
- Pre-filtering 
- Do differential expression analysis and save the result as "res.csv"
![](https://github.com/Margery0011/510_Final_Project/blob/main/images/github3.png)

## Section 2 :Next step

- Shrink the size of Group Duct
- Rename the htseq-counts file


## Section3: Data

## Section4: Known Issues

- There are way too many more samples in one group than the other
- The name of htseq-counts file is not clear
