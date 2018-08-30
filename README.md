DupChecker
==========
* [Introduction](#Introduction)
* [Download and installation](#download)
* [Quick Start](#example)
* [Usage](#usage)
* [Report](#report)
* [Citation](#citation)

<a name="Introduction"/>
# Introduction #
Meta-analysis has become a popular approach for high-throughput genomic data analysis because it often can signifi-cantly increase power to detect biological signals or patterns in datasets. However, when using public-available databases for meta-analysis, duplication of samples is an often encoun-tered problem, especially for gene expression data. Not remov-ing duplicates would make study results questionable. We de-veloped a Bioconductor package Dupchecker that efficiently identifies duplicated samples by generating MD5 fingerprints for raw data.

<a name="download"/>
# Download and installation #
You can install DupChecker package in R from [github](https://github.com/shengqh/DupChecker/) by following codes:

	# install.packages("devtools")
	devtools::install_github("shengqh/DupChecker")
  
or from [Bioconductor](http://bioconductor.org/packages/devel/bioc/html/DupChecker.html) by following codes:

	if (!requireNamespace("BiocManager", quietly=TRUE))
    	install.packages("BiocManager")
	BiocManager::install("DupChecker")
  
<a name="example"/>
# Quick start

Here we show the most basic steps for a validation procedure. You need to create a target directory used to store the data. Here, we assume the target directory is your work directory.

	library(DupChecker)
	geoDownload(datasets = c("GSE14333", "GSE13067", "GSE17538"), targetDir=getwd())
	datafile<-buildFileTable(rootDir=getwd(), filePattern="cel$")
	result<-validateFile(datafile)
	if(result$hasdup){
  		duptable<-result$duptable
  		write.csv(duptable, file="duptable.csv")
	}

<a name="usage"/>
# Usage
##GEO/ArrayExpress data download
Firstly, function geoDownload/arrayExpressDownload will download raw data from ncbi/EBI 
ftp server based on datasets user provided. Once the compressed raw data is downloaded, 
individual data files will be extracted from compressed raw data. 

If the download or decompress cost too much time in R environment, user may download 
the GEO/ArrayExpress raw data and decompress the data to individual data files using other 
tools. The reason that we expect the data file not compressed CEL file is the compressed 
files from same CEL file but by different compress softwares may have different MD5 fingerprint.

The following code will download two datasets from ArrayExpress system and three datasets 
from GEO system. It may cost a few minutes to a few hours based your network performance.

	library(DupChecker)

	#download from ArrayExpress system
	datatable<-arrayExpress(datasets = c("E-TABM-158", "E-TABM-43"), targetDir=getwd()))
	datatable

	#Or download from GEO system
	datatable<-geoDownload(datasets = c("GSE14333", "GSE13067", "GSE17538"), targetDir=getwd())
	datatable

The datatable is a data frame containing dataset name and how many CEL files 
in that dataset.

##Build file table

Secondly, function buildFileTable will try to find all files in the subdirectories 
under root directories user provided. The result data frame contains two columns, 
dataset and filename. Here, rootDir can also be an array of directories. 

	datafile<-buildFileTable(rootDir=getwd(), filePattern="cel$")
	datafile

##Validate file redundancy

The function validateFile will calculate MD5 fingerprint for each file in table and 
then check to see if any two files have same MD5 fingerprint. The files with same 
fingerprint will be treated as duplication. The function will return a table contains 
all duplicated files and datasets.

	result<-validateFile(datafile)
	if(result$hasdup){
  		duptable<-result$duptable
  		write.csv(duptable, file="duptable.csv")
	}

<a name="report"/>
#Report
Table 1. Illustration of summary table generated by DupChecker for duplication among GSE13067, GSE14333, and GSE17538 data sets.

| MD5 | GSE13067(64/74) | GSE14333(231/290) | GSE17538(167/244) |
|-----|-----------------|-------------------|-------------------|
| 001ddd757f185561c9ff9b4e95563372 |	|	GSM358397.CEL |	GSM437169.CEL |
| 00b2e2290a924fc2d67b40c097687404 |	|	GSM358503.CEL |	GSM437210.CEL |
| 012ed9083b8f1b2ae828af44dbab29f0 |	GSM327335 |	GSM358620.CEL|	|
| 023c4e4f9ebfc09b838a22f2a7bdaa59 |	|	GSM358441.CEL |	GSM437117.CEL |

<a name="citation"/>
#Citation

If you use DupChecker in published research, please cite: 

Quanhu Sheng, Yu Shyr, Xi Chen.: DupChecker: a bioconductor package for checking high-throughput genomic data redundancy in meta-analysis. BMC bioinformatics 2014, 15:323.




