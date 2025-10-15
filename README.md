# Gaussian Process Regression for Gravitational Waves
This is a brief summary of my Undergraduate Thesis. The paper is available to read in the repository above.

## Overview  
This project explores how **Gaussian Process Regression (GPR)** can be used to efficiently predict **waveform mismatches** between analytical gravitational wave (GW) models and **Numerical Relativity (NR)** simulations. NR simulations are the gold standard for generating GW waveforms but are computationally costly. By training GPR models on a limited set of NR-informed mismatches, this project builds a **surrogate model** that generalizes across the binary black hole parameter space, reducing the need for new NR runs while maintaining high predictive accuracy.

---

## Previous work and Motivation
Recent Bayesian methods (“NR-informed” approaches) use waveform mismatches to determine which analytical GW model is most accurate in each region of parameter space.  
However, mismatch computation still depends on expensive NR data.  
This thesis proposes a **machine learning alternative** — a Gaussian Process model that predicts mismatches as a smooth function of binary parameters, with quantified uncertainty, allowing faster and more scalable inference.

---

## Data  
- Mismatch values between analytical model **SEOBNRv5PHM** and NR surrogate **NRSur7dq4**.  
- 250 intrinsic parameters (covering 5 mass ratios and a grid of spin projections), repeated for 4 total masses. (1000 total points) 
- Each mismatch is averaged over 294 extrinsic configurations (such as position in sky).  Again read https://arxiv.org/abs/2409.19404 for further details
- The 8D physical parameter space (two masses + two 3D spin vectors) was reduced to 4D via:  
  - **Total mass:** `M_tot = M₁ + M₂`  
  - **Symmetric mass ratio:** `η = q / (1 + q)²` 'q' is the mass ratio such that 'q = M₂/M₁' 
  - **Spin projections:** `χ∥`, `χ⊥` — components of total spin parallel and perpendicular to the orbital angular momentum

- Below we have a visualisation of the input data across reduced dimensions. Left: A 3D scatter plot of all
data points for fixed total mass w = 0.25 (Mtot = 37.5M⊙). Centre: A 2D slice of the data at rescaled
symmetric mass ratio z = 0 (q = 0.404), interpolated over spin components. Right: A 1D cut through
the data at x = 0.8 showing variation across y. When using raw data, interpolation is needed between
samples, but GPR provides an analytic model that can be directly evaluated without interpolation.

  <img width="1399" height="351" alt="image" src="https://github.com/user-attachments/assets/49a7cabe-8d73-4f81-9d87-995928046e54" />




---

## Methodology  
- Below shows a flow-chart of the process followed building and testing the models:

<img width="3413" height="1974" alt="image" src="https://github.com/user-attachments/assets/032da9bf-e92b-46be-8b92-9ceb63274527" />



### 1. Gaussian Process Framework  
- Built GP priors and posteriors for both **homoscedastic** (constant noise) and **heteroscedastic** (input-dependent noise) assumptions.  
- Explored multiple kernel types, their shapes and properties are illustrated below

<img width="1194" height="806" alt="image" src="https://github.com/user-attachments/assets/433a6654-dd43-4765-9094-3152aa4cce16" />

- Examined kernel combinations and noise models via cross-validation across six metrics:
  - RMSE, MAE, FOM, R², Adjusted R², Pearson correlation.


### 2. Model Training & Evaluation  
- Tested **32 GPR configurations** (different kernel + noise setups).  
- Used **10-fold cross-validation** on 90% of data and a 10% hold-out test set.  
- Implemented using `scikit-learn` with `fmin_l_bfgs_b` optimization and `emcee` for MCMC sampling of hyperparameters.

### 3. Final Model  
The best-performing model was a **heteroscedastic additive GPR** with:

`k(x, x') = σ²_f₁ · k_RBF(x, x') + σ²_f₂ · k_Matern(x, x')`

- The RBF kernel captured global smooth structure.  
- The Matern kernel captured local, noise-like variations.  

**Performance (on test data):**
- R² ≈ 0.99
- RMSE ≈ 0.034
- MAE ≈ 0.02

### 4. Uncertainty Quantification  
- Used **MCMC** to build a posterior distribution over kernel hyperparameters.  
- Found the **signal variance** and **Matern length scales** carried most uncertainty — indicating spin parameters dominated mismatch variation.  
- Compared **pointwise predictions** (MAP estimates) with **marginalised posteriors** (integrated over hyperparameter uncertainty).  
  - Marginalisation widened credible intervals but didn’t significantly improve accuracy.  
  - The **pointwise RBF–Matern model** was chosen for efficiency.

- The below Graph shows the uncertainty associated with each hyper-parameter. A single tall peak indicates less uncertainty. A wider peak or two peaks indicates much more uncertainty around the optimal hyperparameters.
 
<img width="1431" height="713" alt="image" src="https://github.com/user-attachments/assets/431d3830-4cd0-48fc-a7f6-1b5eea9e459d" />

  **Notes:**  
- σ²_f₁ and σ²_f₂ are the **signal variances** for each kernel — they scale the amplitude of the model.  
- “Length Scales” correspond to each input dimension in the reduced 4D parameter space. 


---

## Key Results  



### Optimized Hyperparameters for the Final 8 GPR Models

- The below graph is a cross-section taken from the best models:
- 
<img width="1719" height="1428" alt="image" src="https://github.com/user-attachments/assets/762f2326-68d6-4605-9d07-9fa2dba4f9df" />

- The table below represents the metrics for the best models:

| **Model** | **Kernel 1** | **σ²_f₁** | **Length Scales 1** | **Kernel 2** | **σ²_f₂** | **Length Scales 2 / Noise** |
|------------|--------------|------------|----------------------|--------------|------------|------------------------------|
| **RBFMat** | RBF | 2.34 | [1.00, 1.51, 1.38, 1.36] | Matern (ν = 0.75) | 0.207 | [0.0996, 0.0582, 0.414, 2.31] |
| **RBFLap** | RBF | 0.354 | [0.10, 0.10, 1.18, 2.91] | Laplace (γ = 0.964) | 0.292 | — |
| **Mat_noerr** | Matern (ν = 1.75) | 0.926 | [0.227, 0.20, 1.15, 2.85] | White | — | σ²ₙ = 0.00637 |
| **Laplace_noerr** | Laplace (γ = 0.358) | 7.24 | — | White | — | σ²ₙ = 1×10⁻⁶ |
| **RBF_noerr** | RBF | 0.728 | [0.112, 0.112, 0.958, 1.6] | White | — | σ²ₙ = 0.00728 |
| **Mat_minmaxerr** | Matern (ν = 1.75) | 1.14 | [0.27, 0.22, 1.34, 4.73] | White | — | σ²ₙ = 0.0439 |
| **Laplace_minmaxerr** | Laplace (γ = 0.284) | 6.60 | — | White | — | σ²ₙ = 0.0439 |
| **RBF_minmaxerr** | RBF | 0.821 | [0.12, 0.115, 1.19, 2.52] | White | — | σ²ₙ = 0.0439 |

**Notes:**  
- σ²ₙ is the **optimised noise hyperparameter** from the WhiteKernel.  
- “Mat” and “Lap” refer to the **Matern** and **Laplacian** kernels, respectively.

  
- The below visualisation represents how we chose the best model from the metrics. We visualised the metrics with the most different ranking results in the scatter plot.
- The models are labelled 1-8 from the ranking produced in the table above.

<img width="1269" height="425" alt="Image" src="https://github.com/user-attachments/assets/badf2824-bcfb-40f3-91b4-938f7c3ac7c1" />




---

## Tools & Libraries  
- **Python**, `scikit-learn`, `NumPy`, `SciPy`, `matplotlib`, `emcee`  
- Data visualisation using Matplotlib (contour and cross-cut plots).  
- MCMC analysis for Bayesian hyperparameter posteriors.  

---

## Further Work  
- Extend to full 8D parameter space to assess information loss from dimensionality reduction.  
- Explore **model averaging across kernel types** to capture kernel-level uncertainty.  
- Integrate the GPR model into the **NR-informed inference workflow** for live GW parameter estimation.


