# Matlab Multimodal Log-Normal Fitting of Particle Size Distributions

A MATLAB tool for fitting multimodal aerosol particle size distributions (PSDs) using log-normal functions.

The project estimates modal particle concentrations, geometric mean diameters, and geometric standard deviations. It also reconstructs the combined particle size distribution, evaluates fitting quality, and exports the results for further analysis.

## Features

- Fit multiple log-normal modes independently.
- Support ultrafine, fine, and coarse particle populations.
- Configure initial guesses, parameter bounds, and fitting ranges.
- Process multiple experimental datasets.
- Estimate 95% confidence intervals for fitted parameters.
- Reconstruct the combined particle size distribution.
- Calculate the coefficient of determination (R²) and root mean square error (RMSE).
- Generate publication-oriented logarithmic plots.
- Export fitting results as CSV, PNG, and MAT files.

## Requirements

**Software:** MATLAB with the Optimization Toolbox and Statistics and Machine Learning Toolbox.

The implementation uses `lsqcurvefit`, `nlparci`, `readmatrix`, and `exportgraphics`. MATLAB R2020a or newer is recommended.

## Installation

1. Clone or download this repository.
2. Open the project folder in MATLAB.
3. Add your particle size distribution measurements to the data directory, or configure paths to your existing data files.
4. Modify the input file list and fitting configuration as needed.
5. Run `fitParticleSizeDistribution` from the MATLAB Command Window.

No separate installation procedure is required.

## Input data format

Each input file must contain at least two numerical columns:

| Column | Description |
|---|---|
| 1 | Particle diameter, Dg (µm) |
| 2 | Measured particle size distribution |

Example:

| Dg (µm) | dN/dlog Dg |
|---|---|
| 0.010 | 250 |
| 0.015 | 1200 |
| 0.020 | 3500 |
| 0.050 | 1800 |
| 0.100 | 12000 |
| 0.500 | 3000 |
| 1.000 | 450 |
| 5.000 | 80 |

*These values are illustrative and are not an experimental dataset.*

The program removes invalid observations, retains finite nonnegative concentration values and positive diameters, and sorts observations by diameter.

## Mathematical model

The particle size distribution is represented as a sum of log-normal components.

For one component:

**f(Dg) = N / [√(2π) ln(σg)] × exp{−[ln(Dg) − ln(Dgm)]² / [2 ln²(σg)]}**

Where:

- **N** is the modal amplitude parameter.
- **Dgm** is the geometric mean diameter.
- **σg** is the geometric standard deviation.
- **Dg** is the particle diameter.

The combined multimodal distribution is:

**f_total(Dg) = Σ f_i(Dg)**

The implementation uses natural logarithms internally.

**Normalization note:** For data defined as dN/dln(Dg), the model's N parameter corresponds to the integrated modal number concentration. For data defined as dN/dlog10(Dg), conversion of the normalization is required before interpreting N as the integrated number concentration.

## Fitting methodology

Each log-normal mode is fitted independently over a configurable diameter interval using nonlinear least squares.

Optimization is performed using MATLAB's `lsqcurvefit`.

For numerical stability, the parameters N and Dgm are optimized in logarithmic space, while σg is optimized directly.

The fitted parameters are converted back into physical units before results are exported.

Confidence intervals are estimated using the fitting residuals and Jacobian matrix. These are approximate nonlinear least-squares confidence intervals and depend on model assumptions and parameter identifiability.

## Fitting modes

The default configuration contains four modes:

| Mode | Fitting interval (µm) |
|---|---|
| Ultrafine | 0.01–0.05 |
| Fine | 0.05–0.50 |
| Coarse 1 | 0.50–2.00 |
| Coarse 2 | 2.00–20.00 |

These intervals are configurable and should be adapted to the experimental particle size distribution.

The mode names are descriptive identifiers and are not intended to define universal aerosol size-class boundaries.

## Model evaluation

The quality of the combined fit is evaluated using two metrics.

**Coefficient of determination (R²):**

R² = 1 − SSres/SStot

where SSres is the residual sum of squares and SStot is the total sum of squares.

**Root mean square error (RMSE):**

RMSE = √[(1/n) Σ(y_observed − y_predicted)²]

A higher R² and lower RMSE generally indicate a closer fit to the measurements. RMSE uses the same units as the input distribution.

Because the modes are fitted individually, these statistics evaluate the reconstructed combined distribution rather than a globally optimized multimodal solution.

## Output files

For each successfully processed dataset, the program creates:

| Output | Description |
|---|---|
| `*_parameters.csv` | Fitted mode parameters and CI-derived uncertainties |
| `*_diagnostics.csv` | R², RMSE, number of observations, and fitted modes |
| `*_fit.png` | Logarithmic plot of measurements and fitted modes |
| `*_fit.mat` | MATLAB variables containing fitted results and model curves |

Files are saved in the `results/` directory.

The parameter CSV reports N, Dgm, and σg, together with half-widths of the transformed confidence intervals. For log-transformed parameters, intervals may be asymmetric; the MAT file preserves the confidence interval endpoints.

## Visualization

The generated figures include:

- Experimental measurements displayed as black markers.
- Individual log-normal modes represented by dashed colored curves.
- The combined distribution displayed as a solid magenta curve.
- Logarithmic diameter and concentration axes.
- A legend identifying all fitted components.
- R² and RMSE annotations.

Plots are exported at 300 DPI.

## Limitations

- Modal parameters depend on the selected fitting ranges and parameter bounds.
- Independent mode fitting does not guarantee a global optimum for the combined distribution.
- Overlapping modes may lead to uncertain or non-unique parameter estimates.
- Confidence intervals do not automatically account for systematic measurement uncertainty.
- The default fitting objective uses unweighted least squares.
- Mode amplitudes must be interpreted according to the logarithmic normalization of the input PSD.

## Potential extensions

Future improvements may include:

- Joint optimization of all modes.
- Measurement-uncertainty-weighted least squares.
- Automated peak detection and initial parameter estimation.
- Residual diagnostics and additional model-selection criteria.
- Bootstrap confidence intervals.
- Automated generation of summary reports.
- Comparison of fitted distributions from multiple measurement locations.

## Applications

This tool can support research involving atmospheric aerosols, mineral dust, combustion-generated particles, industrial emissions, and laboratory aerosol characterization.

## License

Add a license file defining the conditions under which the software may be used, modified, and distributed. The MIT License is one possible option for an open-source research project.

## Citation

If you use this software in a scientific publication, cite the associated repository and its version or release. Add an author, publication year, repository URL, and DOI when available.
