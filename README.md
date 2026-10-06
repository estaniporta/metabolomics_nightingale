# metabolomics_nightingale

## Question

Which standard clinical biomarkers are associated with BMI, and which independently predict type 2 diabetes, when the same analytical workflow used for NMR metabolomics is applied to a publicly available clinical dataset?

## Data

**Source:** [NHANES 2017-2018](https://wwwn.cdc.gov/nchs/nhanes/continuousnhanes/default.aspx?BeginYear=2017) (National Health and Nutrition Examination Survey), U.S. Centers for Disease Control and Prevention. Public-use files, no registration required.

**Retrieved:** June 2025 via the [nhanesA](https://cran.r-project.org/package=nhanesA) R package, which downloads directly from CDC servers.

**Modules used:** Demographics (DEMO_J), body measures (BMX_J), fasting glucose (GLU_J), HbA1c (GHB_J), total cholesterol (TCHOL_J), HDL (HDL_J), LDL and triglycerides (TRIGLY_J), hsCRP (HSCRP_J), diabetes questionnaire (DIQ_J).

**Size:** 9,254 participants at download; 2,738 after complete-case filtering (see Limitations).

**Note on scope:** NHANES provides standard clinical biomarkers (HbA1c, total cholesterol, HDL, LDL, triglycerides, hsCRP, glucose). It does not include NMR metabolomics. This repo applies the [Nightingale Health NMR tutorial](https://nightingalehealth.github.io/ggforestplot/articles/nmr-data-analysis-tutorial.html) workflow as a methods exercise on a comparable dataset; it does not replicate Nightingale's NMR panel.

## Method

Each biomarker is log-transformed and z-standardised (mean 0, SD 1) before modelling, so regression estimates are comparable across biomarkers with different measurement scales.

**Linear regression (BMI associations):** one model per biomarker, `biomarker ~ BMI + age + sex`. Multiple testing corrected with Benjamini-Hochberg (BH) FDR across 7 tests.

**Logistic regression (type 2 diabetes):** one model per biomarker, `diabetes ~ biomarker + age + sex + BMI`. Diabetes is coded as binary: "Yes" and "Borderline" = 1, "No" = 0. BH correction applied across 7 tests.

## Results

**BMI associations (linear regression, n = 2,738):**

6 of 7 biomarkers are significantly associated with BMI after BH correction. Only total cholesterol is not (BH-adjusted p = 0.27). hsCRP shows the strongest association (estimate: 0.064 SD per BMI unit, p = 3.7e-161). HDL is inversely associated (higher BMI, lower good cholesterol).

![Forest plot: biomarker associations with BMI](docs/forest_plot_bmi.png)

**Type 2 diabetes prediction (logistic regression, n = 2,738):**

HbA1c (log-odds 1.91) and glucose (log-odds 1.33) are the strongest predictors, as expected given their direct relationship to blood sugar. hsCRP, despite being the dominant BMI-associated biomarker, is not significant after adjusting for BMI (p = 0.19), suggesting its association with diabetes is mediated by obesity.

![Forest plot: biomarker associations with type 2 diabetes](docs/forest_plot_diabetes.png)

## How to reproduce

**Requirements:** [conda](https://docs.conda.io/en/latest/miniconda.html)

```bash
git clone <repo>
cd metabolomics_nightingale
conda env create -f environment.yaml
conda activate metabolomics_nightingale
jupyter lab
```

Open `analysis.ipynb` and select the **R** kernel. Run the setup cell first; it installs `nhanesA` and `ggforestplot` on first run (takes a few minutes). Then run all cells in order.

## Limitations

- **Survey design not accounted for.** NHANES uses a complex stratified sample with participant weights designed to make estimates nationally representative. This analysis uses plain `lm()` and `glm()` without the `survey` package. Results describe the analytic sample, not the U.S. population.

- **Complete-case analysis drops 70% of participants.** Glucose, LDL, and triglycerides were only collected from a fasting subsample of NHANES by design, resulting in ~70% missing values for those biomarkers. Any participant missing any biomarker, covariate, or outcome is excluded (n drops from 9,254 to 2,738). The fasting subsample is not a random subset of NHANES, so this may introduce selection bias.

- **"Borderline" diabetes recoded as diabetic.** The diabetes outcome combines "Yes" and "Borderline" responses into a single positive class. This inflates the positive case rate relative to a strict clinical definition.

- **No BMI adjustment in the linear models.** The linear regression treats BMI as the predictor of interest, not a covariate. The logistic regression adjusts for BMI alongside each biomarker.

- **n = 2,738 for all models.** The same analytic sample is used for all 7 linear and all 7 logistic models.

- **Open questions:** Was the "Borderline" recode consistent with the original Nightingale tutorial protocol? Would applying survey weights materially change the results?

## License

Code: [MIT](LICENSE). Data: downloaded from CDC NHANES public-use files (no licence restrictions on use).
