---
title: "Evaluation of read-mapping characteristics from a Cas-mediated PCR-free enrichment"
date: "Report created: 2019-08-20"
output:
  html_document:
    keep_md: yes
    number_sections: yes
    self_contained: yes
    theme: default
    highlight: null
    css: Static/ont_tutorial.css
    toc: yes
    toc_depth: 2
    toc_float:
      collapsed: yes
      smooth_scroll: yes
    df_print: paged
link-citations: yes
bibliography: Static/Bibliography.bib
always_allow_html: yes
---

<div style="position:absolute;top:0px;right:0px;padding:15px;background-color:gray;width:45%;">
![](https://nanoporetech.com/themes/custom/nanopore/images/ont-logo.svg?tutorial=cas9enrichment)<!-- -->
</div>













# Analysis of the fastq format sequence data

## Mapping sequence reads to the reference genome

The first step for the analysis of the Cas9 enrichment strategy is to assess the distribution and regional coverage of sequence reads across the whole genome. The **`fastq`** sequences produced during the DNA sequencing are mapped to the reference genome using the **`Minimap2`** software (@minimap22018). Results from the mapping analysis are passed to the **`samtools`** software (@samtools2009). **`Samtools`** is used to (1) filter out the unmapped sequence reads, (2) convert the uncompressed **`Minimap2`** SAM format output into the compressed BAM format and to (3) sort the sequences in the BAM file by their mapping coordinates. Further indexing the BAM file (again, using Samtools) enables efficient access to BAM entries that correspond to specific genomic locations.


## Definition of background and off-target regions of the genome

The Cas enrichment protocol depletes off-target DNA therefore enriching for the region of interest. In this tutorial all reads are aligned to the reference genome but not all of the reads sequenced during a Cas9 enrichment experiment align to the region of interest. All reads can be classed into four different mutually exclusive groups:

* **`On Target`** - reads that align to the regions of interest provided in the **`BED`** format coordinate file (*RawData/target.bed*)
* **`Target Proximal`** - reads that align to the regions immediately upstream or downstream of the region of interest (this regions is defined as 5000 bases)
* **`Off Target`** - Each crRNA in a panel should allow Cas9 to cut genomic DNA at sequence complementary sites with perfect alignment. Cas9 may also cut genomic DNA at complementary sites with multiple mismatches. Such regions are classified as off-target if the depth of coverage is > **20X**  over the mean background level
* **`Background`** - Reads that align to the reference genome but are not included in any of the categories above

The identification of the genomic regions corresponding to these mapping groups was performed using the **`R`** software. The **`GenomicRanges`** and **`GenomicAlignments`** packages (@granges2013) were used for genome geometry methods and the **`Rsamtools`** package (@R-rsamtools) and **`GenomicAlignments`** (@granges2013) packages were used to summarise the depth-of-coverage information used to identify the **`off-target`** genomic intervals.



## Executive Summary


![](Analysis/R/test_run_snakemake_enrichment_info.png)<!-- -->


The information presented above summarises key metrics for benchmarking the performance of a DNA sequencing run following the Cas-mediated PCR-free enrichment protocol. The expected values below are for a 24hr MinION/GridION run

* Output will be lower following a Cas-mediated enrichment protocol compared to an average Nanopore sequencing experiment (0.5-3.5 Gb depending on the number of gene-targets and number of pooled-samples that are included in the sequencing run)
* 1-10% of the sequenced data should be on target
* The mean coverage per target should be >200X
* A 3000X depletion of non-target DNA should be observed

All these metrics are variable between experiments and depend on the size of the region of interest and the experimental set up. For further information on how to optimise these numbers please refer to the protocol.


## Mapping characteristics by genomic segments



<table class="table table-striped table-condensed" style="margin-left: auto; margin-right: auto;">
<caption>Table summarising global mapping characteristics ranked by on-target, target-flanking and off-target</caption>
 <thead>
<tr>
<th style="border-bottom:hidden" colspan="1"></th>
<th style="border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="1"><div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">Background</div></th>
<th style="border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="1"><div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">Off-Target</div></th>
<th style="border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="1"><div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">Target-flanking</div></th>
<th style="border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="1"><div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">On-Target</div></th>
</tr>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:left;">   </th>
   <th style="text-align:left;">   </th>
   <th style="text-align:left;">   </th>
   <th style="text-align:left;">   </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> total sequence reads<sup>*</sup> </td>
   <td style="text-align:left;"> 19,031 </td>
   <td style="text-align:left;"> 1,562 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:left;"> 2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> mapped reads (primary)<sup>†</sup> </td>
   <td style="text-align:left;"> 17,612 </td>
   <td style="text-align:left;"> 1,562 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:left;"> 2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> bases sequenced </td>
   <td style="text-align:left;"> 277,749,115 </td>
   <td style="text-align:left;"> 12,773,065 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:left;"> 3,205 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> bases mapped </td>
   <td style="text-align:left;"> 268,205,869 </td>
   <td style="text-align:left;"> 12,773,065 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:left;"> 3,205 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Fraction of genome (%) </td>
   <td style="text-align:left;"> 99.544% </td>
   <td style="text-align:left;"> 0.455% </td>
   <td style="text-align:left;"> 0% </td>
   <td style="text-align:left;"> 0.001% </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Mean coverage (primary)<sup>‡</sup> </td>
   <td style="text-align:left;"> 0.08 </td>
   <td style="text-align:left;"> 2.29 </td>
   <td style="text-align:left;"> 0.09 </td>
   <td style="text-align:left;"> 0.09 </td>
  </tr>
</tbody>
<tfoot><tr><td style="padding: 0; border: 0;" colspan="100%">
<span style="font-style: italic;">please note: </span> <sup>*</sup> fastq bases are calculated from the qwidth field of the mapped sequences and from the sequence length of unmapped sequences <sup>†</sup> this table presents only primary sequence mappings <sup>‡</sup> depth of coverage based only on primary mapping reads</td></tr></tfoot>
</table>


* Background reads result from the incomplete dephosphorylation of the genomic DNA followed by a non-specific ligation of the adapter sequence
* Off target reads result from the Cas9 protein cutting the DNA at a genomic location outside of the target region. Further graphs to show the location and distribution of off target regions are presented later in the report. If the number of off target regions and reads is higher than desired, please review the probe design to assess possible SNPs and candidate sequence mismatches
* Comparing the number of bases or reads classified as target-flanking relative to on-target values shows the efficiency of the probe design. A high number of reads/bases classified as target-flanking indicates read-through; it would be recommended to review the probe design for the crRNA probe that appears to “leak”



## Evaluation of individual target performance


To gain the best insight on the performance of the Cas-mediated PCR-free enrichment protocol it is preferable to consider the performance of each discrete target separately. The table below highlights the characteristics for the different target regions defined within the starting BED file.

<table class="table table-striped table-condensed" style="margin-left: auto; margin-right: auto;">
<caption>Table summarising target mapping for pre-defined regions of interest</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> Target Gene </th>
   <th style="text-align:left;"> Target size (nt) </th>
   <th style="text-align:left;"> Mean coverage </th>
   <th style="text-align:left;"> Read count<sup>*</sup> </th>
   <th style="text-align:left;"> Bases<sup>†</sup> </th>
   <th style="text-align:left;"> Mean readLength </th>
   <th style="text-align:left;"> Mean readQuality </th>
   <th style="text-align:left;"> Mean mapQuality </th>
   <th style="text-align:left;"> Reads on FWD(%)<sup>‡</sup> </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> C9orf72 </td>
   <td style="text-align:left;"> 27,322 </td>
   <td style="text-align:left;"> 0.09 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 3,205 </td>
   <td style="text-align:left;"> 1,602 </td>
   <td style="text-align:left;"> 11.07 </td>
   <td style="text-align:left;"> 60 </td>
   <td style="text-align:left;"> 100 </td>
  </tr>
</tbody>
<tfoot><tr><td style="padding: 0; border: 0;" colspan="100%">
<span style="font-style: italic;">please note: </span> <sup>*</sup> Reads are counted as all sequence reads where the SAM start location is located within the target interval. This does not correct for sequences on the reverse strand. <sup>†</sup> Bases are counted as the sum of nucleotides from all reads where the SAM start location is within target region; some of these bases will overlap the flanking region <sup>‡</sup> reads are assessed for strand of mapping; here reads on + strand are summarised as percentage of all</td></tr></tfoot>
</table>


* The mean coverage per target should be >200x
* Reads on FWD(%) indicates the percentage of sequence reads that map to the forward strand. If this value is not in the region of 50% then one of the probes is not working effectively
* A perfect mean map quality should be 60. A value of 60 indicates that reads are mapping to a single location in the genome (the target location). Lower mapping qualities may indicate either fragmented mapping (blocks of sequence interspersed by regions of no mapping at a single genomic location) or multi-mapping (the sequences can be mapped to multiple locations in the genome) leading to off-target effects
* Comparison of target read lengths may be used to identify the targets (and their probes) that either allow read-through. The ratio between the mean read length and target size should also be considered.

If the values in the table above are not ideal then please check the probe design advice and input requirements in the Cas-mediated PCR-free enrichment protocol.





**The output files prepared for the on-target analysis include**

* The list of on-target read Ids can be found in the file **` Analysis/OnTarget/test_run_snakemake.OnTarget.mappedreads `**
* The **`fastq`** sequence file containing the raw sequence reads corresponding to these Ids can be found in the file **` Analysis/OnTarget/test_run_snakemake.OnTarget.fastq `**
* The coordinate information for the off-target regions can be found in the file **` Analysis/OnTarget/test_run_snakemake_ontarget.xlsx `**


## Graphical review of depth-of-coverage for target genes

The tables presented in the previous two sections have provided a summary of general mapping characteristics and on-target statistics. Plotting depth of coverage across the target regions also allows for an assessment of the performance of the crRNA guide used. The plots in this section review the depth of coverage, strandedness of mapping and leakiness of sequence coverage beyond the boundaries of the target region.


#```{r, warning=FALSE}
#singlePlot("HTT", aggregatedGR)
#```

The figure above shows the depth-of-coverage around a target region. The on-target region is located within the vertical red-bars and is flanked by the target-proximal regions. The horizontal bar shows the threshold at which an off-target feature would be defined. This plot is for the **`HTT`** target used in this tutorial.





#```{r, warning=FALSE}
#strandedPlot("HTT", aggregatedGR)
#```

The figure above presents the depth of coverage but is shaded by the strand (forward or reverse) to which the reads are mapped. This figure can be used to observe deviations from the expected 50:50 distribution of mapping between the + and - strands. Sequences that extend from the target regions and into the target-proximal regions may indicate suboptimal performance of a crRNA guide sequence.



![](ont_tutorial_cas9_files/figure-html/aggregatePlot-1.png)<!-- -->












# Off-target mapping

Having assessed on-target characteristics, it makes sense to also consider what has been mapped to off-target regions of the genome.

The **`ideogram`** below presents a description of the off-target mapping locations split by chromosome. Each shaded region (or bar) corresponds to an off-target region. There are in total **1694** genomic regions that satisfy the mean depth-of-coverage threshold of **1.8**


![](ont_tutorial_cas9_files/figure-html/unnamed-chunk-6-1.png)<!-- -->




The coordinates for these off-target regions have been written to an accompanying CSV file that may be imported into Excel for further analysis. The top 10 regions, ranked by mean depth-of-coverage, are presented in the table below.


<table class="table table-striped table-condensed" style="margin-left: auto; margin-right: auto;">
<caption>Table summarising the location and characteristics for the off-target regions with the highest depth-of-coverage</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> chrId </th>
   <th style="text-align:left;"> start </th>
   <th style="text-align:left;"> end </th>
   <th style="text-align:right;"> width </th>
   <th style="text-align:right;"> mean coverage </th>
   <th style="text-align:right;"> reads in segment </th>
   <th style="text-align:left;"> mean read length </th>
   <th style="text-align:right;"> %FWD reads </th>
   <th style="text-align:right;"> mean readQ </th>
   <th style="text-align:right;"> mean MAPQ </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 93,470,321 </td>
   <td style="text-align:left;"> 93,470,800 </td>
   <td style="text-align:right;"> 480 </td>
   <td style="text-align:right;"> 266 </td>
   <td style="text-align:right;"> 362 </td>
   <td style="text-align:left;"> 917 </td>
   <td style="text-align:right;"> 55.25 </td>
   <td style="text-align:right;"> 10.21 </td>
   <td style="text-align:right;"> 14.57 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:left;"> 49,709,041 </td>
   <td style="text-align:left;"> 49,712,000 </td>
   <td style="text-align:right;"> 2960 </td>
   <td style="text-align:right;"> 175 </td>
   <td style="text-align:right;"> 282 </td>
   <td style="text-align:left;"> 3,239 </td>
   <td style="text-align:right;"> 59.57 </td>
   <td style="text-align:right;"> 9.87 </td>
   <td style="text-align:right;"> 3.10 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 22 </td>
   <td style="text-align:left;"> 11,210,961 </td>
   <td style="text-align:left;"> 11,215,520 </td>
   <td style="text-align:right;"> 4560 </td>
   <td style="text-align:right;"> 104 </td>
   <td style="text-align:right;"> 113 </td>
   <td style="text-align:left;"> 8,661 </td>
   <td style="text-align:right;"> 83.19 </td>
   <td style="text-align:right;"> 10.08 </td>
   <td style="text-align:right;"> 5.29 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 8 </td>
   <td style="text-align:left;"> 43,237,601 </td>
   <td style="text-align:left;"> 43,242,400 </td>
   <td style="text-align:right;"> 4800 </td>
   <td style="text-align:right;"> 37 </td>
   <td style="text-align:right;"> 81 </td>
   <td style="text-align:left;"> 2,735 </td>
   <td style="text-align:right;"> 55.56 </td>
   <td style="text-align:right;"> 10.84 </td>
   <td style="text-align:right;"> 3.74 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 1 </td>
   <td style="text-align:left;"> 143,211,361 </td>
   <td style="text-align:left;"> 143,242,880 </td>
   <td style="text-align:right;"> 31520 </td>
   <td style="text-align:right;"> 16 </td>
   <td style="text-align:right;"> 34 </td>
   <td style="text-align:left;"> 15,796 </td>
   <td style="text-align:right;"> 29.41 </td>
   <td style="text-align:right;"> 10.40 </td>
   <td style="text-align:right;"> 4.70 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 16 </td>
   <td style="text-align:left;"> 46,382,561 </td>
   <td style="text-align:left;"> 46,407,600 </td>
   <td style="text-align:right;"> 25040 </td>
   <td style="text-align:right;"> 16 </td>
   <td style="text-align:right;"> 38 </td>
   <td style="text-align:left;"> 11,298 </td>
   <td style="text-align:right;"> 40.00 </td>
   <td style="text-align:right;"> 10.26 </td>
   <td style="text-align:right;"> 8.37 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 22 </td>
   <td style="text-align:left;"> 18,890,481 </td>
   <td style="text-align:left;"> 18,897,040 </td>
   <td style="text-align:right;"> 6560 </td>
   <td style="text-align:right;"> 15 </td>
   <td style="text-align:right;"> 20 </td>
   <td style="text-align:left;"> 13,777 </td>
   <td style="text-align:right;"> 45.00 </td>
   <td style="text-align:right;"> 13.65 </td>
   <td style="text-align:right;"> 3.47 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 14 </td>
   <td style="text-align:left;"> 16,091,761 </td>
   <td style="text-align:left;"> 16,096,560 </td>
   <td style="text-align:right;"> 4800 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 13 </td>
   <td style="text-align:left;"> 7,122 </td>
   <td style="text-align:right;"> 30.77 </td>
   <td style="text-align:right;"> 10.43 </td>
   <td style="text-align:right;"> 3.73 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 41,859,521 </td>
   <td style="text-align:left;"> 41,914,560 </td>
   <td style="text-align:right;"> 55040 </td>
   <td style="text-align:right;"> 8 </td>
   <td style="text-align:right;"> 30 </td>
   <td style="text-align:left;"> 16,266 </td>
   <td style="text-align:right;"> 34.38 </td>
   <td style="text-align:right;"> 8.62 </td>
   <td style="text-align:right;"> 0.96 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:left;"> 49,091,521 </td>
   <td style="text-align:left;"> 49,165,840 </td>
   <td style="text-align:right;"> 74320 </td>
   <td style="text-align:right;"> 7 </td>
   <td style="text-align:right;"> 34 </td>
   <td style="text-align:left;"> 16,547 </td>
   <td style="text-align:right;"> 91.43 </td>
   <td style="text-align:right;"> 10.80 </td>
   <td style="text-align:right;"> 1.41 </td>
  </tr>
</tbody>
<tfoot><tr><td style="padding: 0; border: 0;" colspan="100%">
<span style="font-style: italic;">please note: </span> <sup>*</sup> This table has been prepared using only read mapping information that corresponds to a primary map <sup>†</sup> The reads in segment column describes the number of sequences that start within this genomic interval (using SAM start coordinate only) <sup>‡</sup> mean read length is the mean sequence read length for the mapping reads identified; their strandedness is summarised in %FWD reads (the number of sequences that appear on the forward strand) and the mapping quality is summarised in mapq</td></tr></tfoot>
</table>


**The output files prepared from the off-target analysis include**

* The list of off-target read Ids can be found in the file **` Analysis/OffTarget/test_run_snakemake.OffTarget.mappedreads`**
* The **`fastq`** sequence file containing the raw sequence reads corresponding to these Ids can be found in the file **` Analysis/OffTarget/test_run_snakemake.OffTarget.fastq `**
* The coordinate information for the off-target regions can be found in the file **` Analysis/OffTarget/test_run_snakemake_offtarget.xlsx `**


# Read Mapping Visualisation using IGV

The Integrative Genomics Viewer (**`IGV`**) is a tool (@10.1093/bib/bbs017, @Robinsone31), that has been installed by **`conda`**, for the visualisation of genomics data. The software provides functionality for the display of sequence mapping data from BAM files that can subsequently be overlaid with "tracks" of information that can include depth-of-coverage for mapping data and gene annotations.

![](Static/Images/Cas9-IGV.PNG)

The figure above presents a screenshot from the **`IGV`** software. The coordinates for an off-target region have been selected (see the top bar of the figure for the coordinates) and the display has been zoomed-in so that the quality and mapping strand can be observed.

**`IGV`** can be started from the command line by using the command `igv`.

In this tutorial we recommend that you instead encourage **`IGV`** to display sequence information around a target of interest. During the analysis presented in this tutorial, we have been reviewing the enrichment of sequences around the **`HTT`** gene.

We would like to explore the read mapping information around this gene. The command below will open the **`IGV`** browser to the appropriate genome coordinates - in this example we are using the feature with coordinates `[Chr 4] Start=3072436, Stop=3079444` - this corresponds to the **`HTT`** gene.


```
igv -g ./ReferenceData/Homo_sapiens.GRCh38.dna.chromosome.4.fa \
./Analysis/Minimap2/cas9_FAK76554.bam,./RawData/enrichment_targets.bed \
4:49091201-49156600
```

** please note that if you are using your own reference genome and BED file that the parameters above may require modifications **


# Reproducible research - produce your own report

This report has been created using **`Rmarkdown`**, publicly available **`R`** packages, and the \LaTeX document typesetting software for reproducibility. For clarity the **`R`** packages used, and their versions, are listed below.

\fontsize{8}{12}


```
R version 3.5.1 (2018-07-02)
Platform: x86_64-conda_cos6-linux-gnu (64-bit)
Running under: Debian GNU/Linux 9 (stretch)

Matrix products: default
BLAS/LAPACK: /opt/conda/envs/base-env/lib/R/lib/libRblas.so

attached base packages:
[1] stats4    parallel  stats     graphics  grDevices utils     datasets 
[8] methods   base     

other attached packages:
 [1] tibble_2.1.3                writexl_1.1                
 [3] ggbio_1.30.0                emojifont_0.5.2            
 [5] dplyr_0.8.3                 GenomicAlignments_1.18.1   
 [7] Rsamtools_1.34.0            Biostrings_2.50.2          
 [9] XVector_0.22.0              SummarizedExperiment_1.12.0
[11] DelayedArray_0.8.0          BiocParallel_1.16.6        
[13] matrixStats_0.54.0          Biobase_2.42.0             
[15] GenomicRanges_1.34.0        GenomeInfoDb_1.18.1        
[17] IRanges_2.16.0              S4Vectors_0.20.1           
[19] BiocGenerics_0.28.0         reshape2_1.4.3             
[21] scales_1.0.0                RColorBrewer_1.1-2         
[23] ggplot2_3.2.0               kableExtra_1.1.0           
[25] session_1.0.3               yaml_2.2.0                 
```

\fontsize{10}{14}

It is also worth recording the versions of the software that have been used for the analysis.

\fontsize{8}{12}


```
# packages in environment at /opt/conda:
#
# Name                    Version                   Build  Channel
```

\fontsize{10}{14}



\pagebreak


# References and citations
