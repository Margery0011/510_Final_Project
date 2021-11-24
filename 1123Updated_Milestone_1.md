## Section 1: Update

### 1. Filter:

- 1.1 Filter the Files: Choose the conditions in following graph to generate two groups


     1. Group of **Loular Carcinoma**  - Get 130 Files & 130 Cases ( referred as  `Logroup` in the following ) 
     
     
     ![891637643908_ pic](https://user-images.githubusercontent.com/89502586/143204022-62808922-5318-4e95-8c83-a5acae0c873b.jpg)

    
     2. Group of **Infiltrating Duct Carcinoma**  -Get 135 Files & 35 Cases ( referred as  `Ductgroup` in the following ) 

     
    ![901637645023_ pic](https://user-images.githubusercontent.com/89502586/143204093-76add9a3-a720-4c81-a680-df0dbe0534bc.jpg)

**Note: To make sure get  similar number of 2 groups, in the `Logroup`, I choosed the stage I, stage IA, stage IB, stage II, stage IIA. stage IIB , and in `Ductgroup`, the stages are only stage I ,stage IA and stage IB.**


### 2. DownLoad files and Pre-process Filenames

- 2.1 Download ((all done by clicking download bottons in the website) 
     
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
      `colnames(naid_df) <- c('filename','TCGA_id')`
      
      
   2. Use `change_name.sh` to change the filenames into TCGA-Format
   
   3. Add Pre-fix `Lobulargroup` in the filename of `Logroup` 
   
   4. Add Pre-fix `Ductgroup` in the filename of `Ductgroup`
   
   5. Paste these files into a new folder named `Lobular_Duct`

   6. set the directory to point at this file for further analysis in `Rstudio`
      


### Optional Scripts Explanation

**Prepocess_2group.Rmd**

- Copy the HTseq-counts file in different folder into one folder
- Read multiple HTseq-counts file from GGC into R in batches
- Find the corresponding TCGA id of file name and get the count matrix
- Add column name and gene name to the read data
- Map the file name and TCGA id , save them as "file_id_tcga_duct.csv " and "file_id_tcga_lo.csv". You can check them in Folder "results"
 

**ID Transfer.Rmd**

- Transfer the EnsemblID to Genename
- `Save the files as "Lo_Counts_expMatrix.csv" and "Duct_Counts_expMatrix.csv". You can check them in Folder "results" `




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
