# Lab 2: Analysis Notebook - PUBH 6854

## Overview
This notebook analyzes the Framingham Heart Study (teaching subset) using a logistic regression and developing an odds ratio and plot to determine the most significant risk factors for CHD and which ones contribute the most.  

## Repo structure
- `notebooks/` — analysis notebooks (Python `.ipynb` and R `.Rmd`)
- `data/raw/` — source data or fetch script
- `rendered/` — HTML/PDF exports of each notebook
- `AI_USAGE.md` — AI assistance documentation
- 'src/' - Placeholder script for renv
- `NOTEBOOK_COMPARISON.md` - Workflow comparison
- `README.md` - Overview, instructions, data information.

## How to run
### Clone the repository from GitHub
```bash
git clone https://github.com/nlinares-GWU2026/lab-2-hds.git
cd lab-2-hds
```
### Python Notebook (`notebooks/lab2_analysis.ipynb`)

One-time environment set up within your desired directory:
```bash
mamba create -n notebooks python=3.12 jupyterlab pandas numpy statsmodels matplotlib -c conda-forge
conda activate notebooks
```
To run: 
1. From the repository root, launch `jupyter lab`
2. Open `notebooks/lab2_analysis.ipynb
3. **Kernel -> Restart Kernel and Run All Cells.**

To reproduce the rendered export:
```bash
jupyter nbconvert --to html --execute notebooks/lab2_analysis.ipynb --output-dir=rendered --output=lab2_analysis_python.html
```

### R Notebook (`notebooks/lab2_analysis.Rmd`)

To run:
1. Open the project via **`lab-2-hds.Rproj`** in RStudio (opening the bare folder with not activate `renv` correctly).
2. If prompted, run `renv::restore()` in the console to install the exact package versions recorded in the `renv.lock`.
3. Open `notebooks/lab2_analysis.Rmd`
4. Click **Knit**, or reproduce the exact rendered export with:
5. 
```r
rmarkdown::render("notebooks/lab2_analysis.Rmd", output_dir = "rendered", output_file = "lab2_analysis_r.html")
```

## Dataset
Framingham Heart Study (teaching subset) - epidemiology / population health track. 
4,240 participants, 15 predictor variables, 10-year coronary heart disease (CHD) outcome. Loaded directly from its original source at runtime (not local copy). See `data/raw/lab2-epi-framingham/SOURCE.md` for the exact URL and loading code. 
