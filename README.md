# Transcriptional Activation of KLF6 Leads to Rejuvenation of Aged Human Hematopoietic Stem and Progenitor Cells


## Overview
We investigated how KLF6 regulates the epigenetic and transcriptional landscape in human hematopoietic stem and progenitor cells (HSPC).

## Analysis notes
  - All analyses were performed using Jupyter Lab notebooks within Docker
  - hg19 was used for all analyses
  - Notebooks are provided for:
    - ChIP-seq processing (used for both KLF6 KO and KLF6 activation)
    - RNA-seq processing (used for KLF6 activation and KO of the KLF6 enhancer)
    - Differential gene expression analysis of KLF6 enhancer KO HSPC
    - Differential gene expression analysis of KLF6 activated HSPC
    - Differential ChIP-seq peak dectection in KLF6 KO HSPC
    - Differential ChIP-seq peak dectection in KLF6 activated HSPC
  - Sample config files are also provided
  - The inital Docker file is provided, however, the additional packages were also installed:
    - chipenrich
    - Diffbind (installed within a conda environment)
    - genomation
    - aws cli
    - qorts

## Analysis of histone modifications (ChIP-seq) in HSPC after KLF6 perturbation
For details on processing the libraries, see 'ChIPseq_Processing.ipynb'. For all libraries the below steps were followed:

### ChIP-seq alignment and processing
  - Raw reads for each sample were concatenated, see 'Concat_NovaSeq_fastq.ipynb'
  - fastQC was performed
  - Illumina adapters were removed using cutadapt (v2.6)
  - Trimmed fastq reads were aligned to hg19 using bowtie2 (v2.3.5)
  - Reads were sorted and indexed using samtools (v1.9)
  - Mark duplicate reads using Picard (v2.21.2)
  - Filter out duplicate, multimapped, and low quality reads using samtools (v1.9)
  - Name sort the unique mapped reads using samtools (v1.9)
  - MultiQC performed
  - Bigwigs of the log2(IP/Input) were generated for each IP using deepTools2 bamCompare function
  - Narrow peak calling was performed using MACS2 (v.2.2.4)
  - Bigwigs of the fold-enrichment of (IP/Input) were generated using MACS2 (v.2.2.4) with the SPMR and bdgcmp functions
  - The config files used for input for this pipeline are 'config_file_KLF6a' and 'config_file_KLF6_E1'
  - Blacklist regions are in 'blacklist_filter.bed'
  - The file that contains chromosome sizes used to generate bigwigs is 'hg19.chrom.sizes'
  
### Differential ChIP-seq peak analysis
  - Differential peak calling was performed using the R-package Diffbind (version 3.0.9)
    - Used bUseSummarizeOverlaps=TRUE
  - Diffbind must be run in the conda environment 'diffbind'
  - See 'DiffBind_KLF6a.ipynb' and 'DiffBind_KO_KLF6.ipynb' and the respecitive input sample sheets, 'diffBind_KLF6a_sampleSheet.csv' and 'diffBind_KO_KLF6_sampleSheet.csv'
  - Differential peaks were annotated to the nearest TSS of Refseq hg19 using the R-pacakge chipenrich (v2.14.0)
  - Heatmaps of differential peaks were plotted using deeptools with the log2(IP/Input) bigwigs

## Transcriptional changes regulated by KLF6
RNA-seq was performed on young HSPC transfected with non-targeting control (NTC) or guides specific to the KLF6 enhancer 1 (E1). RNA-seq was also performed on young and aged HSPC in which KLF6 was induced using CRISPRa (KLF6a).
All libraries are stranded paired-end 75bp and contained ERCC spike-ins.


### Data alignment and processing
For details on processing the libraries, see 'RNAseq_Processing.ipynb'. For all libraries the below steps were followed:
- Raw reads were concantenated, see 'Concat_RNA_NexSeq.ipynb'
- Reads were trimmed and adapters removed using cutadapt (v2.6)
- Fastqc was performed 
- Reads were aligned to hg19 using the STAR aligner (v2.7.3)
- Aligned files were sorted using Samtools 
- Counts were generated using QoRTs (v1.3.0) and a .gtf file without rRNA, mitoRNA and tRNAs 


### Differential gene expression analysis
See 'DESeq2_EnhKO.ipynb' and 'DESeq2_KLF6a.ipynb'
  - Differential gene expression analysis was performed using DESeq2 (v1.30.0)
  - For the comparison of NTC vs. KLF6 E1 KO HSPC, a design that controlled for donorID while testing for differences between E1 KO was used.
  - For analysis of young and aged HSPC with KLF6 activation, a design that controlled for experimental batch and sequencing batch while testing for differences in sgNTC (non-targeting) vector versus KLF6 activation was used.\
  - For the main function, must input a DESeq2 style table as well as a data.frame 'comparison_table', which contains the comparisons to be performed
  - The ashr method was used to correct the log-fold change
  - The Wald method was used to generate ranked lists for GSEA
  - The regularized log counts generated by DESeq2 were also used for downstream analysis
  
  
## Published data sets used for analysis:
- ChIP-seq and RNA-seq from aged human HSCe: "Aging Human Hematopoietic Stem Cells Manifest Profound Epigenetic Reprogramming of Enhancers That May Predispose to Leukemia.", Cancer Discovery, PMID: 31085557
- Gene annotations for Gencode v19 (Ensembl 74): Gencode, http://www.gencodegenes.org/releases/19.html
- Genome sequence for GRCh37.p13: Gencode, http://www.gencodegenes.org/releases/19.html
- ERCC spike in .fa and .gtf: ThermoFisher, https://www.thermofisher.com/order/catalog/product/4456739
- hg19 black listed regions for ChIP-seq: https://github.com/Boyle-Lab/Blacklist/tree/master/lists

 

