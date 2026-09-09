# Lab 2 dataset - Epi & Population Health: Framingham Heart Study (teaching subset)

## Source 
Derived teaching subset (not the restricted-access NHLBI Framingham data, which requires a BioLINCC application). It circulates widely online as `framingham.csv`. The instructor provided SOURCE.md provided the URL.

Mirror: https://github.com/GauravPadawe/Framingham-Heart-Study
Raw CSV: https://github.com/GauravPadawe/Framingham-Heart-Study/blob/master/framingham.csv

## Columns 
male, age, education, currentSmoker, cigsPerDay, BPMeds, prevalentStroke, prevalentHyp, diabetes, totChol, sysBP, diaBP, BMI, heartRate, glucose, TenYearCHD (outcome)

## License/Terms of Use
Open teaching dataset. No single consistent formal license.

## PHI/PII Status
De-identified teaching subset.

## Loading
```python
import pandas as pd
url = "https://raw.githubusercontent.com/GauravPadawe/Framingham-Heart-Study/master/framingham.csv"
fram = pd.read_csv(url)
```

R:
```r
fram <- read.csv("https://raw.githubusercontent.com/GauravPadawe/Framingham-Heart-Study/master/framingham.csv")

