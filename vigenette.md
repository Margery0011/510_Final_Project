### Plot Vignette

- 1. MA- Plot

`plotMA` shows the log2 fold changes attributable to a given variable over the mean of normalized counts for all the samples in the DESeqDataSet


the plotMA function is used to plot the histogram of mean of Normalized Counts. If the adjusted P value is less than 0.1, the color is marked.Whatever exceeds it is marked as a triangle

X ： Mean of Normalized Counts

Y : Log Fold Change 

Docs above the line mean Up-regulated

Docs below the line mean Down-regulated

  - res

`plotMA(res, ylim=c(-2,2))`

![991637735042_ pic_hd](https://user-images.githubusercontent.com/89502586/144653670-2ea2fca1-7a73-4a1e-bd75-247efbe949c5.jpg)
                                       
    
  - resLFC -- 

`plotMA(resLFC, ylim=c(-2,2))`

![1001637735149_ pic](https://user-images.githubusercontent.com/89502586/144653192-56cc05de-da3c-4869-89ee-c225e9faa599.jpg)

`resLFC` directly removes the noise associated with LFC from low-expressed genes without the need to manually set the threshold


  - resAsh 


