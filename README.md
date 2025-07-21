# BDL-experiments

This repository contains the nextflow run configurations for all experiments run for presentation in the Bayesian Deep Learning textbook.

## Overview
There are two main steps to this workflow. The first is to run the `TUNE_BETA` subworkflow in order to pick the best value of &beta; for imputation using the &beta;-VAEs worflow. This will create a set of plots that can be assessed by the user to pick the optimal value of &beta; that balances empirical coverage (EC) and mean absolute error (MAE). The second is to run multiple imputation using this chosen value of &beta; to impute M datasets that will then be assessed for coverage via EC, accuracy via MAE and impact on downstream differential gene expression results using LASSO regression. In parallel, single imputation using VAEs will also be run, as well as mean imputation to benchmark our results against.

The &beta;-VAEs repository can be found here: https://github.com/roskamsh/BetaVAEMultImpute and contains the nextflow pipeline and base code for this analysis.

## Setup
This code can be easily parallelized on the University of Edinburgh cluster, Eddie. In order to run this analysis, you must have `nextflow` and `singularity` installed prior. This can easily be done using the package manager `conda`. To install `conda`, follow the instructions here: https://www.anaconda.com/docs/getting-started/miniconda/install#linux-terminal-installer. Once installed, you can create the required environment using:

```
conda install -n nextflow conda-forge::singularity=3.7.1 bioconda::nextflow=24.10.0
```

Make sure to activate your conda environment prior to running nextflow with:

```
conda activate nextflow
```

## Tuning &beta;

To tune beta, run the nextflow subworkflow as follows within the data directory containing the missing experiment you're interested in (ex: `LGGGBM/missing_5perc`):

```
nextflow run https://github.com/roskamsh/BetaVAEMultImpute -c LGGGBM_missing_5perc.config -profile eddie -entry TUNE_BETA -resume
```

Output plots to pick optimal &beta; will be in the output directory `LGGGBM/missing_5perc/output/tune_beta` 

## Imputation

To run imputation & downstream tasks, run the following command from the same data directory:

```
nextflow run https://github.com/roskamsh/BetaVAEMultImpute -c LGGGBM_missing_5perc.config -profile eddie -resume
```

Final results will be in the output directory `LGGGBM/missing_5perc/output/`.