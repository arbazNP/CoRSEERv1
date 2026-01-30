# CoRSEEr (MATLAB App)
**CoRSEEr** (Calculator of Rock Surface Exposure Age and Erosion Rates) is a MATLAB App for **Rock Surface Luminescence (RSL) dating**.  
It estimates **kinetic calibration parameters**, **apparent exposure age**, and **erosion histories** by forward–inverse modelling of **luminescence–depth profiles (LDPs)**.

**Correspondence:** Arbaz N. Pathan (akp20@iitk.ac.in)

---

## What CoRSEEr does 
CoRSEEr simulates luminescence signal evolution inside rock through time and depth, compares modelled LDPs with measured LDPs, and selects best solutions by **chi-square misfit** and **likelihood-based sampling**. It runs a **sigmoidal curve fit** before inversion to normalise the LDP and to stabilise the workflow.

---

## Supported platforms
- **Windows:** Windows 10/11 (64-bit) — recommended  
- **macOS:** 64-bit macOS supported by your MATLAB release

---

## Software requirements
- Developed using **MATLAB App Designer 2025a**
- Recommended: **MATLAB 2025a or newer**
- Required toolboxes:
  - Statistics and Machine Learning Toolbox
  - Curve Fitting Toolbox
  - Parallel Computing Toolbox
  - Optimization Toolbox
- **Data I/O:** Microsoft Excel should be installed (for editing/accessing input files)

---

## Hardware requirements

| Category | Minimum (basic use) | Recommended (research workflow) | High-Performance (large batches / sensitivity runs) |
|---|---:|---:|---:|
| CPU | 2 cores, ≥2.5 GHz | 4–8 cores, ≥3.0 GHz | >16 cores (parallel runs) |
| RAM | 8 GB | 16 GB | 32 GB |
| Storage | 25 GB free | SSD, 100 GB free | NVMe SSD, ≥100 GB free |
| Display | 1366×768 | 1920×1080 | 1920×1080 |
| GPU | Not required | Not required | Not required |

---

## The three modules
### CoRSEEr-I: Calibration
Uses a **known-age sample** to estimate kinetic parameters (bleaching rate term and attenuation coefficient **μ**) via a grid-based forward model + inverse selection.

### CoRSEEr-II: Apparent age estimation
Uses calibrated parameters from CoRSEEr-I to estimate the **exposure age** of an unknown sample by scanning an age range and selecting the minimum misfit solution. Also estimates **maximum datable age** using **x0.5** (depth of half-saturation) tracking.

### CoRSEEr-III: Erosion estimation
Explores erosion-rate and erosion-onset-time combinations to infer plausible **erosion histories**, producing an erosion–exposure solution landscape.

---

## Method overview
- CoRSEEr performs **sigmoidal curve fitting (SCF)** on the experimental LDP first, extracts the plateau magnitude, and **normalises** the measured signal.
- It runs a forward model solved with an **explicit Euler forward time scheme** on a depth grid.
- It evaluates misfit between modelled and experimental LDPs using **chi-square**, converts it to a **Gaussian likelihood**, normalises it, and performs **Monte Carlo acceptance** (likelihood thresholded against `rand`).

---

## Input Excel formats 
### General rules (applies to all parts)
- **Depth values must be unique and non-zero** (duplicates cause interpolation failures).  
  If duplicates are unavoidable, offset one by a tiny amount (e.g., **1e-5 mm**).
- **Lx/Tx must be non-negative and non-zero** (replace true zeros with a small placeholder like **1e-6**).
- **Lx/Tx error must be non-zero** for stable Monte Carlo / fitting behaviour.

### CoRSEEr-I (Calibration) input file
- Excel filename: **`OSL_Calib.xlsx`**
- Each sample is stored in a separate sheet named: **`XX_SAMPLENAME`**
  - `XX` = `IR50`, `pIRIR150`, or `pIRIR225`
- Required columns:
  1. Depth (mm)
  2. Lx/Tx
  3. Lx/Tx error
  4. Order of kinetics `r`
  5. Dose rate (Gy/a) *(final dose rate should follow Sohbati et al., 2018 workflow)*
  6. Characteristic dose (Gy) *(e.g., derived from DRC methods such as Murray & Wintle, 2006)*
  7. Known calibration age (years)

**Parameter bounds used in calibration :**
- Bleaching rate term: **10^-10 to 100 s^-1**
- Attenuation coefficient **μ:** **0 to 4 mm^-1** (user-editable)

### CoRSEEr-II and CoRSEEr-III (Unknown sample) input file
- Excel filename: **`OSL_unknown.xlsx`**
- Each sample is stored in a separate sheet named exactly as the **Sample Name** you enter in the GUI.
- Same column structure as above, but:
  - The **age column must be blank** for unknown samples.
  - Ensure the **order of kinetics** matches the calibration sample (CoRSEEr-I).

---

## GUI file naming and workflow expectations
- CoRSEEr-I expects `OSL_Calib.xlsx` in the selected working directory.
- CoRSEEr-II / III expect `OSL_unknown.xlsx` in the selected working directory.
- CoRSEEr-II expects you to load the calibration output file: `var_cal_[SampleName].xlsx`.

---

## Outputs
### CoRSEEr-I outputs
1. `var_cal_[SampleName].xlsx` (primary result record)
2. `.mat` file containing internal variables (full reproducibility)
3. Diagnostic plots including:
   - SCF fit to experimental LDP (with error bars)
   - Experimental vs modelled LDP (best-fit and median)
   - Likelihood / misfit surface over parameter space

### CoRSEEr-II outputs
1. `var_age_[SampleName].xlsx` (apparent age, maximum datable age, key results)
2. `.mat` file with modelling variables
3. Diagnostic plots including:
   - SCF fit plot
   - x0.5 evolution through time (for erosion scenarios)
   - Accepted-model envelope over the experimental LDP (colour-coded by normalised chi-square)

### CoRSEEr-III outputs
1. `var_Er_[SampleName].xlsx` (erosion inversion results)
2. `.mat` file with all variables
3. Diagnostic plots including:
   - SCF fit plot
   - Erosion surface plot (erosion rate vs onset time vs normalised misfit)

---

## Repository structure
- `src/` — source code (module-wise)
- `app/` — packaged MATLAB App release files (e.g., `.mlappinstall`)
- `docs/` — user manual + plot/table guide + troubleshooting
- `examples/` — example inputs + example outputs
- `tests/` — smoke tests / basic regression checks

---

## How to cite
- **Software (version DOI):**  https://doi.org/10.5281/zenodo.18434547
- **Software (concept DOI):**  https://doi.org/10.5281/zenodo.18434547

Until DOI is minted, cite the GitHub repository and the associated manuscript.

---

## License
MIT License (see `LICENSE`).

---

## Reference
Sohbati, R., et al. (2018). *Centennial- to millennial-scale hard rock erosion rates deduced from luminescence-depth profiles*. EPSL.

