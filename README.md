# metabolomics_nightingale

This repo follows the structure of the [Nightingale Health NMR Data Analysis Tutorial](https://nightingalehealth.github.io/ggforestplot/articles/nmr-data-analysis-tutorial.html), applying the same analytical approach to publicly available data from [NHANES 2017-2018](https://wwwn.cdc.gov/nchs/nhanes/continuousnhanes/default.aspx?BeginYear=2017).

The analysis covers: data download, preprocessing, linear regression of biomarkers against BMI, Benjamini-Hochberg FDR correction, forest plot visualisation, and logistic regression for type 2 diabetes.

## Requirements

- [conda](https://docs.conda.io/en/latest/miniconda.html)

## Setup

Create and activate the conda environment:

```
conda env create -f environment.yaml
conda activate metabolomics_nightingale
```

## Running the notebook

```
jupyter lab
```

Then open `analysis.ipynb` and select the **R** kernel. Run the setup cell first. Note that it will install `nhanesA` and `ggforestplot` on first run (so it might take a couple of minutes).


