# 510_Final_Project

## Title

Differential Gene Expression profiling of female in Breast Cancer StageI with Lobular carcinoma and Infiltrating duct carcinoma

## Author

Margery Lyo

## Overview
This is aimed to identify differential expressed genes for woman having Lobular carcinoma and Infiltrating duct carcinoma in stagei and stage ia. This analysis will utilize the package DeSEQ2 and follow the specific vignette: [DeSEQ2](http://bioconductor.org/packages/release/bioc/vignettes/DESeq2/inst/doc/DESeq2.html).

For this analysis,  I'll use the TCGA cohort and have identified 20 ht-seq counts files for each group.I will decide the control variances based on the clinical data in the Lobular carcinoma group since there are only 20 availble samples here. 

## Data

The data is obtained from [TCGA Database]( https://portal.gdc.cancer.gov/repository).For this subject, I have chosed the patients who are female and diagnosed with breast cancer in stageI. Then the data is grouped by the disease Lobular carcinoma and Infiltrating duct carcinoma.Since there are 20 ht-seq counts files in Lobular carcinoma, I will chose 20 samples from group Infiltrating duct carcinoma.

## Milestone 1

- Download the filted data from TCGA Dataset in Ht-seq format.
- Proprocess these data before analysizing  them into R.

## Milestone 2

- Generate plots of the differentially expressed genes and differential analysis.
- 
