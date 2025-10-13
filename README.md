# Gaussian Process Regression for Gravitational Waves

## Overview  
This project explores how **Gaussian Process Regression (GPR)** can be used to efficiently predict **waveform mismatches** between analytical gravitational wave (GW) models and high-fidelity **Numerical Relativity (NR)** simulations.  
NR simulations are the gold standard for generating GW waveforms but are computationally costly. By training GPR models on a limited set of NR-informed mismatches, this project builds a **surrogate model** that generalizes across the binary black hole parameter space — reducing the need for new NR runs while maintaining high predictive accuracy.

---

## Motivation  
Recent Bayesian methods (“NR-informed” approaches) use waveform mismatches to determine which analytical GW model is most accurate in each region of parameter space.  
However, mismatch computation still depends on expensive NR data.  
This thesis proposes a **machine learning alternative** — a Gaussian Process model that predicts mismatches as a smooth function of binary parameters, with quantified uncertainty, allowing faster and more scalable inference.

---

## Data  
- Mismatch values between analytical model **SEOBNRv5PHM** and NR surrogate **NRSur7dq4**.  
- 250 intrinsic configurations (covering 5 mass ratios and a grid of spin projections), repeated for 4 total masses.  
- Each mismatch averaged over 294 extrinsic configurations.  
- The 8D physical parameter space (two masses + two spin vectors) was reduced to 4D via:  
  - **Total mass:** `M_tot = M₁ + M₂`  
  - **Symmetric mass ratio:** `η = q / (1 + q)²`  
  - **Spin projections:** `χ∥`, `χ⊥` — components of total spin parallel and perpendicular to the orbital angular momentum
 
  <img width="1399" height="351" alt="image" src="https://github.com/user-attachments/assets/49a7cabe-8d73-4f81-9d87-995928046e54" />


---

## Methodology  

<img width="3413" height="1974" alt="image" src="https://github.com/user-attachments/assets/032da9bf-e92b-46be-8b92-9ceb63274527" />



### 1. Gaussian Process Framework  
- Built GP priors and posteriors for both **homoscedastic** (constant noise) and **heteroscedastic** (input-dependent noise) assumptions.  
- Explored multiple kernel types:
  - Radial Basis Function (RBF)
  - Matern
  - Rational Quadratic
  - Laplacian
  - Periodic
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
 
<img width="1431" height="713" alt="image" src="https://github.com/user-attachments/assets/431d3830-4cd0-48fc-a7f6-1b5eea9e459d" />


---

## Key Results  

<img width="1269" height="425" alt="Image" src="https://github.com/user-attachments/assets/badf2824-bcfb-40f3-91b4-938f7c3ac7c1" />




---

## Tools & Libraries  
- **Python**, `scikit-learn`, `NumPy`, `SciPy`, `matplotlib`, `emcee`  
- Data visualisation using Matplotlib (contour and cross-cut plots).  
- MCMC analysis for Bayesian hyperparameter posteriors.  

---

## Outcome  
This project demonstrates that **Gaussian Processes can act as an accurate surrogate model** for waveform mismatch prediction in gravitational wave physics.  
The RBF–Matern hybrid model offers a strong balance between **smooth extrapolation**, **local adaptability**, and **computational efficiency**, improving the **NR-informed Bayesian parameter estimation pipeline**.

---

## Further Work  
- Extend to full 8D parameter space to assess information loss from dimensionality reduction.  
- Explore **model averaging across kernel types** to capture kernel-level uncertainty.  
- Integrate the GPR model into the **NR-informed inference workflow** for live GW parameter estimation.


