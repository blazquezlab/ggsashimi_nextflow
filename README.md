# mcarazo/Sashimi pipeline parameters


## Introduction

Generation of sashimi-plots using ggsashimi

## Installation

The pipeline is written in Nextflow, a workflow manager that allows to run the pipeline in a wide variety of systems. It is configured to be run either on a SLURM-managed HPC cluster or a local machine, though it can be run on a cloud instance or using other workload managers by editing the configuration file according to [Nextflow documentation](https://www.nextflow.io/docs/latest/config.html#config-scopes).


### Installing in a local machine

In your local machine, there are two ways of running the pipeline:

1. Using EPI2ME: the easiest way for those without bioinformatics experience. It's just a graphical interface that allows you to run the pipeline. Below will be explained how to install it.
2. Using the command line: for those with bioinformatics experience. It follows the same procedure as running in a cluster, so it will be explained in the `Installing in a cluster` section.

#### Installing EPI2ME

[Install EPI2ME](https://labs.epi2me.io/installation/) on your system and follow the instructions on the app to install all the dependencies (Java, Docker and Nextflow). To add the pipeline to your saved workflows simply copy this repository's URL and paste it on the "Add workflow" section of the EPI2ME interface.

### Installing in a cluster

If using Nextflow/nf-core, clone the repository and install the basic dependencies (Nextflow). The easiest way to do so is using conda. The pipeline can be run on any system that supports Docker or Singularity. If using Windows, we recommend using the Windows Subsystem for Linux (WSL).

```bash
git clone https://github.com/a-hr/plasmidsaurus_nextflow.git
```

The internal dependencies of the pipeline are managed by Nextflow, so you don't need to worry about them. If for some reason Nextflow fails to download them when using Singularity (they are provided as Docker containers), you can manually download them with the Makefile:

```bash
# make sure you have Singularity/Apptainer installed and available.
make pull
```

The pipeline is especially tailored to be run on a HPC cluster, though it can seamlessly be run on a local machine and, with some configuration, on a cloud instance.

## Usage

### Running with EPI2ME

1. Open EPI2ME and go to the "Workflows" tab.
2. Select the workflow.
3. Fill in the parameters.
4. In the `profile` section, type in `local_docker`.
5. Run the pipeline.

### Running with the command line

1. Go to the directory where you cloned the repository.
2. Fill in the parameters in the `input_params.yaml` file.
3. Make sure your system has Docker/Singularity/Apptainer and Nextflow available.
4. Edit launch.sh file dependending on the HPC where you are launching the pipeline
   ```bash
      nextflow run main.nf -resume -profile cluster -params-file input_params.yaml # for ATLAS CLUSTER
      nextflow run main.nf -resume -profile cluster_apptainer -params-file input_params.yaml # for HYPERION
      nextflow run main.nf -resume -profile local_apptainer -params-file input_params.yaml # for HYPERION WHEN LAUNCHING IT INSIDE A NODE
   ```
6. Run the pipeline in the cluster with the following command:

```bash
sbatch launch.sh
```

***If you are running the pipeline in a local machine, you can run it with the following command:***

```bash
nextflow run main.nf -profile local_docker -params-file input_params.yaml
# or with Singularity
nextflow run main.nf -profile local_singularity -params-file input_params.yaml
```

## Parameters

### Run options



| Parameter | Description | Type | Default | Required | Hidden |
|-----------|-----------|-----------|-----------|-----------|-----------|
| `run_name` | name of the experiment (files will be named after it) | `string` | plasmidsaurus-mar24 |  |  |
| `output_dir` |  | `string` | /Users/varo/Desktop/pipe_plasmidsaurus/plasmidsaurus_pipeline/output-plasmidsaurus-mar24 |  | True |
| `get_sashimis` | whether to generate sashimi plots | `boolean` | True |  |  |

### Input parameters



| Parameter | Description | Type | Default | Required | Hidden |
|-----------|-----------|-----------|-----------|-----------|-----------|
| `input_bam` | path to the directory containing the BAM files to plot | `string` | /Users/varo/Desktop/pipe_plasmidsaurus/plasmidsaurus_pipeline/inputs/bams | True |  |
| `plots_config` | path to the CSV file containing plot configuration. <details><summary>Help</summary><small>The semicolon (;) separated file should have the following fields:<br>- plotID <int>: the ID that groups twogether the BAMs of the plot. Can be repeated as many times as necessary.<br>- coords <str>: the coordinates that will be used in the plot. Format: chr:start-end  <br>- fastqName: the name, without extension, of the file to include in the plot. Can be used in more than one plot.<br>- groupName: the group the file belongs to (e.g WT, KO...). Groups together different files inside a specific plot.</small></details>| `string` | /Users/varo/Desktop/pipe_plasmidsaurus/plasmidsaurus_pipeline/inputs/plots.csv | True |  |

### QC options



| Parameter | Description | Type | Default | Required | Hidden |
|-----------|-----------|-----------|-----------|-----------|-----------|
| `min_len` | minimum length a read should have in order to be processed | `integer` | 200 |  |  |
| `max_len` | maximum length a read should have in order to be processed | `integer` | 5000 |  |  |

### Plot options

ggsashimi's internal options

| Parameter | Description | Type | Default | Required | Hidden |
|-----------|-----------|-----------|-----------|-----------|-----------|
| `sashimi_min_cov` | minimum coverage of an event in order to be included | `integer` | 5 |  |  |
| `sashimi_alpha` | alpha to apply on the coverage colour | `number` | 0.6 |  |  |
| `sashimi_collapse_groups` | collapse the files by group | `boolean` |  |  |  |
| `sashimi_shrink` | shrink the intronic regions without coverage | `boolean` |  |  |  |
| `sashimi_fix_scale` | set the same Y-axis scale to all the groups/files | `boolean` | True |  |  |
| `sashimi_annot_height` | height of the annotations in the transcript track | `integer` | 5 |  |  |
| `sashimi_width` | width in cm of the output plot | `integer` | 15 |  |  |
| `sashimi_gtf_annotations` | whether to show reference annotations in sashimi plot (only if exons are annotated) | `boolean` | False |  |  |
| `ref_gtf` | path to the reference GTF file to include transcript annotations in sashimis (if sashimi_gtf_annotations == True) | `string` | reference.gtf |  |  |
