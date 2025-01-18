# BENchmarking strateGies for cross-species integrAtion of singLe-cell RNA sequencing data

BENGAL is a comprehensive Nextflow DSL2 pipeline designed for cross-species single-cell RNA-seq data integration and evaluation of integration results. This pipeline offers a streamlined and efficient approach to process, integrate, and analyze datasets from multiple species.

### Author and Maintainer
- **Original Author**: Yuyao Song (ysong@ebi.ac.uk)
- **Contributor**: Deekshith Komira (deekshithsaiteja25@gmail.com)

## Overview

This repository includes:
- The Nextflow pipeline for cross-species integration of single-cell RNA-seq data using diverse strategies.
- Container paths, Dockerfiles, and Conda environments.
- An example configuration file for executing the pipeline.

## System Requirements

### Hardware:
- Designed for execution on HPC clusters with an LSF job scheduler. It can be adapted for other schedulers by modifying job resource syntax in the Nextflow configuration file.
- For GPU implementations of scVI/scANVI (to speed up large dataset integrations), GPU nodes like Nvidia Tesla V100 are required. Refer to the scVI-tools documentation for specific GPU setup guidelines.

### Operating System:
- Developed on Rocky Linux 8.5 (RHEL). It should work on most Linux distributions.
- GPU implementation of scVI/scANVI was tested on Nvidia Tesla V100 GPUs.

## Installation

### Clone the Repository
```bash
git clone -b main git@github.com:Functional-Genomics/BENGAL.git
```

### Prerequisites
If Nextflow or Singularity is not installed on your cluster, consult your IT administrator. Refer to the [Nextflow documentation](https://www.nextflow.io/) and [Singularity documentation](https://sylabs.io/singularity/).

## Inputs

### Required Input File
The pipeline requires a metadata file (tab-separated) mapping species to raw count AnnData objects (.h5ad files). Example: `example_metadata_nf.tsv`.

### Configuration File
The configuration file defines directories and parameters for the project. Example: `config/example.config`. Ensure that metadata paths and configuration parameters are customized for your application.

### Input Data Requirements
- **Annotations:**
  - `species_key` in `adata.obs`: Stores species identity (e.g., hsapiens, mmusculus, drerio).
  - `cluster_key` in `adata.obs`: Stores cell types (preferably Cell Ontology annotations).
  - `mean_counts` in `adata.var`: Computed using `sc.pp.calculate_qc_metrics` from Scanpy.
- Gene IDs in `.var_names` must use ENSEMBL IDs.
- Data matrices (`.X`) should be in dense format for compatibility with SeuratDisk.

## Conda Environment Setup

### Sceasy Conversion Environment
```bash
conda env create -f envs/sceasy.yml
```
Add the path to the Sceasy Conda environment in the configuration file.

### scVI/scANVI and scIB Environments
```bash
conda env create -f envs/scvi.yml
conda env create -f envs/scib.yml
```
Update the paths to these environments in the configuration file.

## Containers
To simplify execution, pre-built containers are provided:
- **Concatenation**: `singularity pull bengal_concat.sif docker://yysong123/bengal_concat:4.2.0`
- **Python Integration**: `singularity pull bengal_py.sif docker://yysong123/bengal_py:1.9.2`
- **Seurat Integration**: `singularity pull bengal_seurat.sif docker://yysong123/bengal_seurat:4.3.0`
- **SCCAF Assessment**: `singularity pull bengal_sccaf.sif docker://yysong123/bengal_sccaf:0.0.11`

## Running the Pipeline

### Example Commands
1. Activate Nextflow and run concatenation:
   ```bash
   conda activate nextflow
   nextflow -C config/example.config run concat_by_homology_multiple_species.nf
   ```

2. Perform integration:
   ```bash
   nextflow -C config/example.config run cross_species_integration_multiple_species.nf
   ```

3. Run assessment:
   ```bash
   nextflow -C config/example.config run cross_species_assessment_multiple_species_individual.nf
   ```

Add the `-resume` flag for subsequent runs to avoid re-processing.

## Outputs
- **Concatenated AnnData**: Combined `.h5ad` files with gene matching via ENSEMBL homology annotations.
- **Integration Results**: Outputs from algorithms like fastMNN, Harmony, LIGER, scVI, SeuratV4, etc.
- **Visualizations**: UMAPs, batch correction, and cell type plots.
- **Assessment Metrics**: Batch correction and biological conservation metrics, with associated plots.
- **Cell Type Annotation**: Cross-species annotation results using SCCAF.

## Estimated Runtime
Approximately 6 hours for 100,000 cells with the specified resources.

## Citation
If you use BENGAL, please cite:
Song, Y., Miao, Z., Brazma, A. et al. Benchmarking strategies for cross-species integration of single-cell RNA sequencing data. *Nat Commun* **14**, 6495 (2023). [https://doi.org/10.1038/s41467-023-41855-w](https://doi.org/10.1038/s41467-023-41855-w)

The BENGAL pipeline version used in this publication is archived in Zenodo: [DOI]

## License
This project is licensed under GPLv3. See the `LICENSE` file for details.
