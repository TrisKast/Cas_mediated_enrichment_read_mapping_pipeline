# Usage

## General Nextflow info
Nextflow handles job submissions on SLURM or other environments, and supervises running the jobs. Thus the Nextflow process must run until the pipeline is finished. We recommend that you put the process running in the background through `screen` / `tmux` or similar tool. Alternatively you can run nextflow within a cluster job submitted your job scheduler.

It is recommended to limit the Nextflow Java virtual machines memory. We recommend adding the following line to your environment (typically in `~/.bashrc` or `~./bash_profile`):

```bash
NXF_OPTS='-Xms1g -Xmx4g'
```

## Running the pipeline
A typical command for running the pipeline is as follows:
```bash
nextflow run TrisKast/Cas_mediated_enrichment_read_mapping_pipeline --reads path/to/reads.fastq --reference path/to/ref.fasta --targets path/to/target.bed -profile galaxy -name experiment_name
```

This will launch the pipeline with the `galaxy` configuration profile. See below for more information about profiles.

Note that the pipeline will create the following files in your working directory:

```bash
work            # Directory containing the nextflow working files
pipeline_info   # Information about the pipeline run
RawData         # Local copy of the reads
ReferenceData   # Local copy of the reference and target file
Analysis        # Folder with computed files during the run
report.Rmd      # Report to summarize the evaluation of the experiment
Static          # Local copy of files needed to render the report
config.yaml     # Config file for the report
.nextflow_log   # Log file from Nextflow
# Other nextflow hidden files, eg. history of pipeline runs and old logs.
```

It is recommended to create a folder for each pipeline run and run the pipeline from inside this folder.

### Updating the pipeline
When you run the above command, Nextflow automatically pulls the pipeline code from GitHub and stores it as a cached version. When running the pipeline after this, it will always use the cached version if available - even if the pipeline has been updated since. To make sure that you're running the latest version of the pipeline, make sure that you regularly update the cached version of the pipeline:

```bash
nextflow pull TrisKast/Cas_mediated_enrichment_read_mapping_pipeline
```


## Main Arguments

### `-profile`
Use this parameter to choose a configuration profile. Each profile is designed for a different compute environment - follow the links below to see instructions for running on that system. Available profiles are:

* `docker`
    * A generic configuration profile to be used with [Docker](http://docker.com/)
    * Runs using the `local` executor and pulls software from dockerhub:[`Cas_mediated_enrichment_read_mapping_pipeline`](https://hub.docker.com/r/tristankast/cas_pipeline)
* `galaxy`
    * Runs the pipeline with optimized settings for one of our local machines
* `none`
    * No configuration at all. Useful if you want to build your own config from scratch and want to avoid loading in the default `base` config profile (not recommended).

### `--reads`
Use this to specify the location of your Nanopore reads in fastq format. For example:

```bash
--reads path/to/reads.fastq
```

### `--reference`
Use this flag to indicate the path to your reference genome file in fasta format.

```bash
--reference path/to/reference.fasta
```

### `--targets`
Use this flag to specify the path to your target BED file, containing the genomic location of the enriched region.

```bash
--targets path/to/targets.bed
```

### `--gstride`
Set the bin size for summarizing depth of coverage across the reference genome.
Default: 80

```bash
--gstride 80
```

### `--target_proximity`
Define the distance up- and down-stream of ontarget BED for defining target proximal mapping.
Default: 5000
```bash
--target_proximity 5000
```

### `--offTarget`
Set a threshold for defining off-target mapping.
Default: 20
```bash
--offTarget 20
```

## Job Resources
### Automatic resubmission
Each step in the pipeline has a default set of requirements for number of CPUs, memory and time. For most of the steps in the pipeline, if the job exits with an error code of `143` (exceeded requested resources) it will automatically resubmit with higher requests (2 x original, then 3 x original). If it still fails after three times then the pipeline is stopped.

### Custom resource requests
Wherever process-specific requirements are set in the pipeline, the default value can be changed by creating a custom config file. See the files in [`conf`](../conf) for examples.

### `--outdir`
The output directory where the results will be saved.

### `-name`
Name for the pipeline run. If not specified, Nextflow will automatically generate a random mnemonic.

**NB:** Single hyphen (core Nextflow option)

### `-resume`
Specify this when restarting a pipeline. Nextflow will used cached results from any pipeline steps where the inputs are the same, continuing from where it got to previously.

You can also supply a run name to resume a specific run: `-resume [run-name]`. Use the `nextflow log` command to show previous run names.

**NB:** Single hyphen (core Nextflow option)

### `-c`
Specify the path to a specific config file (this is a core NextFlow command).


### `--max_memory`
Use to set a top-limit for the default memory requirement for each process.
Should be a string in the format integer-unit. eg. `--max_memory '8.GB'``

### `--max_time`
Use to set a top-limit for the default time requirement for each process.
Should be a string in the format integer-unit. eg. `--max_time '2.h'`

### `--max_cpus`
Use to set a top-limit for the default CPU requirement for each process.
Should be a string in the format integer-unit. eg. `--max_cpus 1`
