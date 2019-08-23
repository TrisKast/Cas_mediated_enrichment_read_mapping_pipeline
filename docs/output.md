# Output

## Pipeline overview
The pipeline is built using [Nextflow](https://www.nextflow.io/)
and processes data using the following tools and scripts:

* [Minimap2](#minimap2) - versatile mapping tool for short and long reads
* [Samtools](#samtools) - suite of programs for interacting with high-throughput sequencing data
* [harvest.R](#harvest.R) - R script for enrichment evaluation
* [seqtk](#seqtk) - toolkit for processing sequences in FASTA/Q formats
* [report.Rmd](#render.Rmd) - R rmarkdown script to render the end report


## minimap2
[minimap2](https://github.com/lh3/minimap2)

**Output directory: `ReferenceData`**

* `reference.mmi`
  * Index of the reference for faster mapping

## samtools
[samtools](http://www.htslib.org/)

**Output directory: `Analysis/Minimap2`**
* `experiment_name.bam`
  * sorted bam file, containing mapped reads of the mapping sam file
* `experiment_name.bam.bai`
  * index of the sorted bam file
* `experiment_name.unmapped.bam`
  * bam file, containing the unmapped reads of the mapping sam file
* `experiment_name.unmapped.quals`
  * quality values of the unmapped reads

## harvest.R
[harvest.R](https://github.com/nanoporetech/ont_tutorial_cas9/blob/master/Static/Source/harvest.R)

**Output directory: `Analysis/OnTarget`**
* `experiment_name.OnTarget.mappedreads`
  *IDs of reads mapped ontarget

**Output directory: `Analysis/OffTarget`**
* `experiment_name.OffTarget.mappedreads`
  *IDs of reads mapped offtarget

**Output directory: `Analysis/R`**
* `experiment_name_aggregated_coverage.Rdata`
* `experiment_name_aggregated_offt_coverage.Rdata`
* `experiment_name_mapping_results.Rdata`
* `experiment_name.unmapped.rcounts.Rdata`
* `experiment_name_enrichment_info.png`
  * Overview of the key metrics

## seqtk
[seqtk](https://github.com/lh3/seqtk)

**Output directory: `Analysis/OnTarget`**

* `experiment_name.OnTarget.fastq`
  * Reads, mapped ontarget

**Output directory: `Analysis/OffTarget`**
* `experiment_name.OffTarget.fastq`
  * Reads, mapped offtarget

## report.Rmd
[report.Rmd](https://github.com/nanoporetech/ont_tutorial_cas9/blob/master/ont_tutorial_cas9.Rmd)

**Output directory: `.`**

* `report.html`
  * Report of the evaluation of the enrichment
