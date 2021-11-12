## Section 1: Update

- Downloading data from  GDC Data portal webstie: https://portal.gdc.cancer.gov/ 

### Prepocess_2group.Rmd

- Read multiple HTseq-counts file from GGC into R in batches
- Find the corresponding TCGA id of file name and get the count matrix
- Add column name and gene name to the read data

### ID Transfer.Rmd

- Transfer the EnsemblID to Genename

### Deseq2.Rmd

- Change the name of group Lobular to "treated-number" and the name of group Duct to "untreated-number", put them in a new folder named "SampleFiles"
- Use ***htseq-count input*** to build DESeqDataSet
- Pre-filtering 
- Do differential expression analysis and save the result as "res.csv"

## Section 2 :Next step

- Shrink the size of Group Duct
- Do analyze


## Section3: Data

## Section4: Known Issues

- There are way too many more samples in one group than the other
