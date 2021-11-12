## Section 1: Update

- Downloading data from  GDC Data portal webstie: https://portal.gdc.cancer.gov/ 

### Prepocess_2group.Rmd

- Read multiple HTseq-counts file from GGC into R in batches
- Find the corresponding TCGA id of file name and get the count matrix
- Add column name and gene name to the read data
- Save them as "Lo_Counts_expMatrix.csv" and "Duct_Counts_expMatrix.csv" , you can check them in Folder results

### ID Transfer.Rmd

- Transfer the EnsemblID to Genename

### Deseq2.Rmd

- Change the name of group Lobular to "treated-number" and the name of group Duct to "untreated-number", put them in a new folder named "SampleFiles"
![](https://github.com/Margery0011/510_Final_Project/blob/main/images/github1.png?raw=true)

- Use ***htseq-count input*** to build DESeqDataSet
- Pre-filtering 
- Do differential expression analysis and save the result as "res.csv"
![](https://github.com/Margery0011/510_Final_Project/blob/main/images/github3.png)

## Section 2 :Next step

- Shrink the size of Group Duct
- Do analyze


## Section3: Data

## Section4: Known Issues

- There are way too many more samples in one group than the other
