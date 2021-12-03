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
                                       
    
  - resLFC 

`plotMA(resLFC, ylim=c(-2,2))`

![1001637735149_ pic](https://user-images.githubusercontent.com/89502586/144653192-56cc05de-da3c-4869-89ee-c225e9faa599.jpg)

`resLFC` directly removes the noise associated with LFC from low-expressed genes without the need to manually set the threshold


  - different Types

`apeglm `is the adaptive t prior shrinkage estimator from the apeglm package (Zhu, Ibrahim, and Love 2018). As of version 1.28.0, it is the default estimator.

`ashr` is the adaptive shrinkage estimator from the ashr package (Stephens 2016). Here DESeq2 uses the ashr option to fit a mixture of Normal distributions to form the prior, with method="shrinkage".

`normal` is the the original DESeq2 shrinkage estimator, an adaptive Normal distribution as prior.

![2211638565892_ pic_hd](https://user-images.githubusercontent.com/89502586/144673661-eb201646-ebcf-491f-996f-b639ecdee8df.jpg)

`type='apeglm` and `type='ashr'` have shown to have less bias than `type='normal'`









- 2. Plot Count

From the IPA  result, gene `MAGEA4` which is  Down-Regulataed compared to the  reference (Duct group)  is in the PATHway of disease "HER2 non-overexpressing breast carcinoma" (Category : Cancer,Organismal Injury and Abnormalities,Reproductive System Disease) , so I chose this gene to Plot Counts
  
  - Plot Counts

```
  d <- plotCounts(dds, gene="ENSG00000147381.10", intgroup="condition", 
                returnData=TRUE)
  library("ggplot2")
  ggplot(d, aes(x=condition, y=count)) + ggtitle("MAGEA4")+
    geom_point(position=position_jitter(w=0.1,h=0)) + 
    scale_y_log10(breaks=c(25,100,400))
```
![2221638567293_ pic](https://user-images.githubusercontent.com/89502586/144675796-7bd81773-988e-4ac4-8e16-54838007c852.jpg)


  - Box plot
  
```

  d1 <- plotCounts(dds,gene="ENSG00000147381.10", intgroup="condition",returnData = T)
              
  ggplot(d1,aes(condition, count)) + geom_boxplot(aes(fill=condition)) + scale_y_log10()

```

![2231638567382_ pic](https://user-images.githubusercontent.com/89502586/144675873-bfd3ad6a-dbd0-4916-a9c0-c1d2bc041f5f.jpg)



From the boxplot, it is obvious that this gene is signicant expressed between 2 groups.

