# Lab 2: Analysis Notebook - PUBH 6854

## Overview
What this notebook analyzes and why

## Repo structure
- `notebooks/` — analysis notebooks (Python `.ipynb` and R `.Rmd`)
- `data/raw/` — source data or fetch script
- `rendered/` — HTML/PDF exports of each notebook
- `AI_USAGE.md` — AI assistance documentation

## How to run
Prior to creating the notebooks, in your terminal (depending on the package manager you have installed) run:
```bash
$ mamba create -n notebooks python=3.12 jupyterlab -c conda-forge
$ conda activate notebooks
$ jupyter --version
$ jupyter lab
```
to create an environment within your desired directory to run Jupyter notebooks. 

Then from RStudio withing your desired directory: 
```r
renv::init()
install.packages("rmarkdown")
renv::snapshot
```
If the above prompts you saying `The lockfile is already up to date.` despite having not set anything up yet, run:
```r
dir.create("src") # Create a scripts folder
writeLines('library(rmarkdown', "src/placeholder.R") # Placeholder script
renv::snapshot
```
CONTINUE WITH HOW TO RUN THE ACTUAL NOTEBOOKS EACH

## Dataset
Framingham Heart Study (teaching subset) - epidemiology / population health track. 
4,240 participants, 15 predictor variables, 10-year coronary heart disease (CHD) outcome. Loaded directly from its original source at runtime (not local copy). See `data/raw/lab2-epi-framingham/SOURCE.md` for the exact URL and loading code. 
