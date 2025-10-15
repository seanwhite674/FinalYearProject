# Gaussian Process Regression for Gravitational Waves
This is a brief summary of my Undergraduate Thesis. The paper is available to read in the repository above.

## Explaining This Repository:
"├── 1D_ToyProblem/
│   └── Introductory GPR examples (learning fundamentals)
│
├── Final_Model_Code/
│   ├── Training_CrossValidated_32GPRs/   → 32 GPR models trained using 10-fold cross-validation (on 90% of data)
│   ├── Ranking_All32_Models/              → Ranks models based on cross-validation metrics and selects the top 8
│   └── Testing_Best8Models/               → Evaluates the 8 best models on the unseen 10% test set"


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
- The below shows all models ranked indexed by there position in the table below it.
  
<img width="1627" height="491" alt="image" src="https://github.com/user-attachments/assets/3075ca6d-66d0-431b-869d-f74a225ba2d4" />
<img width="782" height="745" alt="image" src="https://github.com/user-attachments/assets/61020a7c-f916-45d2-aa0a-3a1012f0408f" />




### 3. Final Model  
The best-performing model was a **heteroscedastic additive GPR** with:

`k(x, x') = σ²_f₁ · k_RBF(x, x') + σ²_f₂ · k_Matern(x, x')`

- The RBF kernel captured global smooth structure.  
- The Matern kernel captured local, noise-like variations.  

<img width="2390" height="1990" alt="image" src="https://github.com/user-attachments/assets/416c2171-48c8-4a0d-a547-1955975a9cd3" />


### 4. Uncertainty Quantification  
- Used **MCMC** to build a posterior distribution over kernel hyperparameters.  
- Found the **signal variance** and **Matern length scales** carried most uncertainty — indicating spin parameters dominated mismatch variation.  
- Compared **pointwise predictions** (MAP estimates) with **marginalised posteriors** (integrated over hyperparameter uncertainty).  
  - Marginalisation widened credible intervals but didn’t significantly improve accuracy.  
  - The **pointwise RBF–Matern model** was chosen for efficiency.

- The below Graph shows the uncertainty associated with each hyper-parameter. A single tall peak indicates less uncertainty. A wider peak or two peaks indicates much more uncertainty around the optimal hyperparameters. 
 
<img width="1431" height="713" alt="image" src="https://github.com/user-attachments/assets/431d3830-4cd0-48fc-a7f6-1b5eea9e459d" />



