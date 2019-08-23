# Cas mediated enrichment read mapping pipeline

[![Nextflow](https://img.shields.io/badge/nextflow-%E2%89%A50.27.0-brightgreen.svg)](https://www.nextflow.io/)

[![Docker Repository on Dockerhub](https://img.shields.io/badge/docker-available-green.svg "Docker Repository on Dockerhub")](https://hub.docker.com/r/tristankast/cas_pipeline)

### Introduction
This pipeline maps long DNA sequence reads to a reference genome, and evaluates the performance of a Cas9 based target enrichment strategy. The workflow is suitable for Oxford Nanopore fastq sequence collections and requires a reference genome and a BED file of target coordinates. The program is built using [Nextflow](https://www.nextflow.io), a workflow tool to run tasks across multiple compute infrastructures in a very portable manner. It comes with docker / singularity containers making installation trivial and results highly reproducible.

The current workflow consists of:
1. Mapping of the reads onto a reference genome
2. Handling of generated sam files and transformation into bam files
3. Evaluation of the performance of the enrichment
4. Separation of reads into different files according to their mapping status


### Documentation
The pipeline comes with documentation, found in the `docs/` directory:

1. [Installation](docs/installation.md)
2. Pipeline configuration
    * [Local installation](docs/configuration/local.md)
3. [Running the pipeline](docs/usage.md)
4. [Output](docs/output.md)


### Credits
This pipeline was written by Tristan Kast ([tristankast](https://github.com/TrisKast)) at [DZNE](http://www.dzne.de), using R scripts from the nanoporetech ont_tutorial_cas9 github repo (https://github.com/nanoporetech/ont_tutorial_cas9).

[![DZNE](assets/dzne-logo.jpeg)](http://www.dzne.de)
